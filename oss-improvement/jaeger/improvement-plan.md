# Jaeger 분산 트레이싱 시스템 개선 계획서

## 1. 프로젝트 개요

Jaeger는 CNCF Graduated 프로젝트로, 마이크로서비스 아키텍처에서 요청의 end-to-end 흐름을 추적하는 분산 트레이싱 플랫폼이다. v2에서 OpenTelemetry Collector를 런타임 프레임워크로 채택하여 단일 바이너리 구조로 전환했으며, Receiver -> Processor -> Exporter 파이프라인 모델과 Extension 메커니즘을 활용한다.

주요 스토리지 백엔드로 Memory, Badger, Cassandra, Elasticsearch, ClickHouse를 지원하며, 적응형 샘플링, Query API(HTTP/gRPC), MCP 서버(LLM 통합) 등을 Extension으로 구현하고 있다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 V1/V2 어댑터 계층의 다중 데이터 변환 오버헤드

**현상**: Cassandra, Elasticsearch 등 주요 프로덕션 스토리지 백엔드는 아직 v1 API(`model.Span` 기반)로 구현되어 있다. v2 파이프라인에서 들어온 `ptrace.Traces` 데이터는 다음과 같은 다중 변환을 거친다:

```
ptrace.Traces (OTLP)
  -> V1BatchesFromTraces() [v1adapter/translator.go:20]
    -> model.Span (Jaeger IDL v1)
      -> dbmodel.Span (Cassandra/ES별 DB 모델)
        -> CQL INSERT / ES Bulk
```

읽기 경로는 역순으로 3번의 역변환이 발생한다. `V1BatchesFromTraces()`와 `V1BatchesToTraces()` 함수(`internal/storage/v2/v1adapter/translator.go`)에서 매 호출 시 전체 Span 트리를 순회하며 필드를 복사해야 하고, 특히 Attributes(`pcommon.Map`)와 `model.KeyValue` 간 변환에서 타입별 분기(STRING, BOOL, INT64, FLOAT64, BINARY)가 매 태그마다 실행된다.

**영향**: 초당 수만 건의 Span을 처리하는 프로덕션 환경에서 CPU 사용량의 상당 부분이 직렬화/역직렬화 변환에 소모되며, GC 압력 증가로 tail latency가 높아진다.

### 2.2 Cassandra 인덱스 테이블의 Fan-out 쿼리 병목

**현상**: Cassandra 스토리지의 `FindTraces()` 쿼리는 비정규화된 인덱스 테이블 구조로 인해 심각한 fan-out 문제를 가진다. 서비스+오퍼레이션+태그+duration 조건이 포함된 복합 쿼리는 다음과 같이 실행된다:

1. `service_operation_index` 테이블에서 trace_id 집합 조회
2. `tag_index` 테이블에서 태그별 trace_id 집합 조회 (태그 수만큼 별도 쿼리)
3. `duration_index` 테이블에서 duration 범위 trace_id 조회
4. **클라이언트 측에서** 모든 trace_id 집합의 교집합(intersection) 계산
5. 교집합 결과의 각 trace_id로 `traces` 테이블에서 실제 Span 데이터 조회

Cassandra는 서버 측 JOIN을 지원하지 않으므로, `SpanReader.FindTraces()` (`internal/storage/v1/cassandra/spanstore/reader.go`)에서 Go 코드로 교집합을 계산한다. 태그 필터가 3개면 최소 4개의 인덱스 쿼리 + 1개의 traces 조회로 총 5회 이상의 Cassandra 라운드트립이 발생한다.

또한 `tag_index` 테이블의 파티션 키가 `(service_name, tag_key, tag_value)`로 설계되어 있어, 카디널리티가 높은 태그(예: `request.id`, `user.id`)는 파티션이 매우 작아져 비효율적이고, 반대로 카디널리티가 낮은 태그(예: `http.method=GET`)는 파티션이 극도로 비대해져 핫스팟이 발생한다.

**영향**: 복합 조건 검색 시 응답 시간이 수 초에 달하며, 대규모 환경에서 Query 서비스의 확장성을 저해한다.

### 2.3 Memory 스토리지의 단일 Mutex 경합 및 O(n) 검색

**현상**: Memory 스토리지(`internal/storage/v2/memorystore/`)는 `sync.RWMutex` 하나로 전체 데이터 구조를 보호한다. 모든 `FindTraces()` 호출은 읽기 잠금을 획득한 후, 전체 트레이스 맵을 순회하며 조건을 평가하는 O(n) 풀 스캔을 수행한다.

서비스명, 오퍼레이션명에 대한 인덱스가 없으므로, `max_traces: 100000` 설정 시 10만 개 트레이스의 모든 Span을 검사해야 한다. 쓰기 경로에서는 `WriteTraces()`가 쓰기 잠금을 획득하므로, 읽기와 쓰기가 완전히 직렬화된다.

또한 멀티테넌시 지원을 위한 `perTenant` 맵 구조에서, 테넌트별 독립 Store를 생성하지만 상위 레벨의 테넌트 맵 접근에도 별도 잠금이 필요하여 이중 잠금이 발생한다.

**영향**: All-in-One 모드에서 개발/테스트 환경의 응답성 저하. 트레이스 수가 증가하면 쿼리 지연이 선형적으로 증가한다.

### 2.4 적응형 샘플링의 단일 리더 선출 병목 및 갱신 지연

**현상**: 적응형 샘플링(`internal/sampling/samplingstrategy/adaptive/`) 시스템은 분산 환경에서 단일 리더(Leader)만 샘플링 확률을 계산하는 구조이다.

리더 선출은 `distributedlock.Lock` 인터페이스(`internal/distributedlock/interface.go`)의 `Acquire(resource, ttl)` 메서드를 통해 이루어지며, Cassandra의 경우 `locks` 테이블에 대한 Lightweight Transaction(LWT: `IF NOT EXISTS`)을 사용한다. 이 LWT는 Paxos 합의를 거치므로 일반 쓰기 대비 4-6배 느리다.

`aggregator.go`의 처리량 집계 주기(`AggregationInterval`, 기본 1분)와 `post_aggregator.go`의 확률 계산 주기(`CalculationInterval`, 기본 1분)가 결합되면, 트래픽 패턴 변화 후 최대 2분까지 샘플링 확률이 구식 상태로 유지된다. 급격한 트래픽 스파이크 시 과도한 샘플링 또는 과소 샘플링이 발생할 수 있다.

또한, `weightvectorcache.go`의 가중치 벡터 캐시는 서비스-오퍼레이션 쌍이 매우 많은 대규모 환경에서 메모리 사용량이 선형적으로 증가하며, 캐시 무효화 전략이 TTL 기반이라 이미 사라진 서비스의 데이터가 계속 남아 있을 수 있다.

**영향**: 트래픽 변동이 심한 대규모 마이크로서비스 환경에서 샘플링 정확도 저하, 중요 트레이스 누락 또는 스토리지 비용 증가.

---

## 3. 개선 제안

### 3.1 V2 네이티브 스토리지 구현 가속화

**문제 상세**: Cassandra, Elasticsearch 백엔드가 v1 API(`WriteSpan(model.Span)`)로 구현되어 있어, v2 파이프라인(`WriteTraces(ptrace.Traces)`)과의 연결에 `v1adapter` 변환 계층이 필수적이다. `internal/storage/v2/v1adapter/tracereader.go`와 `tracewriter.go`에서 매번 ptrace <-> model.Span 변환이 발생한다.

**근본 원인**: Jaeger v2 마이그레이션 과정에서 스토리지 백엔드는 v1 구현을 어댑터로 래핑하는 점진적 전략을 채택했다. Memory 스토리지만 v2 네이티브(`internal/storage/v2/memorystore/`)로 구현되어 있고, 나머지는 모두 v1 어댑터를 사용한다.

**제안 솔루션**:
1. **Elasticsearch v2 네이티브 Writer 구현**: `ptrace.Traces`를 직접 ES Bulk 문서로 변환하는 Writer를 구현한다. OTLP의 `ResourceSpans -> ScopeSpans -> Spans` 계층 구조를 ES 인덱스 매핑에 직접 매핑하여, 중간 `model.Span` 변환을 제거한다. ES의 `_bulk` API에 대한 직접 JSON 직렬화를 통해 할당 수를 최소화한다.
2. **Cassandra v2 네이티브 Writer 구현**: `ptrace.Span`의 Attributes(`pcommon.Map`)를 CQL 바인딩 값으로 직접 변환하는 경로를 구현한다. `ResourceSpans` 단위로 배치 CQL을 생성하여 Process 중복 직렬화를 제거한다.
3. **벤치마크 기반 점진적 전환**: v1 어댑터 경로와 v2 네이티브 경로를 feature flag로 전환할 수 있도록 구현하고, 처리량/지연/메모리 사용량 비교 벤치마크를 수행한다.

**예상 효과**:
- Write 경로 CPU 사용량 20-35% 감소 (변환 오버헤드 제거)
- GC 압력 감소로 P99 지연 개선
- Read 경로에서도 역변환 제거 시 쿼리 응답 시간 15-25% 개선

**구현 난이도**: 상 (각 백엔드별 DB 모델 직접 매핑 구현 필요, 기존 스키마 호환성 유지 필수)

---

### 3.2 Cassandra 인덱스 구조 개선 및 클라이언트 측 쿼리 최적화

**문제 상세**: `FindTraces()` 경로에서 다수의 인덱스 테이블에 대한 병렬 쿼리 후 클라이언트 측 교집합 계산이 필요하다. `SpanReader`의 `findTraceIDs()` 메서드가 각 인덱스 쿼리를 goroutine으로 병렬 실행하지만, 결과를 모두 수집한 후에야 교집합을 계산할 수 있어 가장 느린 인덱스 쿼리가 전체 응답 시간을 결정한다.

**근본 원인**: Cassandra의 데이터 모델 제약(서버 측 JOIN 불가, 파티션 키 등가 조건 필수)으로 인해 쿼리 패턴별로 비정규화된 인덱스 테이블이 필요하고, 교집합 연산을 클라이언트에서 수행해야 한다. 또한 `tag_index`의 파티션 키 설계가 카디널리티 분포를 고려하지 않아 핫스팟이 발생한다.

**제안 솔루션**:
1. **Bloom Filter 기반 사전 필터링**: 인덱스 쿼리 결과에 대해 Bloom Filter를 적용하여, 전체 trace_id 집합을 메모리에 로드하지 않고도 교집합 후보를 빠르게 추려낸다. 첫 번째 인덱스 쿼리 결과로 Bloom Filter를 생성하고, 이후 인덱스 결과를 스트리밍하며 필터링한다.
2. **시간 기반 파티션 버킷팅 강화**: `tag_index` 테이블의 파티션 키에 시간 버킷(예: 1시간 단위)을 추가하여 `(service_name, tag_key, tag_value, time_bucket)` 구조로 변경한다. 이렇게 하면 핫스팟이 시간 축으로 분산되고, 시간 범위 쿼리 시 필요한 파티션만 스캔할 수 있다.
3. **쿼리 결과 캐싱 계층 추가**: `FindTraces()` 결과에 대한 LRU 캐시를 Query 서비스 레벨에서 도입한다. 동일한 검색 조건에 대한 반복 쿼리(예: UI 페이지 새로고침)를 캐시에서 즉시 응답한다. `internal/cache/lru.go`의 기존 LRU 구현을 활용한다.
4. **인덱스 교집합 알고리즘 개선**: 현재 모든 인덱스 쿼리를 완료 후 교집합을 계산하는 방식에서, 가장 선택도(selectivity)가 높은 인덱스를 먼저 실행하고 결과를 다음 인덱스 쿼리의 필터로 활용하는 순차적 축소 방식으로 변경한다.

**예상 효과**:
- 복합 조건 검색 응답 시간 50-70% 감소
- Cassandra 클러스터 부하 분산 개선 (핫스팟 완화)
- UI 사용자 경험 대폭 개선

**구현 난이도**: 중~상 (스키마 마이그레이션은 Rolling 업그레이드 고려 필요, 캐싱은 중간 난이도)

---

### 3.3 Memory 스토리지의 인덱스 구조 및 동시성 개선

**문제 상세**: Memory 스토리지의 `FindTraces()`가 전체 트레이스 맵을 O(n) 순회하며, 단일 `sync.RWMutex`로 인해 읽기와 쓰기가 경합한다. `max_traces` 증가 시 성능이 선형적으로 저하된다.

**근본 원인**: Memory 스토리지가 단순 `map[traceID]ptrace.Traces` 구조만 유지하며, 서비스명/오퍼레이션명/태그에 대한 보조 인덱스가 없다. 또한 단일 뮤텍스로 전체 데이터 구조를 보호하므로 동시성이 제한된다.

**제안 솔루션**:
1. **보조 인메모리 인덱스 추가**: 서비스명 -> trace_id 집합, 오퍼레이션명 -> trace_id 집합의 역인덱스를 유지한다. `WriteTraces()` 시 Span의 `service.name` 리소스 속성과 Span Name을 추출하여 인덱스를 동시에 업데이트한다. `FindTraces()` 시 인덱스를 먼저 조회하여 후보 trace_id를 좁힌 후 상세 필터링을 수행한다.
2. **샤딩 기반 동시성 개선**: 단일 맵 대신 trace_id의 해시 값 기반으로 N개 샤드(예: 64개)로 분할한다. 각 샤드가 독립적인 `sync.RWMutex`를 가지므로, 서로 다른 샤드에 대한 읽기/쓰기가 병렬로 실행된다.
3. **시간 기반 만료 개선**: 현재 `max_traces` 초과 시 가장 오래된 트레이스를 제거하는 방식에 TTL 기반 만료를 추가하여, 설정된 시간이 지난 트레이스를 백그라운드에서 정리한다.

**예상 효과**:
- `FindTraces()` 응답 시간 O(n) -> O(k) (k = 인덱스 매칭 결과 수)
- 동시 읽기/쓰기 처리량 4-8배 향상 (샤드 수에 비례)
- All-in-One 개발 환경에서의 체감 성능 대폭 개선

**구현 난이도**: 중 (인메모리 인덱스는 비교적 간단, 샤딩은 일관성 관리에 주의 필요)

---

### 3.4 적응형 샘플링 반응 속도 및 확장성 개선

**문제 상세**: 적응형 샘플링의 확률 갱신 주기가 최대 2분(집계 1분 + 계산 1분)으로, 급격한 트래픽 변화에 대한 반응이 느리다. 리더 선출에 Cassandra LWT를 사용하여 성능 오버헤드가 크고, `weightvectorcache.go`의 서비스-오퍼레이션 캐시가 무한 증가할 수 있다.

**근본 원인**: `aggregator.go`의 집계 주기와 `post_aggregator.go`의 계산 주기가 독립적으로 설정되어 파이프라인 지연이 누적된다. 리더 선출이 스토리지 백엔드(Cassandra)에 강하게 결합되어 있으며, Elasticsearch와 ClickHouse는 `distributedlock.Lock`을 구현하지 않아 적응형 샘플링 자체를 지원하지 못한다.

**제안 솔루션**:
1. **이벤트 드리븐 확률 갱신**: 고정 주기 기반에서 변화 감지 기반으로 전환한다. 집계된 처리량이 이전 주기 대비 일정 비율(예: 20%) 이상 변화하면 즉시 확률 재계산을 트리거한다. 변화가 없으면 기존 주기를 유지하여 불필요한 계산을 방지한다.
2. **리더 선출 추상화 계층 강화**: `distributedlock.Lock` 구현을 스토리지 백엔드에서 분리하여, Kubernetes 환경에서는 Kubernetes Lease API, 독립 환경에서는 etcd 또는 내장 Raft 합의를 활용할 수 있도록 한다. 이를 통해 ES/ClickHouse 백엔드에서도 적응형 샘플링을 지원한다.
3. **서비스-오퍼레이션 캐시 LRU 전환**: `weightvectorcache.go`와 `cache.go`의 무한 성장 맵을 `internal/cache/lru.go`의 LRU 캐시로 교체한다. `maxSize`와 `TTL`을 설정하여 비활성 서비스-오퍼레이션 쌍을 자동으로 퇴출한다.
4. **로컬 우선 샘플링 결정**: 각 Collector 인스턴스가 자체 로컬 처리량 통계를 기반으로 임시 샘플링 결정을 내리고, 리더의 글로벌 확률이 갱신되면 점진적으로 수렴하는 2단계 방식을 도입한다.

**예상 효과**:
- 트래픽 변화 반응 시간 2분 -> 10-20초로 단축
- ES/ClickHouse 환경에서 적응형 샘플링 지원 확대
- 대규모 환경(수천 서비스-오퍼레이션 쌍)에서 메모리 사용량 안정화

**구현 난이도**: 상 (리더 선출 추상화는 분산 시스템 전문 지식 필요, 2단계 샘플링은 수렴 알고리즘 설계 필요)

---

## 4. 우선순위 및 로드맵

### Phase 1: 단기 (1-3개월) - 즉시 효과가 큰 개선

| 순위 | 항목 | 예상 공수 | 영향도 |
|------|------|----------|--------|
| 1 | Memory 스토리지 보조 인덱스 추가 (3.3-1) | 2주 | 개발 환경 체감 성능 대폭 개선 |
| 2 | FindTraces 쿼리 결과 LRU 캐싱 (3.2-3) | 2주 | 반복 검색 응답 즉시 개선 |
| 3 | 적응형 샘플링 캐시 LRU 전환 (3.4-3) | 1주 | 메모리 안정성 확보 |

### Phase 2: 중기 (3-6개월) - 핵심 아키텍처 개선

| 순위 | 항목 | 예상 공수 | 영향도 |
|------|------|----------|--------|
| 4 | Memory 스토리지 샤딩 동시성 개선 (3.3-2) | 3주 | 동시 처리량 향상 |
| 5 | Cassandra 인덱스 교집합 알고리즘 개선 (3.2-4) | 3주 | 복합 검색 성능 개선 |
| 6 | 이벤트 드리븐 샘플링 갱신 (3.4-1) | 4주 | 샘플링 정확도 향상 |
| 7 | ES v2 네이티브 Writer 구현 (3.1-1) | 6주 | Write 경로 성능 대폭 개선 |

### Phase 3: 장기 (6-12개월) - 근본적 구조 전환

| 순위 | 항목 | 예상 공수 | 영향도 |
|------|------|----------|--------|
| 8 | Cassandra v2 네이티브 Writer 구현 (3.1-2) | 8주 | Write/Read 전체 경로 최적화 |
| 9 | Cassandra 태그 인덱스 파티셔닝 개선 (3.2-2) | 6주 | 스키마 마이그레이션 포함 |
| 10 | 리더 선출 추상화 및 다중 백엔드 지원 (3.4-2) | 8주 | 적응형 샘플링 범용화 |
| 11 | 로컬 우선 2단계 샘플링 (3.4-4) | 6주 | 대규모 환경 최적화 |

---

## 5. 기대 성과

### 정량적 목표

| 지표 | 현재 추정 | 개선 후 목표 | 비고 |
|------|----------|-------------|------|
| Write 경로 CPU 사용률 | 기준값 100% | 65-80% | v2 네이티브 구현으로 변환 오버헤드 제거 |
| Cassandra 복합 검색 P99 지연 | 수 초 | 1초 미만 | 인덱스 개선 + 캐싱 |
| Memory 스토리지 FindTraces P50 | O(n) 선형 | O(k) 인덱스 기반 | 보조 인덱스 도입 |
| 적응형 샘플링 반응 시간 | ~120초 | ~15초 | 이벤트 드리븐 갱신 |
| 적응형 샘플링 지원 백엔드 | 3개 (Memory, Badger, Cassandra) | 5개 (+ ES, ClickHouse) | 리더 선출 추상화 |
| Memory 스토리지 동시 처리량 | 기준값 100% | 400-800% | 샤드 기반 동시성 |

### 정성적 효과

- **개발자 경험 개선**: All-in-One 모드에서의 빠른 피드백 루프로 Jaeger 채택 장벽 저하
- **프로덕션 안정성**: 샘플링 반응 속도 향상으로 중요 트레이스 누락률 감소
- **기술 부채 해소**: v1/v2 어댑터 계층 축소로 코드베이스 복잡도 감소 및 유지보수성 향상
- **생태계 확장**: ES/ClickHouse에서 적응형 샘플링 지원으로 더 넓은 운영 환경 커버리지
- **커뮤니티 기여 가치**: CNCF Graduated 프로젝트에 대한 실질적 성능 개선 기여로 오픈소스 참여 실적 확보
