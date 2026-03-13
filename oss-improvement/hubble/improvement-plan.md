# Hubble 개선 계획서

## 1. 프로젝트 개요

Hubble은 Cilium 기반 Kubernetes 클러스터의 네트워크 관측(observability) 플랫폼이다. CLI - Server(Observer) - Relay의 3계층 아키텍처로 구성되며, 각 노드의 Cilium Agent 내부에서 eBPF 이벤트를 수집하여 Ring Buffer에 저장하고, gRPC API를 통해 클라이언트에 전달한다. Relay는 클러스터 전체 노드의 Flow를 중앙에서 집계하여 시간순으로 정렬된 통합 뷰를 제공한다.

핵심 데이터 경로: `eBPF Perf Ring Buffer -> MonitorAgent -> Observer events channel -> Parser.Decode() -> Hook Chain -> Ring Buffer -> gRPC GetFlows -> (Relay PriorityQueue) -> Client`

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 Ring Buffer 용량 제약 및 고정 크기 한계

**현상**: Ring Buffer의 용량이 `2^n - 1` 형태로 고정되어 있으며, 최대 65,535개 이벤트만 저장할 수 있다. `container/ring.go`의 `Capacity` 타입이 `uint16` 기반이므로 이 상한을 넘을 수 없다. 대규모 클러스터(100+ Pod)에서 초당 수만 건의 Flow가 발생할 경우, Ring Buffer가 수 초 내에 한 바퀴 돌아 reader가 따라잡지 못하면 `LostEvent`가 빈번하게 발생한다.

**코드 경로**: `container/ring.go`의 `capacity` 타입 정의(`type capacity uint16`)가 근본 제약이며, `NewCapacity()` 함수에서 `n & (n+1) == 0` 검증으로 인해 임의 크기 지정이 불가능하다. `read()` 함수의 default 케이스와 `readFrom()`의 default 케이스에서 오버플로우 시 `getLostEvent()`를 호출하며, 이때 `hubble_lost_events_total{source="hubble_ring_buffer"}` 메트릭만 증가시키고 유실된 이벤트 수를 정확히 추적하지 못한다(`NumEventsLost: 1`로 고정).

**영향**: 트래픽이 많은 노드에서 과거 Flow 조회(`hubble observe --last N`)의 시간 범위가 극히 짧아지며, Relay를 통한 클러스터 전체 조회 시 특정 노드의 데이터가 과도하게 유실될 수 있다.

### 2.2 Relay PriorityQueue의 O(N) 병합 및 단일 스레드 정렬 병목

**현상**: Relay의 `GetFlows` 파이프라인에서 모든 노드의 Flow를 하나의 공유 채널(`flows chan`)로 수집한 뒤, `sortFlows()` 단계에서 `queue/priority_queue.go`의 힙 기반 PriorityQueue로 시간순 정렬을 수행한다. 이 정렬은 단일 고루틴에서 실행되며, 노드 수가 많을수록 힙 연산(insert/pop이 O(log N))의 부하가 커진다.

**코드 경로**: `relay/observer/observer.go`의 `flowCollector.collect()`에서 각 피어별 고루틴이 `retrieveFlowsFromPeer()`를 실행하여 공유 채널에 Flow를 push한다. 이후 `aggregateErrors()` -> `sortFlows()` -> `sendFlowsResponse()` 파이프라인이 순차적으로 동작한다. 에러 집계(`aggregateErrors`)에서 10초 윈도우 내 동일 에러를 병합하는 로직은 모든 Flow가 이 단일 파이프라인을 거쳐야 하므로 백프레셔가 발생한다.

**영향**: 100+ 노드 클러스터에서 `hubble observe --follow`를 실행하면 Relay의 CPU 사용량이 급증하고, 정렬 지연으로 인해 실시간 Flow 스트리밍의 end-to-end 레이턴시가 수백 ms까지 증가할 수 있다.

### 2.3 L3/L4 Parser의 gopacket Mutex 경합

**현상**: `threefour/parser.go`의 `packetDecoder` 구조체는 gopacket의 `DecodingLayerParser`를 메모리 재할당 없이 재사용하기 위해 구조체 필드로 레이어 객체를 임베딩한다. 이 설계는 동시 접근 시 `lock.Mutex`로 보호되며(`DecodePacket()` 시작 시 `d.Lock()`, 종료 시 `defer d.Unlock()`), Observer의 이벤트 처리 루프(`LocalObserverServer.Start()`)가 단일 고루틴이므로 현재는 문제가 없지만, 향후 파이프라인 병렬화 시 심각한 병목이 된다.

**코드 경로**: `threefour/parser.go`의 `packetDecoder.DecodePacket()` 메서드에서 Mutex를 잡고, L2/L3 디바이스 타입에 따라 `decLayerL2Dev.DecodeLayers()` 또는 `decLayerL3Dev.IPv4/IPv6.DecodeLayers()`를 호출한다. 오버레이 네트워크(VXLAN/Geneve)의 경우 `decLayerOverlay` 디코더도 같은 Mutex 아래에서 실행된다. 이 Mutex는 전체 L3/L4 파싱을 직렬화한다.

**영향**: Observer 이벤트 루프를 worker pool 패턴으로 병렬화하려 해도, 파서의 Mutex가 직렬화 포인트가 되어 처리량 개선이 제한된다.

### 2.4 메트릭 핸들러의 순차 실행 및 고카디널리티 레이블 문제

**현상**: `StaticFlowProcessor.OnDecodedFlow()`에서 10개의 메트릭 핸들러(DNS, HTTP, TCP, Drop, Flow, Policy, ICMP, Port-Distribution, SCTP, Flows-to-World)가 순차적으로 실행된다. 각 핸들러는 `ProcessFlow()` 내에서 `prometheus.CounterVec.WithLabelValues()`를 호출하는데, 이는 내부적으로 Prometheus 라이브러리의 `metricMap`에 대한 읽기/쓰기 잠금을 수반한다.

**코드 경로**: `metrics/flow_processor.go`의 `for _, nh := range p.metrics { errs = errors.Join(errs, nh.Handler.ProcessFlow(ctx, flow)) }` 루프. 특히 `ContextOptions`에서 `sourceContext=pod;destinationContext=pod` 설정 시 Pod 이름이 레이블에 포함되어 카디널리티가 Pod 수의 제곱으로 폭발한다. `CiliumEndpointDeletionHandler`가 graceful period(1분) 후에 메트릭을 정리하지만, 그 사이 메모리 사용량이 급증할 수 있다.

**영향**: 대규모 클러스터에서 메트릭 핸들러가 Observer 이벤트 루프의 처리 속도를 저하시키며, Prometheus 스크레이핑 시 수만 개의 시계열로 인해 `/metrics` 엔드포인트 응답 시간이 수 초로 증가한다.

### 2.5 필터 체인의 비효율적 평가 순서

**현상**: `filters/filters.go`의 `DefaultFilters()`가 반환하는 24개 필터 빌더의 순서가 고정되어 있으며, 필터 선별력(selectivity)에 따른 동적 재정렬이 없다. `BuildFilterList()`에서 생성된 `FilterFuncs`의 `MatchAll()`은 첫 번째 false에서 단락 평가되지만, 선별력이 낮은 필터(예: `IPVersionFilter`)가 선별력이 높은 필터(예: `VerdictFilter`)보다 먼저 평가될 수 있다.

**코드 경로**: `filters/filters.go`의 `DefaultFilters()` 반환 순서에서 `UUIDFilter` -> `EventTypeFilter` -> `VerdictFilter` -> ... -> `CELExpressionFilter` 순이다. IP 필터(`filterByIPs`)에서는 매 이벤트마다 `slices.Contains(addresses, eventIP)`로 O(N) 선형 탐색을 수행하며, CIDR 매칭 시에도 `slices.ContainsFunc(prefixes, prefix.Contains)`로 prefix 목록을 순회한다.

**영향**: 복잡한 필터 조합을 사용하는 `GetFlows` 요청에서 불필요한 필터 평가로 CPU 시간이 낭비되며, 특히 대량 Flow를 처리하는 Relay에서 필터링 오버헤드가 누적된다.

---

## 3. 개선 제안

### 3.1 적응형 Ring Buffer 확장 및 정밀 유실 추적

**문제 상세**: Ring Buffer 용량이 `uint16` 기반 `2^n - 1`로 고정되어 최대 65,535개. 고트래픽 노드에서 LostEvent가 빈번히 발생하며, `getLostEvent()`가 `NumEventsLost: 1`로 고정 보고하여 실제 유실 규모를 파악할 수 없다.

**근본 원인**: `capacity` 타입의 `uint16` 제한과 비트마스크 기반 인덱싱(`write & mask`)이 2의 거듭제곱 크기를 강제한다. 또한 `read()` 함수의 사이클 기반 유효성 검사가 유실된 이벤트의 정확한 수를 계산하지 않는다.

**제안 솔루션**:
1. `capacity` 타입을 `uint32`로 확장하여 최대 `2^20 - 1` (1,048,575개)까지 지원. `cycleExp` 필드가 `uint8`이므로 지수 범위에는 여유가 있다.
2. `read()` 함수의 default 케이스에서 `(writeCycle - readCycle) * dataLen`으로 유실 이벤트 수를 근사 계산하여 `LostEvent.NumEventsLost`에 반영.
3. 런타임 메트릭 기반 자동 튜닝 가이드: `hubble_lost_events_total` 증가율이 임계치를 초과하면 Helm 설정 변경을 권고하는 Operator webhook 추가.

**예상 효과**: 대규모 노드에서 LostEvent 발생률 80% 이상 감소. 유실 이벤트의 정확한 규모 파악으로 용량 계획 개선.

**구현 난이도**: 중 (capacity 타입 변경은 하위 호환성 주의 필요, Helm chart 및 ConfigMap 연동)

---

### 3.2 Relay 다중 스트림 병합 최적화 (K-way Merge 도입)

**문제 상세**: Relay의 `flowCollector.collect()`에서 모든 노드의 Flow가 단일 공유 채널로 합쳐진 뒤 `sortFlows()`에서 힙 기반 정렬을 수행한다. 노드 수 K에 대해 힙 연산 비용이 O(log K)이지만, 공유 채널로의 fan-in이 순서 정보를 잃어 불필요한 정렬 작업을 유발한다.

**근본 원인**: 각 노드의 gRPC 스트림은 이미 시간순으로 정렬된 Flow를 전달하지만, 공유 채널에서 뒤섞이면서 이 속성이 파괴된다. PriorityQueue가 개별 이벤트 단위로 힙을 유지하므로 메모리 할당과 비교 연산이 과도하다.

**제안 솔루션**:
1. 공유 채널 대신 노드별 개별 채널을 유지하고, K-way merge 알고리즘으로 변경. 각 노드 채널의 head를 min-heap에 유지하여 정렬된 스트림의 속성을 활용.
2. `sortFlows()` 단계에서 배치 처리 도입: 일정 시간 윈도우(예: 100ms) 동안 수집된 Flow를 한 번에 정렬하여 힙 연산 횟수를 줄임.
3. `aggregateErrors()`의 10초 윈도우를 설정 가능하게 만들어 클러스터 규모에 따라 튜닝 가능하도록 함.

**예상 효과**: 100노드 기준 Relay CPU 사용량 30-40% 감소. GetFlows 스트리밍의 end-to-end 레이턴시 50% 이상 개선.

**구현 난이도**: 상 (relay/observer 패키지의 파이프라인 구조 변경 필요, 기존 테스트 대폭 수정)

---

### 3.3 Parser 인스턴스 풀링을 통한 병렬 디코딩

**문제 상세**: `threefour/parser.go`의 `packetDecoder`가 단일 Mutex로 보호되어 모든 L3/L4 패킷 디코딩이 직렬화된다. 현재 Observer 이벤트 루프가 단일 고루틴이므로 문제가 드러나지 않지만, 초당 10만+ Flow 처리가 필요한 환경에서는 디코딩이 병목이 된다.

**근본 원인**: gopacket의 `DecodingLayerParser`가 내부 상태를 가지므로(레이어 구조체 재사용) 동시 접근이 불가하다. 이를 단일 인스턴스로 공유하면서 Mutex로 보호하는 현재 설계는 메모리 효율적이나 확장성이 없다.

**제안 솔루션**:
1. `sync.Pool` 기반으로 `packetDecoder` 인스턴스 풀 도입. CPU 코어 수만큼의 인스턴스를 미리 할당하여 Mutex 없이 병렬 디코딩 가능.
2. Observer 이벤트 루프를 producer-consumer 패턴으로 변경: `events channel`에서 읽은 `MonitorEvent`를 worker pool의 고루틴에 분배, 각 worker가 자체 `packetDecoder` 인스턴스로 디코딩. 디코딩 완료된 Flow를 순서 보장 큐(sequence number 기반)를 통해 Ring Buffer에 쓰기.
3. Hook 체인(`OnMonitorEvent`, `OnDecodedFlow`, `OnDecodedEvent`)의 스레드 안전성 검증 및 필요시 atomic 연산으로 전환.

**예상 효과**: 멀티코어 환경에서 파싱 처리량 4-8배 향상. 초당 50만+ Flow 처리 가능.

**구현 난이도**: 상 (Observer 이벤트 루프의 근본적 재설계 필요, Hook 체인의 동시성 모델 변경)

---

### 3.4 메트릭 핸들러 병렬 실행 및 카디널리티 제어

**문제 상세**: `StaticFlowProcessor`가 10개 메트릭 핸들러를 순차 호출하며, Pod 기반 ContextOptions 사용 시 레이블 카디널리티가 O(N^2)으로 폭발한다. `DynamicFlowProcessor`의 `RWMutex`도 설정 변경 시 모든 `ProcessFlow()` 호출을 블로킹한다.

**근본 원인**: `for _, nh := range p.metrics` 순차 루프 구조와 Prometheus의 `MetricVec` 내부 잠금. ContextOptions가 카디널리티 상한을 제어하는 메커니즘이 없다.

**제안 솔루션**:
1. 메트릭 핸들러를 카테고리별로 그룹화(lightweight: Flow, Drop, TCP / heavyweight: HTTP, DNS, Policy)하고, heavyweight 그룹을 별도 고루틴에서 비동기 실행. 경량 채널 버퍼로 Flow를 전달.
2. ContextOptions에 `maxCardinality` 설정 추가: 레이블 조합 수가 임계치를 초과하면 `__overflow__` 레이블로 집계하여 시계열 폭발 방지.
3. `DynamicFlowProcessor`의 설정 변경을 atomic pointer swap으로 변경하여 `RWMutex` 대신 lock-free 읽기 경로 구현. `atomic.Value`에 `[]NamedHandler` 슬라이스를 저장.
4. `CiliumEndpointDeletionHandler`의 graceful period를 설정 가능하게 하고, 대규모 Pod churn 시 배치 삭제 지원.

**예상 효과**: 메트릭 처리 오버헤드 50% 감소. 고카디널리티 환경에서 Prometheus 메모리 사용량 70% 절감. `/metrics` 응답 시간 2초 -> 200ms.

**구현 난이도**: 중 (비동기 실행은 채널 기반으로 비교적 단순, 카디널리티 제어는 Prometheus 라이브러리 래핑 필요)

---

### 3.5 필터 체인 최적화 (인덱싱 및 적응형 재정렬)

**문제 상세**: 24개 필터 빌더가 고정 순서로 평가되며, IP 필터의 `slices.Contains()`가 O(N) 선형 탐색. CIDR 매칭도 prefix 목록 순회. 복잡한 필터 조합 시 불필요한 평가가 누적된다.

**근본 원인**: `DefaultFilters()` 반환 순서가 코드에 하드코딩되어 있고, 필터 선별력에 따른 동적 최적화가 없다. IP 주소 매칭에 해시 셋이나 트라이(trie) 같은 효율적 자료구조를 사용하지 않는다.

**제안 솔루션**:
1. IP 필터에서 정확 매치용 `map[string]struct{}` 해시 셋 도입 (`slices.Contains` -> `map` 조회로 O(1)). CIDR 매칭에 `netipx.IPSet` 또는 radix trie 도입으로 O(prefix 길이) 조회.
2. `BuildFilterList()` 단계에서 필터 빌드 후, 간단한 휴리스틱(필터 타입별 기대 선별력)으로 `FilterFuncs` 슬라이스를 재정렬. 예: `VerdictFilter`(높은 선별력) -> `EventTypeFilter` -> `IPFilter` -> `LabelsFilter`(낮은 선별력).
3. CEL 표현식 필터에 컴파일된 프로그램 캐싱 레이어 추가. 동일 표현식의 반복 빌드 방지.

**예상 효과**: 필터링 처리 시간 40-60% 감소. 대규모 CIDR 목록(100+ 항목) 사용 시 10배 이상 성능 향상.

**구현 난이도**: 하-중 (IP 필터 최적화는 기존 인터페이스 유지하면서 내부 구현만 변경, 재정렬은 BuildFilterList 수정)

---

## 4. 우선순위 및 로드맵

### Phase 1 (1-2개월): 즉시 효과, 낮은 위험도

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 1 | 3.5 필터 체인 최적화 (IP 해시셋/트라이) | 기존 인터페이스 변경 없이 내부 최적화만으로 즉시 효과. 테스트 용이. |
| 2 | 3.1 Ring Buffer capacity uint32 확장 | 타입 변경과 Helm chart 수정으로 비교적 단순. 대규모 환경 즉시 혜택. |
| 3 | 3.4 카디널리티 제어 (maxCardinality) | Prometheus OOM 방지를 위한 안전장치. 운영 안정성 직결. |

### Phase 2 (2-4개월): 중간 규모 구조 개선

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 4 | 3.4 메트릭 핸들러 비동기 실행 | Observer 이벤트 루프 처리량 개선. Phase 3의 전제 조건. |
| 5 | 3.1 정밀 유실 추적 (NumEventsLost 계산) | 운영 가시성 개선. 용량 계획에 필수. |
| 6 | 3.5 적응형 필터 재정렬 | Phase 1의 기반 위에 추가 최적화. |

### Phase 3 (4-6개월): 아키텍처 레벨 개선

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 7 | 3.3 Parser 인스턴스 풀링 및 병렬 디코딩 | Observer 처리량의 근본적 확장. 멀티코어 활용. |
| 8 | 3.2 Relay K-way Merge 도입 | 대규모 클러스터 Relay 성능의 결정적 개선. |
| 9 | 3.4 DynamicFlowProcessor lock-free 설계 | 동적 메트릭 재설정의 무중단 운영. |

---

## 5. 기대 성과

### 정량적 기대 효과

| 지표 | 현재 (추정) | 개선 후 (목표) | 개선율 |
|------|------------|---------------|--------|
| 노드당 최대 Ring Buffer 용량 | 65,535 이벤트 | 1,048,575 이벤트 | 16배 |
| 고트래픽 노드 LostEvent 비율 | 5-15% | < 1% | 80%+ 감소 |
| Observer 초당 Flow 처리량 | ~100K flows/s | ~500K flows/s | 5배 |
| Relay GetFlows 레이턴시 (100노드) | 200-500ms | 50-100ms | 75% 감소 |
| 필터 평가 시간 (100 CIDR 필터) | ~50us/flow | ~5us/flow | 10배 |
| Prometheus /metrics 응답 시간 | 2-5초 | < 500ms | 80% 감소 |
| 메트릭 메모리 사용량 (1000 Pod) | ~2GB | ~600MB | 70% 감소 |

### 정성적 기대 효과

- **운영 안정성**: LostEvent 감소와 정밀 유실 추적으로 네트워크 이벤트 관측의 신뢰성 향상
- **확장성**: 멀티코어 활용과 K-way Merge로 대규모 클러스터(500+ 노드) 지원 가능
- **비용 효율**: Prometheus 메모리/CPU 사용량 감소로 모니터링 인프라 비용 절감
- **개발자 경험**: 필터링 성능 개선으로 `hubble observe` 명령의 응답성 향상
- **커뮤니티 기여 가치**: 상위 CNCF 프로젝트에 대한 실질적 성능 개선 기여로 조직의 OSS 역량 입증
