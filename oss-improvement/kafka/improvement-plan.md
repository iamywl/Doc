# Apache Kafka 개선 계획서

> 작성일: 2026-03-13
> 대상 버전: Apache Kafka 4.x (KRaft 모드)
> 분석 기반: Kafka 내부 아키텍처 교육 자료 (01~26장) 및 PoC 코드 분석

---

## 1. 프로젝트 개요

Apache Kafka는 분산 이벤트 스트리밍 플랫폼으로, KRaft 합의 프로토콜 기반의 메타데이터 관리, 추가 전용(append-only) 로그 스토리지 엔진, NIO 기반 네트워크 계층, ISR 복제 시스템 등으로 구성된다. Kafka 4.0부터 ZooKeeper 의존성을 완전히 제거하고 KRaft 모드로 전환하였다.

본 문서는 Kafka의 핵심 컴포넌트별 구조적 한계점을 분석하고, 각 이슈에 대한 구체적인 개선 방안을 제안한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 파티션 단위 synchronized 잠금으로 인한 쓰기 경합

**위치**: `storage/src/main/java/org/apache/kafka/storage/internals/log/UnifiedLog.java` (append 메서드)

`UnifiedLog.append()` 내부에서 `synchronized(lock)` 블록을 사용하여 파티션 단위로 쓰기를 직렬화한다. 이 잠금은 오프셋 할당, 레코드 검증, 프로듀서 상태 업데이트, 실제 로그 기록, HW 업데이트를 모두 포함한다. 단일 파티션에 다수의 프로듀서가 동시에 쓸 때, 모든 요청 핸들러 스레드(`KafkaRequestHandlerPool`, 기본 `num.io.threads=8`)가 이 잠금에서 경합한다.

특히 `analyzeAndValidateProducerState()`에서 멱등성/트랜잭션 검증이 수행되고, `LogValidator.validateMessagesAndAssignOffsets()`에서 각 레코드 배치의 CRC 검증과 오프셋 재할당이 일어나는데, 이 두 작업이 잠금 범위 안에 있어 잠금 보유 시간이 길어진다.

### 2.2 SkimpyOffsetMap의 MD5 해싱과 선형 탐사로 인한 로그 컴팩션 지연

**위치**: `storage/src/main/java/org/apache/kafka/storage/internals/log/SkimpyOffsetMap.java`, `Cleaner.java`

로그 컴팩션에서 키-오프셋 매핑에 사용되는 `SkimpyOffsetMap`은 MD5 해시(16바이트) + 오프셋(8바이트) = 24바이트/엔트리 구조의 오픈 어드레싱 해시 테이블이다. 기본 128MB 버퍼로 약 560만 키를 추적할 수 있지만, 다음과 같은 병목이 존재한다:

- **MD5 해시 계산 비용**: 컴팩션 시 더티 영역의 모든 레코드 키에 대해 `MessageDigest.digest()`를 호출한다. MD5는 암호학적 해시이므로 컴팩션 전용으로는 과도한 비용이다.
- **선형 탐사(linear probing)의 클러스터링**: 로드 팩터가 높아질수록 탐사 거리가 급격히 증가하여 캐시 효율이 떨어진다. 560만 키에 가까워지면 `put/get` 연산의 평균 탐사 거리가 수십 슬롯으로 늘어난다.
- **단일 스레드 처리**: `Cleaner` 스레드가 더티 영역 스캔(OffsetMap 구축) -> 클린 세그먼트 재작성 -> 더티 세그먼트 재작성을 순차적으로 수행하여, 대용량 토픽의 컴팩션이 수 시간 소요될 수 있다.

### 2.3 Classic 그룹 프로토콜의 Stop-the-World 리밸런스

**위치**: `group-coordinator/src/main/java/org/apache/kafka/coordinator/group/GroupMetadataManager.java` (classicGroupJoin 메서드), `ClassicGroup.java`

Classic 그룹 프로토콜은 5단계 상태 머신(EMPTY -> PREPARING_REBALANCE -> COMPLETING_REBALANCE -> STABLE -> DEAD)을 따르며, 리밸런스 시 다음과 같은 문제가 발생한다:

- **전체 정지(Stop-the-World)**: `PREPARING_REBALANCE` 진입 시 모든 컨슈머의 파티션 할당이 해제된다. 새 할당이 `STABLE` 상태로 전환될 때까지 전체 그룹이 메시지 소비를 중단한다. `rebalance.timeout.ms`(기본 300초) 동안 모든 멤버의 JoinGroup 응답이 보류(park)된다.
- **클라이언트 측 할당의 불안정성**: 리더 컨슈머가 `SyncGroup`에서 파티션 할당을 제출해야 하므로, 리더 장애 시 전체 리밸런스가 재시작된다.
- **Heartbeat/poll() 결합**: `ClassicKafkaConsumer`에서 `poll()` 호출 지연이 하트비트 지연으로 직결되어, 처리 시간이 긴 메시지에서 불필요한 리밸런스가 빈번히 발생한다.

### 2.4 KRaft 메타데이터 전파의 지연과 단일 파티션 병목

**위치**: `raft/src/main/java/org/apache/kafka/raft/KafkaRaftClient.java`, `metadata/src/main/java/org/apache/kafka/controller/QuorumController.java`, `metadata/src/main/java/org/apache/kafka/image/loader/MetadataLoader.java`

KRaft 아키텍처에서 모든 메타데이터 변경은 `__cluster_metadata` 토픽의 단일 파티션(파티션 0)에 순차적으로 기록된다. `QuorumController`가 리더에서 단일 스레드로 모든 관리 요청을 처리하고, 레코드를 `KafkaRaftClient`를 통해 Raft 합의 후 커밋한다. 이 구조는 다음과 같은 병목을 만든다:

- **단일 파티션 처리량 한계**: 대규모 클러스터(수천 토픽, 수만 파티션)에서 ISR 변경(`AlterPartition`), 브로커 등록(`RegisterBrokerRecord`), 설정 변경(`ConfigRecord`) 등이 모두 단일 파티션에 직렬화되어, 메타데이터 변경 처리량이 병목이 된다.
- **메타데이터 전파 지연**: 일반 브로커는 `MetadataLoader`가 `__cluster_metadata`를 Pull 방식으로 Fetch하여 `MetadataImage`(불변 스냅샷)를 구성하고, `BrokerMetadataPublisher`를 통해 `ReplicaManager`에 파티션 변경을 적용한다. Fetch 간격과 Raft 커밋 지연이 합산되어, 리더 변경 후 프로듀서/컨슈머가 메타데이터를 갱신하기까지 수 초의 지연이 발생할 수 있다.
- **스냅샷 크기 증가**: `metadata.log.max.record.bytes.between.snapshots`(기본 20MB) 간격으로 스냅샷이 생성되지만, 대규모 클러스터에서 `MetadataImage` 전체(TopicsImage, ClusterImage, AclsImage 등)가 수백 MB에 달할 수 있어, 새 브로커 조인 시 `FetchSnapshot`으로 전체 스냅샷을 Pull하는 데 상당 시간이 소요된다.

### 2.5 네트워크 계층의 RequestChannel 단일 큐 경합

**위치**: `core/src/main/scala/kafka/network/SocketServer.scala`, `core/src/main/scala/kafka/network/RequestChannel.scala`

Data-Plane의 모든 Processor 스레드(기본 `num.network.threads=3`)가 하나의 `requestQueue`(`ArrayBlockingQueue`, 기본 크기 `queued.max.requests=500`)에 요청을 넣고, 모든 Handler 스레드(기본 `num.io.threads=8`)가 같은 큐에서 요청을 꺼낸다. 이 설계는 자연스러운 부하 분산을 제공하지만:

- **큐 경합**: 11개 스레드(3 Processor + 8 Handler)가 하나의 `ArrayBlockingQueue`에서 동시에 `put/poll` 연산을 수행하여, 높은 처리량에서 CAS 경합이 발생한다.
- **Head-of-Line Blocking**: 느린 요청(대용량 Produce, 복잡한 Fetch)이 큐를 채우면, 빠른 요청(Heartbeat, Metadata)도 지연된다.
- **채널 뮤팅의 연쇄 효과**: 요청 순서 보장을 위해 `KafkaChannel`이 뮤트되면(`MUTED_AND_RESPONSE_PENDING`), 해당 클라이언트의 후속 요청이 모두 차단된다. Purgatory에 등록된 `DelayedProduce`(acks=-1) 또는 `DelayedFetch`가 완료될 때까지 채널이 뮤트 상태를 유지하여 클라이언트 측 파이프라이닝 효율이 저하된다.

---

## 3. 개선 제안

### 3.1 파티션 쓰기 경로의 잠금 범위 축소 및 락프리 오프셋 할당

#### 문제 상세
`UnifiedLog.append()`의 `synchronized(lock)` 블록이 레코드 검증, 오프셋 할당, 프로듀서 상태 검증, 디스크 기록, HW 업데이트를 모두 포함하여 잠금 보유 시간이 길다. 특히 `LogValidator.validateMessagesAndAssignOffsets()`의 CRC 검증과 바이트 버퍼 조작이 CPU 집약적이다.

#### 근본 원인
Kafka의 파티션 내 오프셋 연속성(gap-free) 보장을 위해 오프셋 할당이 직렬화되어야 하는데, 검증 로직까지 같은 잠금 안에 넣어 불필요하게 임계 영역이 넓다.

#### 제안 솔루션
1. **잠금 밖에서 사전 검증 수행**: `analyzeAndValidateRecords()`, `trimInvalidBytes()`, `LogValidator`의 CRC 검증을 `synchronized` 블록 밖으로 이동한다. 잠금 안에서는 오프셋 할당과 오프셋 기반 검증(순서, 범위)만 수행한다.
2. **AtomicLong 기반 오프셋 예약**: 오프셋을 `AtomicLong.getAndAdd(batchSize)`로 원자적으로 예약하고, 실제 기록은 예약된 오프셋 범위를 사용하여 순서 독립적으로 수행한다. `FileRecords.append()`는 `FileChannel.write(buffer, position)`으로 특정 위치에 기록 가능하다.
3. **프로듀서 상태 검증 분리**: `ProducerStateManager.update()`를 별도의 세밀한 잠금(`ConcurrentHashMap` 수준)으로 분리하여, 서로 다른 프로듀서 ID의 상태 검증이 병렬로 수행되도록 한다.

#### 예상 효과
- 단일 파티션의 동시 쓰기 처리량 2~3배 향상 (잠금 보유 시간 70% 감소 예상)
- Produce 요청의 p99 지연시간 감소

#### 구현 난이도
**높음** -- 오프셋 연속성 보장과 장애 복구 시 일관성 검증이 복잡하다. `LogSegment.append()`의 인덱스 업데이트(`bytesSinceLastIndexEntry` 카운터)도 원자적 처리가 필요하다.

---

### 3.2 로그 컴팩션 엔진의 해싱 알고리즘 교체 및 병렬화

#### 문제 상세
`SkimpyOffsetMap`이 MD5 `MessageDigest`를 사용하여 키당 약 500ns의 해시 비용이 발생하고, 선형 탐사 방식의 해시 테이블이 로드 팩터 0.75 이상에서 급격한 성능 저하를 보인다. 단일 `Cleaner` 스레드가 더티 영역 전체를 순차 처리한다.

#### 근본 원인
`SkimpyOffsetMap`이 설계될 당시(Kafka 초기) 해시 충돌에 의한 데이터 손실 방지를 위해 암호학적 해시를 선택했으나, 컴팩션 목적에서는 충돌 시 "이전 값 보존"만 발생하므로 보안 등급 해시가 불필요하다.

#### 제안 솔루션
1. **xxHash3/xxHash64로 해시 알고리즘 교체**: MD5(16바이트, ~500ns) 대신 xxHash64(8바이트, ~10ns)를 사용한다. 엔트리 크기가 24바이트에서 16바이트(8바이트 해시 + 8바이트 오프셋)로 줄어 동일 메모리에 1.5배 더 많은 키를 추적할 수 있다.
2. **Robin Hood 해싱 적용**: 선형 탐사 대신 Robin Hood 해싱을 사용하여 탐사 거리의 분산을 줄이고, 캐시 라인 활용을 극대화한다.
3. **세그먼트 레벨 병렬 컴팩션**: 더티 영역의 세그먼트들을 독립적으로 분할하여, 여러 `Cleaner` 스레드가 세그먼트 그룹을 병렬로 처리하고 최종 병합한다. `OffsetMap`을 스레드별로 독립 구축 후 병합하는 방식이다.

#### 예상 효과
- 컴팩션 `OffsetMap` 구축 시간 50배 이상 단축 (해시 비용 감소)
- 동일 메모리(`log.cleaner.dedupe.buffer.size`)에서 1.5배 더 많은 키 추적
- 병렬 컴팩션으로 대용량 토픽(수십 GB 이상)의 컴팩션 시간 N배 단축 (N = Cleaner 스레드 수)

#### 구현 난이도
**중간** -- xxHash 교체는 `SkimpyOffsetMap` 내부 수정으로 충분하다. 병렬 컴팩션은 `Cleaner.java`의 `clean()` 메서드 리팩토링과 세그먼트 분할 로직이 필요하다. `.cleaned` -> `.swap` 파일 교체의 원자성 보장에 주의해야 한다.

---

### 3.3 Consumer 그룹 프로토콜(KIP-848) 전환 가속화 및 Reconciliation 최적화

#### 문제 상세
Classic 그룹 프로토콜은 리밸런스 시 전체 그룹이 수십 초에서 수 분간 메시지 소비를 중단한다. `GroupMetadataManager.classicGroupJoin()`에서 `PREPARING_REBALANCE` 진입 시 `rebalanceTimeout` 타이머가 시작되고, 이 기간 동안 모든 JoinGroup 응답이 `CompletableFuture`로 보류된다. 리밸런스당 평균 다운타임이 10~30초이며, 대규모 그룹(100+ 멤버)에서는 1분 이상 소요될 수 있다.

#### 근본 원인
Classic 프로토콜은 클라이언트(리더)가 파티션 할당을 계산하는 구조로, 전체 멤버 정보를 한 번에 수집해야 하므로 Stop-the-World가 불가피하다.

#### 제안 솔루션
1. **KIP-848 Consumer 프로토콜 디폴트 전환**: `group.coordinator.rebalance.protocols` 설정에서 Consumer 프로토콜을 기본값으로 활성화하고, 서버 측 할당(`UniformAssignor`)을 통해 점진적 리밸런스(Cooperative Reconciliation)를 구현한다. `ConsumerGroupHeartbeat` API를 통해 멤버가 독립적으로 할당을 수신한다.
2. **Reconciliation 파이프라이닝**: 현재 `ASSIGNING` -> `RECONCILING` -> `STABLE` 순서에서, 파티션 해제 확인을 기다리지 않고 다음 할당 계산을 시작하는 파이프라인 방식을 적용한다. `TargetAssignmentBuilder`가 이전 Reconciliation 완료 전에 다음 타겟 할당을 미리 계산한다.
3. **__consumer_offsets 파티션 확장**: 기본 50개 파티션을 100개 이상으로 확장하여 `GroupCoordinatorShard`의 부하를 분산한다. 해시 함수(`hash(groupId) % partitions`)의 분포를 개선하여 핫스팟을 방지한다.

#### 예상 효과
- 리밸런스 다운타임: 10~30초 -> 밀리초 단위 (변경된 파티션만 전환)
- 그룹 멤버 100+ 환경에서 리밸런스 안정성 대폭 향상
- 서버 측 할당으로 클라이언트 간 버전 불일치 문제 해소

#### 구현 난이도
**중간** -- KIP-848은 이미 Kafka에 구현되어 있으므로, 마이그레이션 가이드 및 운영 도구 정비가 핵심이다. `ConsumerGroup` 상태 머신과 `ClassicGroup` 간의 자동 전환 로직(`classicGroupJoinToConsumerGroup`) 검증이 필요하다.

---

### 3.4 KRaft 메타데이터 전파 최적화 및 스냅샷 분할

#### 문제 상세
`QuorumController`가 단일 스레드에서 모든 메타데이터 변경을 처리하며, `__cluster_metadata` 단일 파티션의 커밋 지연(Raft 과반수 복제 대기)이 메타데이터 변경 처리량을 제한한다. `MetadataLoader`가 Pull 방식으로 Fetch하므로 전파에 추가 지연이 발생한다.

#### 근본 원인
Raft 프로토콜의 근본적 제약(단일 리더, 순차 로그)과 Pull 기반 복제 설계가 메타데이터 전파 지연의 근본 원인이다. `LeaderState.maybeUpdateHighWatermark()`에서 투표자 오프셋의 중앙값(과반수) 방식으로 HW를 계산하므로, 느린 컨트롤러 노드가 HW 상승을 지연시킨다.

#### 제안 솔루션
1. **배치 레코드 최적화**: `QuorumController`에서 `TopicRecord` + `PartitionRecord * N`을 단일 `RecordBatch`로 묶어 커밋하여 Raft 라운드트립을 줄인다. 현재 개별 레코드로 기록되는 대규모 토픽 생성(수백 파티션)의 지연을 대폭 감소시킨다.
2. **Push 기반 메타데이터 알림**: `BrokerLifecycleManager`의 하트비트 응답에 최신 메타데이터 오프셋을 포함시켜, 브로커가 새 메타데이터가 있음을 즉시 인지하고 Fetch를 트리거하도록 한다. 현재의 Fetch 간격(`controller.quorum.fetch.timeout.ms`, 기본 2000ms) 대기를 제거한다.
3. **분할 스냅샷(Chunked Snapshot)**: `MetadataImage`를 `TopicsImage`, `ClusterImage`, `AclsImage` 등 하위 이미지별로 분할하여 독립적으로 스냅샷하고, `FetchSnapshot`에서 필요한 부분만 전송한다. 새 브로커 조인 시 전체 스냅샷 대신 필요한 이미지만 로드한다.
4. **AlterPartition 배치 처리**: ISR 변경이 빈번한 환경에서 동일 에포크 내의 `AlterPartition` 요청을 10ms 윈도우로 배치하여 단일 레코드로 기록한다.

#### 예상 효과
- 메타데이터 변경 전파 지연: 2~5초 -> 500ms 이하
- 대규모 토픽 생성 시간: 파티션 수에 비례하던 지연이 배치 처리로 상수 시간에 가까워짐
- 새 브로커 조인 시 메타데이터 로드 시간: 수십 초 -> 수 초

#### 구현 난이도
**높음** -- Raft 프로토콜 레벨의 변경이 필요하며, `KafkaRaftClient`의 Fetch 흐름과 `MetadataLoader`의 스냅샷 처리 로직에 대한 심층적 이해가 요구된다. 분할 스냅샷은 `MetadataImage`의 직렬화/역직렬화 로직 전체를 재설계해야 한다.

---

### 3.5 네트워크 계층의 요청 우선순위 큐 도입 및 큐 분리

#### 문제 상세
모든 API 요청(Produce, Fetch, Heartbeat, Metadata, AlterPartition 등 80+ 종류)이 단일 `requestQueue`를 공유하여, 대용량 Produce/Fetch 요청이 경량 Heartbeat/Metadata 요청의 지연을 유발한다.

#### 근본 원인
`RequestChannel`의 `ArrayBlockingQueue`가 FIFO 방식으로 모든 요청을 동등하게 처리하여, 요청 유형별 우선순위 차별화가 불가능하다.

#### 제안 솔루션
1. **다중 우선순위 큐**: `RequestChannel`을 3개의 우선순위 레벨로 분리한다.
   - **High**: Heartbeat, Metadata, ApiVersions, LeaderAndIsr (지연 민감)
   - **Medium**: Produce, OffsetCommit (일반 데이터 경로)
   - **Low**: Fetch, ListOffsets (배치 처리 가능)
   Handler 스레드가 High 큐를 우선 확인하고, 비어있으면 Medium, Low 순서로 처리한다.
2. **큐 파티셔닝**: `requestQueue`를 Handler 스레드 수만큼 분할하여 경합을 줄인다. Processor가 요청의 해시(파티션 ID 기반)로 특정 큐에 넣어, 같은 파티션의 요청이 같은 Handler에서 처리되도록 하여 캐시 지역성을 향상시킨다.
3. **적응형 채널 뮤팅**: 현재의 채널 단위 뮤팅 대신, 요청 단위 큐잉을 구현하여 하나의 TCP 연결에서 여러 요청을 파이프라이닝할 수 있도록 한다. `CorrelationId` 기반으로 응답을 비순차 전송하고, 클라이언트에서 재정렬한다.

#### 예상 효과
- Heartbeat/Metadata 요청의 p99 지연시간: 100ms+ -> 10ms 이하
- 높은 Produce 부하에서의 컨슈머 그룹 안정성 향상 (Heartbeat 지연으로 인한 불필요한 리밸런스 방지)
- 큐 파티셔닝으로 CAS 경합 3~5배 감소

#### 구현 난이도
**중간** -- 우선순위 큐는 `RequestChannel.scala` 수정으로 구현 가능하다. 비순차 응답은 Kafka 프로토콜의 순서 보장 계약을 변경하므로 클라이언트 호환성 검증이 필요하다. `SocketServer.scala`의 Processor 실행 루프(`processCompletedReceives`, `processNewResponses`)와의 통합이 핵심이다.

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 구현 난이도 | 영향도 | 예상 기간 | 선행 조건 |
|---------|----------|-----------|-------|----------|----------|
| **P0** | 3.3 Consumer 그룹 프로토콜 전환 | 중간 | 매우 높음 | 4~6주 | KIP-848 활성화 검증 |
| **P1** | 3.5 네트워크 요청 우선순위 큐 | 중간 | 높음 | 6~8주 | RequestChannel 리팩토링 |
| **P1** | 3.2 로그 컴팩션 엔진 개선 | 중간 | 높음 | 4~6주 | xxHash 라이브러리 통합 |
| **P2** | 3.4 KRaft 메타데이터 최적화 | 높음 | 높음 | 8~12주 | Raft 프로토콜 이해 |
| **P2** | 3.1 파티션 쓰기 잠금 최적화 | 높음 | 중간 | 8~12주 | 장애 복구 검증 체계 |

### 단계별 로드맵

**Phase 1 (1~2개월): 즉시 적용 가능한 개선**
- Consumer 그룹 프로토콜(KIP-848) 전환 검증 및 마이그레이션
- 로그 컴팩션 해시 알고리즘 교체 (MD5 -> xxHash64)
- 벤치마크 환경 구축 (ProducerPerformance, ConsumerPerformance 기반)

**Phase 2 (2~4개월): 네트워크 및 성능 최적화**
- RequestChannel 우선순위 큐 구현
- 로그 컴팩션 병렬화
- KRaft 배치 레코드 최적화 및 AlterPartition 배치 처리

**Phase 3 (4~6개월): 핵심 엔진 개선**
- 파티션 쓰기 경로 잠금 범위 축소
- KRaft Push 기반 메타데이터 알림
- 분할 스냅샷 프로토타이핑

---

## 5. 기대 성과

### 정량적 성과

| 지표 | 현재 수준 (추정) | 개선 후 목표 |
|------|----------------|------------|
| 리밸런스 다운타임 | 10~30초 | < 1초 |
| Heartbeat p99 지연 (고부하) | 100~500ms | < 10ms |
| 로그 컴팩션 처리 속도 | 50MB/s (단일 스레드) | 200MB/s+ (병렬) |
| 메타데이터 전파 지연 | 2~5초 | < 500ms |
| 단일 파티션 쓰기 처리량 | 100K msg/s | 200~300K msg/s |
| 새 브로커 조인 시간 (대규모) | 30~60초 | < 10초 |

### 정성적 성과

- **운영 안정성 향상**: 불필요한 리밸런스 감소로 서비스 가용성 개선
- **확장성 개선**: 대규모 클러스터(1000+ 브로커, 100K+ 파티션) 운영 가능성 확대
- **오픈소스 기여 역량 확보**: Kafka 커뮤니티에 구체적인 개선 패치를 제안할 수 있는 기술적 기반 마련
- **내부 역량 강화**: Kafka 핵심 엔진에 대한 심층 이해를 바탕으로 장애 진단 및 성능 튜닝 역량 확보
