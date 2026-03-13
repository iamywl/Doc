# Prometheus Alertmanager 구조 개선 계획서

## 1. 프로젝트 개요

Prometheus Alertmanager는 Prometheus 서버 등 클라이언트로부터 알림(Alert)을 수신하여 **중복 제거, 그룹핑, 라우팅, 전송**을 담당하는 독립 서비스이다. YAML 기반 선언적 설정, Stage 체인 기반 파이프라인 아키텍처, hashicorp/memberlist를 활용한 Gossip 기반 고가용성 클러스터링을 핵심 설계 원칙으로 한다.

현재 구조는 중소규모 환경에서 안정적으로 동작하지만, 대규모 엔터프라이즈 환경(수만 개 Alert, 수백 개 Silence, 복잡한 라우팅 트리)에서 성능 병목과 운영상 제약이 발생한다. 본 문서는 실제 코드 경로와 데이터 구조를 기반으로 구조적 한계점을 분석하고 구체적인 개선안을 제시한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 Notification Log(nflog) 선형 탐색 및 글로벌 락 병목

**현상**: nflog의 `state`는 `map[string]*pb.MeshEntry` 타입으로, `Query()` 호출 시 전체 맵을 `sync.RWMutex` 읽기 락 하에 순회한다. DedupStage에서 매 Aggregation Group flush마다 `nflog.Query(receiver, groupKey)`를 호출하므로, nflog 엔트리 수가 증가하면 flush 지연이 선형적으로 증가한다.

**코드 경로**: `nflog/nflog.go` > `Query()` > `state` 맵 순회. 키 형식이 `{groupKey}:{receiverGroupName}:{integrationName}:{integrationIdx}`이므로 직접 키 조회가 불가능하고, receiver와 groupKey를 조합한 prefix 매칭이 필요하다.

**영향 범위**: 대규모 환경에서 nflog 엔트리가 10,000개 이상이면, 각 flush의 DedupStage에서 수 밀리초의 추가 지연이 발생한다. 동시에 여러 Aggregation Group이 flush하면 `sync.RWMutex` 읽기 락 경합이 발생하며, `Log()` 호출(쓰기 락)이 대기한다.

**클러스터 증폭**: Push-Pull 교환(1분 간격)에서 `MarshalBinary()`가 전체 state를 Protobuf로 직렬화한다. nflog 10,000 엔트리 기준 약 2MB의 직렬화/역직렬화 비용이 매분 발생하며, `Merge()` 시에도 글로벌 쓰기 락을 획득해야 한다.

---

### 2.2 Silence 매칭의 글로벌 버전 기반 캐시 비효율

**현상**: `Silencer.Mutes()`는 버전 기반 캐시(`cache`)를 사용하여, `Silences.version`이 변경되면 캐시 미스가 발생하고 모든 활성 Silence와 Alert Labels를 다시 매칭한다. 단 하나의 Silence가 추가/삭제/만료되더라도 `version++`이 발생하여, 이후 모든 Alert에 대한 캐시가 점진적으로 무효화된다.

**코드 경로**: `silence/silence.go` > `Silencer.Mutes()` > `cache.Get(fp, silences.Version())` > 캐시 미스 시 `QueryActiveSilenceIDs(lset)` > 모든 활성 Silence의 `matcherIndex` 순회.

**최악 시나리오 복잡도**: O(A x S x M) — A = 파이프라인을 통과하는 Alert 수, S = 활성 Silence 수, M = Silence당 평균 Matcher 수. 활성 Silence 100개, 동시 flush Alert 1,000개 환경에서, Silence 하나가 변경되면 최대 100,000회의 Matcher 비교가 발생한다.

**클러스터 환경 악화**: 클러스터 모드에서 다른 인스턴스의 `Merge()`가 `version++`을 유발하므로, Gossip 메시지 수신 빈도(200ms 간격)에 비례하여 캐시 무효화가 빈번하게 발생한다.

---

### 2.3 Inhibitor의 규칙 수 비례 선형 순회 및 scache 동시성 제약

**현상**: `Inhibitor.Mutes()`는 모든 InhibitRule을 순차적으로 순회하며 `TargetMatchers.Matches(lset)`를 호출한다. `sindex`의 O(1) 조회 덕분에 Source Alert 매칭은 빠르지만, 규칙 자체의 TargetMatchers 매칭은 규칙 수에 비례한다.

**코드 경로**: `inhibit/inhibit.go` > `Mutes()` > `for _, rule := range ih.rules` > `rule.TargetMatchers.Matches(lset)` > `rule.hasMatchingSources(lset)`. 또한 `Run()` goroutine에서 `processAlert()`가 모든 규칙의 `SourceMatchers.Matches()`를 호출하므로, 새 Alert 수신 시에도 O(R) 비용이 발생한다 (R = 규칙 수).

**동시성 제약**: `ih.mtx` RWMutex로 `Run()`(쓰기)과 `Mutes()`(읽기)가 동기화된다. 대량 Alert가 동시에 수신되면 `processAlert()`의 쓰기 락이 `Mutes()`의 읽기 락과 경합하여 파이프라인 전체 지연이 증가한다.

**벤치마크 근거**: `inhibit/inhibit_bench_test.go`의 `BenchmarkMutes`에서 "10000 inhibition rules, last rule matches" 시나리오가 가장 느리며, 모든 규칙을 순회해야 하는 최악 경우를 보여준다.

---

### 2.4 Alert Provider 구독 채널의 고정 버퍼 및 드롭 정책

**현상**: `provider/mem/mem.go`에서 구독자 채널 버퍼 크기가 `alertChannelLength = 200`으로 하드코딩되어 있다. Put() 시 채널이 가득 차면 `select { default: }` 패턴으로 Alert를 드롭한다. 대량 Alert burst 상황에서 Dispatcher나 Inhibitor가 Alert를 놓칠 수 있다.

**코드 경로**: `provider/mem/mem.go` > `Put()` > 리스너 브로드캐스트 루프 > `select { case l.alerts <- providerAlert: ... default: // 드롭 }`.

**연쇄 영향**: Dispatcher가 Alert를 놓치면 해당 Alert는 다음 Prometheus scrape까지 라우팅되지 않는다. Inhibitor가 Source Alert를 놓치면 `scache`에 Source가 없어 Target Alert가 잘못 전송된다. 특히 설정 리로드 시 Dispatcher가 재시작되면서 `SlurpAndSubscribe()`로 전체 Alert를 다시 로드하는 동안, 새로 수신되는 Alert의 드롭 확률이 높아진다.

**모니터링 한계**: `alertmanager_alerts_subscriber_channel_writes_total{result="dropped"}` 메트릭으로 드롭을 감지할 수 있지만, 사후 대응만 가능하고 드롭 자체를 방지하는 메커니즘이 없다.

---

### 2.5 설정 리로드 시 전체 재구축으로 인한 서비스 중단 윈도우

**현상**: `Coordinator.Reload()` 시 Dispatcher, Inhibitor, Notification Pipeline이 모두 파괴되고 재생성된다. 기존 Aggregation Group은 전부 폐기되고, 모든 Alert가 새 Route 트리로 다시 라우팅된다.

**코드 경로**: `config/coordinator.go` > `Reload()` > `Dispatcher.Stop()` (ctx 취소, 모든 goroutine 종료 대기) > `NewDispatcher()` > `Run()` (SlurpAndSubscribe로 전체 Alert 재로드). Inhibitor도 동일하게 `Stop()` > `NewInhibitor()` > `Run()`.

**서비스 영향**: Dispatcher.Stop()에서 `d.finished.Wait()`로 모든 goroutine 종료를 대기하므로, 진행 중인 RetryStage(exponential backoff 최대 5분)가 있으면 최대 수분간 새 Alert 처리가 중단된다. 재시작 후 모든 Aggregation Group의 GroupWait 타이머가 리셋되어, 이미 그룹화되어 있던 Alert에 대해 불필요한 재전송이 발생할 수 있다.

---

## 3. 개선 제안

### 3.1 nflog 인덱싱 및 샤딩 도입

**문제 상세**: nflog의 `state` 맵이 단일 글로벌 구조로, Query 시 전체 순회가 필요하며 글로벌 RWMutex로 동시성이 제한된다. DedupStage의 매 flush마다 O(N) 탐색이 발생한다.

**근본 원인**: nflog의 키가 복합 문자열(`{groupKey}:{receiver}:{integration}:{idx}`)이어서 receiver나 groupKey 단독으로 직접 조회가 불가능하다. `Query()` 함수가 `QueryParam` 패턴으로 필터링 조건을 받지만, 인덱스 없이 전체 순회로 구현되어 있다.

**제안 솔루션**:

1. **복합 인덱스 도입**: `groupKey → []*pb.MeshEntry`와 `receiver → []*pb.MeshEntry`의 보조 인덱스 맵을 추가한다. `Log()` 시 보조 인덱스도 함께 업데이트하고, `Query()` 시 인덱스를 먼저 조회하여 후보 엔트리를 좁힌다.

```go
type Log struct {
    // 기존 필드
    st        state
    // 신규 인덱스
    byGroup   map[string]map[string]*pb.MeshEntry  // groupKey -> key -> entry
    byRecv    map[string]map[string]*pb.MeshEntry  // receiverKey -> key -> entry
}
```

2. **샤드 기반 락 분할**: groupKey의 해시값으로 N개 샤드로 분할하고, 각 샤드에 독립적인 RWMutex를 부여한다. 서로 다른 그룹의 flush가 동시에 nflog에 접근할 때 락 경합을 제거한다.

```go
type shardedState struct {
    shards [16]struct {
        mtx sync.RWMutex
        st  state
    }
}

func (s *shardedState) getShard(groupKey string) int {
    return int(fnv32(groupKey)) % len(s.shards)
}
```

3. **클러스터 동기화 최적화**: `MarshalBinary()`에서 변경된 엔트리만 delta 직렬화하여 Push-Pull 페이로드를 줄인다. `lastPushPullVersion`을 추적하여 이전 교환 이후 변경된 엔트리만 전송한다.

**예상 효과**: Query 복잡도 O(N) -> O(1) (인덱스 적중 시). 동시 flush의 락 경합 제거로 p99 지연 50% 이상 감소. Push-Pull 대역폭 80% 이상 절감.

**구현 난이도**: 중상 (nflog 내부 구조 변경, 클러스터 프로토콜 호환성 유지 필요)

---

### 3.2 Silence 캐시의 세분화된 무효화 전략

**문제 상세**: 글로벌 `version` 카운터로 인해 단일 Silence 변경이 모든 Alert의 캐시를 무효화한다. 대규모 환경에서 Silence 변경 빈도가 높으면 캐시 히트율이 급격히 하락한다.

**근본 원인**: `Silences.version`이 단일 정수값으로, 어떤 Silence가 변경되었는지 정보를 담지 못한다. 캐시 엔트리는 `(fingerprint, version) -> silenceIDs` 구조여서 version이 바뀌면 모든 엔트리가 stale이 된다.

**제안 솔루션**:

1. **Silence별 버전 추적**: 글로벌 version 대신 각 Silence의 `updatedAt` 타임스탬프를 캐시 키에 포함한다. 캐시 엔트리가 참조하는 Silence들의 updatedAt 최대값을 저장하고, 해당 Silence만 변경되었을 때만 무효화한다.

```go
type cacheEntry struct {
    silenceIDs     []string
    silenceVersions map[string]time.Time  // silenceID -> updatedAt
}

func (c *cache) isValid(entry cacheEntry, silences *Silences) bool {
    for id, ver := range entry.silenceVersions {
        if s, ok := silences.get(id); ok && s.UpdatedAt.After(ver) {
            return false  // 이 Silence가 변경됨
        }
    }
    // 새 Silence가 이 Alert에 매칭될 수 있는지도 확인 필요
    return silences.Version() == entry.globalVersion
}
```

2. **Bloom Filter 기반 사전 필터링**: 새로 추가/변경된 Silence의 Matcher에서 사용하는 레이블 이름을 Bloom Filter에 기록한다. Alert의 레이블 이름이 Bloom Filter에 없으면 해당 Alert의 캐시는 여전히 유효하다.

3. **matcherIndex 역방향 활용**: 현재 `matcherIndex`는 `silenceID -> MatcherSet` 매핑이다. 이를 `labelName -> Set[silenceID]`로 확장하여, 변경된 Silence의 레이블과 무관한 Alert의 캐시를 보존한다.

**예상 효과**: Silence 변경 시 캐시 무효화 범위를 관련 Alert로만 한정하여, 캐시 히트율 90% 이상 유지. MuteStage의 평균 지연 70% 감소.

**구현 난이도**: 중 (캐시 구조 변경, 기존 동작과의 호환성 검증 필요)

---

### 3.3 Inhibitor 규칙 인덱싱 및 락 세분화

**문제 상세**: `Mutes()`에서 모든 InhibitRule을 선형 순회하며 TargetMatchers를 비교한다. 규칙 수가 수백 개 이상이면 매 Alert마다 수백 회의 Matcher 비교가 발생한다. 또한 `ih.mtx` 글로벌 RWMutex가 `Run()`과 `Mutes()` 간 경합을 유발한다.

**근본 원인**: InhibitRule의 TargetMatchers에 대한 인덱스가 없어 모든 규칙을 순회해야 한다. `sindex`는 Equal 레이블에 대한 인덱스만 제공하고, TargetMatchers 자체의 빠른 필터링은 지원하지 않는다.

**제안 솔루션**:

1. **TargetMatchers 레이블 인덱스**: TargetMatchers에서 사용하는 레이블 이름을 인덱스하여, Alert의 레이블과 교집합이 있는 규칙만 후보로 선별한다.

```go
type Inhibitor struct {
    // 기존 필드
    rules  []*InhibitRule
    // 신규 인덱스: 레이블 이름 -> 해당 레이블을 TargetMatcher에서 사용하는 규칙 인덱스
    targetLabelIndex map[model.LabelName][]*InhibitRule
}

func (ih *Inhibitor) Mutes(ctx context.Context, lset model.LabelSet) bool {
    // Alert의 레이블 이름으로 관련 규칙만 조회
    candidateRules := ih.getCandidateRules(lset)
    for _, rule := range candidateRules {
        if rule.TargetMatchers.Matches(lset) && rule.hasMatchingSources(lset) {
            return true
        }
    }
    return false
}
```

2. **규칙별 독립 락**: 글로벌 `ih.mtx` 대신 각 InhibitRule의 `scache`와 `sindex`에 이미 존재하는 개별 `sync.RWMutex`를 활용한다. `Run()`에서 규칙별로 독립적으로 업데이트하고, `Mutes()`에서도 규칙별 락으로 접근한다.

3. **processAlert 비동기 배치 처리**: 개별 Alert마다 모든 규칙을 순회하는 대신, Alert를 배치로 모아서 한 번에 처리한다. 각 규칙의 SourceMatchers를 Alert 배치에 대해 벡터화하여 비교한다.

**예상 효과**: 규칙 수 100개 기준, Mutes() 호출당 평균 후보 규칙 수를 5-10개로 줄여 90% 이상의 불필요한 매칭을 제거. 글로벌 락 제거로 동시성 처리량 3-5배 향상.

**구현 난이도**: 중 (인덱스 추가는 비교적 단순, 글로벌 락 제거 시 동시성 검증 필요)

---

### 3.4 적응형 구독 채널 및 백프레셔 메커니즘

**문제 상세**: Alert Provider의 구독 채널이 200 고정 버퍼로, burst 상황에서 Alert가 드롭된다. 드롭된 Alert는 Dispatcher/Inhibitor에서 처리되지 않아 알림 누락 또는 잘못된 억제가 발생할 수 있다.

**근본 원인**: `alertChannelLength = 200` 상수가 하드코딩되어 환경에 따른 조정이 불가능하다. 드롭 정책(`select { default: }`)은 시스템 안정성을 위한 설계이지만, Alert 신뢰성을 희생한다.

**제안 솔루션**:

1. **링 버퍼 기반 구독 채널**: Go 채널 대신 lock-free 링 버퍼를 사용하여, 버퍼가 가득 차면 가장 오래된 Alert를 덮어쓴다. 최신 Alert가 항상 처리되도록 보장한다.

```go
type RingBuffer struct {
    items  []*provider.Alert
    head   atomic.Int64
    tail   atomic.Int64
    size   int
    dropped atomic.Int64  // 메트릭용
}

func (rb *RingBuffer) Push(alert *provider.Alert) {
    // lock-free CAS 기반 push
    // 가득 차면 tail 전진 (가장 오래된 항목 덮어쓰기)
}
```

2. **동적 버퍼 크기 조정**: 채널 사용률 메트릭을 기반으로 버퍼 크기를 런타임에 조정한다. 사용률이 80%를 초과하면 2배로 확장하고, 20% 미만이면 축소한다.

3. **백프레셔 전파**: 구독 채널이 임계치(90%)에 도달하면 API 계층에 시그널을 보내어 새 Alert 수신 속도를 제한한다. HTTP 429 또는 지연 응답으로 Prometheus의 전송 속도를 조절한다.

4. **설정 가능한 버퍼 크기**: `--alerts.channel-capacity` 플래그를 추가하여 운영 환경에 맞게 버퍼 크기를 조정할 수 있도록 한다.

**예상 효과**: Alert 드롭율 0%에 근접. burst 상황에서도 최신 Alert가 항상 처리됨을 보장. 백프레셔로 시스템 전체의 안정성 유지.

**구현 난이도**: 중하 (링 버퍼 구현 자체는 단순, 기존 채널 기반 인터페이스와의 호환성 유지 필요)

---

### 3.5 Graceful 설정 리로드 (Hot-Swap 방식)

**문제 상세**: 설정 리로드 시 Dispatcher, Inhibitor, Pipeline이 완전히 파괴되고 재생성된다. 진행 중인 알림 처리가 중단되고, Aggregation Group 타이머가 리셋되어 불필요한 재전송이 발생한다.

**근본 원인**: `Coordinator.Reload()`가 기존 컴포넌트를 Stop()한 후 새 컴포넌트를 생성하는 "stop-then-start" 패턴을 사용한다. Dispatcher.Stop()에서 `d.finished.Wait()`로 모든 goroutine 종료를 기다리므로, RetryStage의 backoff 대기 시간만큼 중단이 길어진다.

**제안 솔루션**:

1. **Dispatcher Route 트리 Hot-Swap**: Dispatcher를 재생성하지 않고, Route 트리만 atomic하게 교체한다. 기존 Aggregation Group 중 새 Route 트리에서도 유효한 그룹은 보존하고, 변경된 Route만 재매핑한다.

```go
type Dispatcher struct {
    route atomic.Pointer[Route]  // 원자적 교체 가능
    // ...
}

func (d *Dispatcher) SwapRoute(newRoute *Route) {
    oldRoute := d.route.Swap(newRoute)
    // 기존 Aggregation Group을 새 Route에 재매핑
    d.remapGroups(oldRoute, newRoute)
}
```

2. **Pipeline Drain-then-Switch**: 새 Pipeline을 먼저 구축한 후, 기존 Pipeline에서 진행 중인 처리가 완료되면 새 Pipeline으로 전환한다. 이중 Pipeline 구조로 zero-downtime 전환을 달성한다.

```go
type PipelineManager struct {
    current atomic.Pointer[notify.Stage]
    draining atomic.Pointer[notify.Stage]
}

func (pm *PipelineManager) Swap(newStage notify.Stage) {
    old := pm.current.Swap(&newStage)
    pm.draining.Store(old)
    // 진행 중인 처리 완료 대기 후 old 정리
}
```

3. **Inhibitor 규칙 증분 업데이트**: 변경된 InhibitRule만 교체하고, 변경되지 않은 규칙의 scache와 sindex는 보존한다. 규칙의 해시값을 비교하여 변경 여부를 판단한다.

**예상 효과**: 설정 리로드 시 서비스 중단 시간 0에 근접. 기존 Aggregation Group 보존으로 불필요한 재전송 방지. GroupWait 타이머 리셋 없이 연속적인 알림 전송.

**구현 난이도**: 상 (Dispatcher, Pipeline, Inhibitor 모두 수정 필요, 동시성 모델의 근본적 변경)

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 난이도 | 예상 기간 | 영향도 |
|---------|----------|--------|----------|--------|
| **P0** | 3.1 nflog 인덱싱 및 샤딩 | 중상 | 4-6주 | 높음 — DedupStage 성능 직접 개선 |
| **P0** | 3.4 적응형 구독 채널 | 중하 | 2-3주 | 높음 — Alert 신뢰성 직접 개선 |
| **P1** | 3.2 Silence 캐시 세분화 | 중 | 3-4주 | 중상 — MuteStage 성능 개선 |
| **P1** | 3.3 Inhibitor 규칙 인덱싱 | 중 | 3-4주 | 중 — 규칙이 많은 환경에서 효과 |
| **P2** | 3.5 Graceful 설정 리로드 | 상 | 6-8주 | 중 — 운영 안정성 개선 |

### Phase 1 (1-2개월): 핵심 성능 병목 해소

- nflog 복합 인덱스 도입 및 Query 최적화
- 구독 채널 링 버퍼 전환 및 설정 가능한 버퍼 크기 도입
- 성능 벤치마크 구축 (개선 전후 비교 기준선)

### Phase 2 (2-3개월): 캐시 및 인덱싱 최적화

- Silence 캐시 세분화된 무효화 전략 적용
- Inhibitor TargetMatchers 레이블 인덱스 도입
- nflog 샤드 기반 락 분할
- 클러스터 환경 성능 검증

### Phase 3 (3-5개월): 아키텍처 개선

- Graceful 설정 리로드 (Route 트리 Hot-Swap)
- Pipeline Drain-then-Switch 구현
- Inhibitor 증분 업데이트
- 전체 통합 테스트 및 안정성 검증

---

## 5. 기대 성과

### 5.1 정량적 성과

| 지표 | 현재 (추정) | 개선 후 목표 |
|------|------------|------------|
| DedupStage Query 지연 (nflog 10K 엔트리) | ~5-10ms | <0.1ms |
| Silence 변경 후 캐시 히트율 | <10% (변경 직후) | >90% |
| Inhibitor Mutes() 지연 (100 규칙) | ~2-5ms | <0.5ms |
| Alert 드롭율 (burst 시) | 환경 의존 (>0%) | ~0% |
| 설정 리로드 중단 시간 | 수초~수분 | <100ms |
| nflog Push-Pull 페이로드 크기 | ~2MB (10K 엔트리) | <400KB (delta) |

### 5.2 정성적 성과

- **운영 안정성**: Alert 드롭 방지 및 설정 리로드 시 무중단 전환으로 SLA 향상
- **확장성**: 대규모 환경(수만 Alert, 수백 Silence, 수백 Inhibit Rule)에서도 안정적 성능
- **클러스터 효율성**: Gossip 대역폭 절감으로 네트워크 비용 감소 및 동기화 신뢰성 향상
- **오픈소스 기여**: 업스트림 PR을 통한 커뮤니티 기여 및 조직의 기술 리더십 확보
