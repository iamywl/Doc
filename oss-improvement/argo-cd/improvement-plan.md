# Argo CD 구조적 개선 계획서

## 1. 프로젝트 개요

Argo CD는 CNCF Graduated 프로젝트로, Git 저장소를 단일 진실 공급원(Single Source of Truth)으로 삼아 Kubernetes 클러스터의 실제 상태를 선언된 원하는 상태와 지속적으로 일치시키는 GitOps 기반 지속적 배포 도구이다.

핵심 컴포넌트는 다음과 같다:
- **API Server** (`server/server.go`): UI/CLI/gRPC 요청의 프론트엔드 게이트웨이
- **Application Controller** (`controller/appcontroller.go`, 2,697줄): GitOps 조정 루프(reconciliation loop) 구동
- **Repo Server** (`reposerver/repository/repository.go`, 3,276줄): Git clone, Helm template, Kustomize build 등 매니페스트 생성 전담
- **GitOps Engine** (`gitops-engine/pkg/`): Diff, Sync, Health 평가의 독립 라이브러리
- **ApplicationSet Controller**: 단일 템플릿으로 다수 Application 자동 생성/관리

본 문서는 대규모 환경(1,000개 이상 Application, 100개 이상 클러스터)에서 발견되는 구조적 한계점을 분석하고, 실행 가능한 개선 방안을 제시한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 Application Controller의 단일 워크큐 병목 및 O(n) 리소스 순회

**영향 범위**: `controller/appcontroller.go` L.886-967의 `Run()` 함수

Application Controller는 `appRefreshQueue`와 `appOperationQueue`라는 두 개의 rate-limited 워크큐를 중심으로 동작한다. `statusProcessors` (기본 20개)와 `operationProcessors` (기본 10개) 고루틴이 이 큐를 소비한다.

문제는 `processAppRefreshQueueItem()` 내부의 `CompareAppState()` 호출이다. `controller/state.go` L.551-1042에 구현된 이 10단계 파이프라인은 단일 Application당 다음을 순차 수행한다:

1. `GetRepoObjs()` — repo-server gRPC 호출 (네트워크 I/O)
2. `liveStateCache.GetManagedLiveObjs()` — 클러스터 캐시에서 live 객체 조회
3. `sync.Reconcile()` — target/live 페어링
4. `argodiff.StateDiffs()` — Three-way diff 계산
5. `setApplicationHealth()` — 헬스 평가

이 파이프라인이 20개 고루틴에서 병렬 실행되지만, `clusterCache.lock` (sync.RWMutex)에서 경합이 발생한다. `GetManagedLiveObjs()`와 `FindResources()`가 모두 이 RWMutex의 읽기 잠금을 획득해야 하며, Watch 이벤트 처리 시 쓰기 잠금이 발생하면 모든 읽기가 블로킹된다.

또한 `needRefreshAppStatus()`에서 `reflect.DeepEqual()`을 사용한 비교(`app.Spec.Destination` vs `app.Status.Sync.ComparedTo.Destination`)가 매 조정 주기마다 수행되어, 대규모 Application 목록에서 GC 압력을 높인다.

### 2.2 Repo Server의 Git Clone/Fetch 중복 및 repoLock 직렬화

**영향 범위**: `reposerver/repository/repository.go` L.322-525 `runRepoOperation()`

Repo Server의 동시성 제어는 3계층으로 구성된다:
- `parallelismLimitSemaphore` (전역 세마포어)
- `repoLock` (Git 저장소별 잠금)
- `manifestGenerateLock` (앱 경로별 잠금)

핵심 병목은 `repoLock`이다. 동일 Git 저장소를 참조하는 수백 개 Application이 있을 때, `repoLock.Lock(gitClient.Root(), revision, allowConcurrent, checkoutFn)` 호출에서 체크아웃 작업이 직렬화된다. `allowConcurrent=true`인 읽기 작업도 체크아웃 중에는 블로킹된다.

더 심각한 문제는 Git 저장소의 로컬 클론 관리 방식이다. `utilio.TempPaths`가 URL을 UUID 기반 경로에 매핑하므로, 같은 저장소의 서로 다른 revision을 동시에 체크아웃할 수 없다. Monorepo 환경에서 하나의 Git 저장소에 50개 Application이 서로 다른 branch를 참조하면, 각 체크아웃이 순차적으로 대기해야 한다.

`checkoutRevision()` 내부에서는 `gitClient.Fetch("", depth)` 후 `gitClient.Checkout(revision)`을 수행하는데, fetch가 실패하면 `Fetch(revision)` + `Checkout("FETCH_HEAD")` 폴백이 발생하여 네트워크 요청이 2배로 증가한다.

### 2.3 ClusterCache의 전체 재동기화(Full Resync)로 인한 메모리 스파이크

**영향 범위**: `gitops-engine/pkg/cache/cluster.go`의 `clusterCache` 구조체

ClusterCache는 Kubernetes Watch API 기반으로 실시간 업데이트를 유지하지만, `defaultClusterResyncTimeout = 24 * time.Hour` 주기로 전체 리소스를 List API로 재로드한다. 이때 `listPageSize = 500`, `listSemaphore` 가중치 50으로 동시 List 요청이 제한되어 있지만, 대규모 클러스터(10,000개 이상 리소스)에서 전체 재동기화 시 메모리 사용량이 급격히 증가한다.

문제의 핵심은 `resources map[kube.ResourceKey]*Resource`와 `nsIndex map[string]map[kube.ResourceKey]*Resource` 두 개의 인덱스가 동시에 유지되는 구조다. 전체 재동기화 시 새로운 리소스 맵을 구축하고 기존 맵을 교체하는 과정에서 순간적으로 2배의 메모리가 필요하다.

`parentUIDToChildren map[types.UID][]kube.ResourceKey` 인덱스는 O(1) 트리 순회를 가능하게 하지만, `IterateHierarchyV2`에서 재귀 순회 시 visited 집합을 매번 새로 생성하여 GC 부담을 가중시킨다.

Watch 이벤트 처리의 `eventProcessingInterval = 100ms` 배치 처리도 문제가 될 수 있다. 대량의 이벤트가 한꺼번에 도착하면(예: Deployment rollout 시 수백 개 Pod 이벤트) 배치 크기가 무제한으로 증가하여 처리 지연이 발생한다.

### 2.4 Redis 캐시의 gob 인코딩 비효율 및 TwoLevelClient 동일성 검사 오버헤드

**영향 범위**: `util/cache/twolevelclient.go`, `util/cache/inmemory.go`

TwoLevelClient 패턴은 Redis 트래픽 최소화를 위해 `InMemoryCache.HasSame()`에서 gob 인코딩 후 바이트 비교를 수행한다:

```go
func (i *InMemoryCache) HasSame(key string, obj any) (bool, error) {
    var buf bytes.Buffer
    err := gob.NewEncoder(&buf).Encode(obj)
    // ...
    return bytes.Equal(buf.Bytes(), existingBuf.Bytes()), nil
}
```

매 `Set()` 호출마다 객체 전체를 gob으로 직렬화하여 바이트 비교한다. Application Controller가 매 reconcile 주기마다 `SetAppResourcesTree()`를 호출하고, 리소스 트리가 수백 개 노드를 포함하면, 이 직렬화/비교 비용이 상당하다. gob 인코더는 reflection 기반이므로 구조체 필드 수에 비례하여 느려진다.

리소스 트리 샤딩(`GetShards(treeShardSize)`)이 적용되어 있지만, 각 샤드별로 `HasSame()`이 호출되므로 전체 직렬화 횟수는 오히려 증가한다. 샤드 수가 10개이면 gob 인코딩이 10회 수행된다.

또한 Redis 캐시 키 구성이 `repo URL + source path + revision + ApplicationSource 설정 + refSources + Namespace + TrackingMethod + AppLabelKey + AppName + InstallationID`로 매우 길어, Redis 메모리 사용량과 키 비교 비용이 높다.

---

## 3. 개선 제안

### 3.1 Application Controller 워크큐 샤딩 및 잠금 경합 해소

#### 문제 상세
`processAppRefreshQueueItem()`의 20개 statusProcessor 고루틴이 `clusterCache.lock` RWMutex에서 경합한다. Watch 이벤트에 의한 쓰기 잠금이 발생하면 모든 읽기 고루틴이 블로킹되어, 1,000개 Application 환경에서 reconcile 지연이 수십 초까지 증가할 수 있다.

#### 근본 원인
- `clusterCache`가 단일 RWMutex로 전체 리소스 맵을 보호하는 coarse-grained locking 설계
- `GetManagedLiveObjs()`가 리소스 맵 전체에 대한 읽기 잠금을 보유한 상태에서 추가 필터링 수행
- `needRefreshAppStatus()`의 `reflect.DeepEqual()` 호출이 불필요한 메모리 할당 유발

#### 제안 솔루션

**A) 네임스페이스별 분할 잠금(Sharded Lock) 도입**

`clusterCache`의 `resources` 맵을 네임스페이스별로 분할하고, 각 샤드에 독립 RWMutex를 부여한다:

```go
type shardedResourceMap struct {
    shards    [256]*resourceShard
    hashFunc  func(namespace string) uint8
}

type resourceShard struct {
    mu        sync.RWMutex
    resources map[kube.ResourceKey]*Resource
}
```

`GetManagedLiveObjs()`가 특정 네임스페이스의 리소스만 필요한 경우, 해당 샤드의 읽기 잠금만 획득하면 된다. 클러스터 스코프 리소스는 별도 샤드(인덱스 0)에 저장한다.

**B) `reflect.DeepEqual` 제거 및 해시 기반 변경 감지**

`needRefreshAppStatus()`에서 `reflect.DeepEqual(app.Spec.Destination, app.Status.Sync.ComparedTo.Destination)` 대신, Application 스펙의 변경을 `resourceVersion` 또는 해시값으로 추적:

```go
type appSpecHash struct {
    destinationHash    uint64
    sourceHash         uint64
    ignoreDiffHash     uint64
}
```

매 reconcile 시 DeepEqual 대신 해시 비교로 변경 여부를 O(1)에 판단한다.

#### 예상 효과
- 잠금 경합 70-80% 감소 (네임스페이스별 분할로 동시 읽기 가능)
- `needRefreshAppStatus()` CPU 사용량 50% 이상 감소
- 1,000개 Application 환경에서 reconcile 지연 3-5초 이내로 개선

#### 구현 난이도: **상** (ClusterCache 인터페이스 변경 필요, GitOps Engine 모듈 수정)

---

### 3.2 Repo Server Git 저장소 풀링(Pooling) 및 Bare Repository 기반 아키텍처

#### 문제 상세
동일 Git 저장소를 참조하는 N개 Application이 서로 다른 revision을 요구할 때, `repoLock`에 의해 체크아웃이 직렬화된다. Monorepo에서 50개 Application이 5개 branch를 사용하면, 한 번에 하나의 branch만 체크아웃되어 대기 시간이 선형 증가한다.

#### 근본 원인
- `utilio.TempPaths`가 Git URL당 단일 작업 디렉토리만 관리하는 1:1 매핑 구조
- `checkoutRevision()`이 working tree 체크아웃을 수행하므로 동시 다중 revision 접근 불가
- `repoLock`이 체크아웃 중 전체 저장소 접근을 차단

#### 제안 솔루션

**A) Bare Repository + Git Worktree 풀 도입**

Git bare repository를 중앙 캐시로 유지하고, 필요 시 `git worktree add`로 revision별 독립 작업 디렉토리를 생성하는 풀링 구조:

```
rootDir/
  ├── bare/
  │   └── {repo-hash}/          ← bare clone (fetch만 수행)
  │       ├── objects/
  │       └── refs/
  └── worktrees/
      ├── {repo-hash}-{rev-1}/  ← worktree (읽기 전용)
      ├── {repo-hash}-{rev-2}/  ← worktree (읽기 전용)
      └── pool-config.json      ← 풀 크기, TTL 설정
```

Fetch는 bare repository에 대해서만 수행(단일 잠금)하고, 여러 worktree가 동시에 서로 다른 revision을 읽을 수 있다. worktree는 풀에서 관리하여 재사용하며, LRU 정책으로 오래된 것부터 정리한다.

**B) Git Partial Clone / Sparse Checkout 활용**

대형 Monorepo에서 `manifest-generate-paths` 어노테이션이 설정된 경우, sparse checkout으로 필요한 디렉토리만 가져오도록 개선:

```go
func (c *gitClient) SparseCheckout(revision string, paths []string) error {
    // git sparse-checkout set <paths>
    // git checkout <revision>
}
```

#### 예상 효과
- 동일 저장소 다중 revision 동시 접근 가능 (직렬 대기 제거)
- Monorepo 환경에서 매니페스트 생성 시간 5-10배 단축
- 네트워크 fetch 횟수 절반 이하로 감소 (bare repo가 이미 최신이면 fetch 스킵)

#### 구현 난이도: **중상** (Git 클라이언트 추상화 계층 재설계 필요, 하위 호환성 유지 필요)

---

### 3.3 ClusterCache 증분 재동기화(Incremental Resync) 및 메모리 최적화

#### 문제 상세
24시간 주기 전체 재동기화 시 대규모 클러스터에서 메모리 사용량이 2배로 급증한다. 10,000개 리소스 클러스터에서 Resource 구조체 하나당 평균 2KB로 계산하면, 재동기화 시 약 40MB의 추가 메모리가 순간적으로 필요하다. 100개 클러스터를 관리하면 4GB 메모리 스파이크가 발생한다.

#### 근본 원인
- 전체 재동기화가 atomic swap 방식으로 구현되어, 새 맵 구축 완료까지 기존 맵이 해제되지 않음
- `IterateHierarchyV2()`에서 visited 집합(`map[kube.ResourceKey]bool`)이 매 호출마다 힙 할당
- Watch 이벤트 배치 처리 시 배치 크기 상한이 없어 버스트 시 처리 지연

#### 제안 솔루션

**A) Resource Version 기반 증분 재동기화**

전체 재동기화 대신, Kubernetes의 `resourceVersion`을 활용한 증분 방식:

```go
func (c *clusterCache) incrementalResync() error {
    for _, apiResource := range c.apisMeta {
        // List with resourceVersion=0 (캐시에서 읽기)
        // Watch 이벤트 누락 감지 시에만 해당 리소스 타입만 재동기화
        list, err := c.kubectl.ListResources(apiResource,
            metav1.ListOptions{ResourceVersion: c.lastResourceVersion})
    }
}
```

Watch 연결이 끊어진 리소스 타입만 선택적으로 재동기화하면, 전체 메모리 스파이크를 방지할 수 있다.

**B) visited 집합의 sync.Pool 활용**

`IterateHierarchyV2()`에서 사용하는 visited 맵을 `sync.Pool`로 재사용:

```go
var visitedPool = sync.Pool{
    New: func() interface{} {
        return make(map[kube.ResourceKey]bool, 64)
    },
}

func (c *clusterCache) IterateHierarchyV2(keys []kube.ResourceKey, action func(...) bool) {
    visited := visitedPool.Get().(map[kube.ResourceKey]bool)
    defer func() {
        clear(visited) // Go 1.21+
        visitedPool.Put(visited)
    }()
    // ...
}
```

**C) Watch 이벤트 배치 크기 상한 도입**

```go
const maxEventBatchSize = 500

func (c *clusterCache) processEvents() {
    batch := make([]*event, 0, maxEventBatchSize)
    // eventProcessingInterval(100ms) 내에서 최대 maxEventBatchSize까지만 수집
    // 나머지는 다음 배치로 이월
}
```

#### 예상 효과
- 전체 재동기화 시 메모리 스파이크 90% 감소 (증분 방식)
- `IterateHierarchyV2()` GC 압력 60-70% 감소 (sync.Pool)
- Watch 이벤트 버스트 시 처리 지연 안정화

#### 구현 난이도: **중** (증분 재동기화는 Watch 안정성 보장 필요, sync.Pool은 즉시 적용 가능)

---

### 3.4 캐시 직렬화 방식 교체 및 해시 기반 변경 감지

#### 문제 상세
TwoLevelClient의 `HasSame()`이 매번 gob 인코딩으로 전체 객체를 직렬화한 후 바이트 비교를 수행한다. `SetAppResourcesTree()`가 샤드별로 호출되면, 10개 샤드 x gob 인코딩 = 10회 직렬화가 발생한다. gob은 reflection 기반이므로 protobuf이나 msgpack 대비 5-10배 느리다.

#### 근본 원인
- `InMemoryCache`가 gob 인코딩된 `bytes.Buffer`를 값으로 저장하여 동일성 비교에 사용
- 캐시 키가 다수의 필드를 연결한 긴 문자열이라 Redis 메모리 효율이 낮음
- 리소스 트리 전체를 직렬화하는 것이 아니라 변경 여부만 알면 되는 경우에도 전체 직렬화 수행

#### 제안 솔루션

**A) xxHash 기반 변경 감지**

전체 직렬화 대신 경량 해시 함수로 변경 여부를 판단:

```go
import "github.com/cespare/xxhash/v2"

type InMemoryCache struct {
    memCache *gocache.Cache
    hashMap  sync.Map  // key -> uint64 (xxhash)
}

func (i *InMemoryCache) HasSame(key string, obj any) (bool, error) {
    h := xxhash.New()
    enc := gob.NewEncoder(h)  // hash writer에 직접 인코딩 (버퍼 할당 없음)
    enc.Encode(obj)
    newHash := h.Sum64()

    if existing, ok := i.hashMap.Load(key); ok {
        return existing.(uint64) == newHash, nil
    }
    return false, nil
}
```

이 방식은 `bytes.Buffer` 할당을 제거하고, 기존 캐시값의 역직렬화 없이 해시만 비교한다.

**B) 직렬화 포맷을 msgpack으로 교체**

gob → msgpack 교체로 직렬화/역직렬화 성능 향상:

```go
import "github.com/vmihailenco/msgpack/v5"

func (i *InMemoryCache) Set(item *Item) error {
    data, err := msgpack.Marshal(item.Object)
    // msgpack은 gob 대비 2-3배 빠르고 출력 크기도 작음
}
```

**C) Redis 캐시 키 해싱**

긴 캐시 키를 SHA-256 해시로 축약하되, 해시 충돌 검증을 위해 원본 키를 값 내부에 저장:

```go
func manifestCacheKey(components ...string) string {
    h := sha256.New()
    for _, c := range components {
        h.Write([]byte(c))
        h.Write([]byte{0}) // 구분자
    }
    return "mfst:" + hex.EncodeToString(h.Sum(nil))[:32]
}
```

#### 예상 효과
- `HasSame()` CPU 사용량 70-80% 감소 (해시 비교만 수행)
- 메모리 할당 50% 이상 감소 (bytes.Buffer 제거)
- Redis 키 크기 80% 축소 (해시화), 메모리 절약

#### 구현 난이도: **하~중** (HasSame 해시화는 즉시 적용 가능, msgpack 교체는 하위 호환성 고려 필요)

---

### 3.5 ApplicationSet Reconciler의 비효율적 전체 재생성 문제

#### 문제 상세
`applicationset/controllers/applicationset_controller.go`의 `Reconcile()` 함수는 매 조정 주기마다 모든 Generator를 실행하여 전체 Application 목록을 재생성한 뒤, 기존 Application 목록과 diff하여 생성/수정/삭제를 결정한다. 1,000개 클러스터에 대한 Cluster Generator가 매번 1,000개 Application 스펙을 생성하고 비교하는 것은 심각한 낭비다.

#### 근본 원인
- Generator 결과를 캐싱하지 않고 매번 전체 재생성
- `createOrGetField()` 기반 템플릿 렌더링이 매 Application마다 독립 수행
- Progressive Syncs의 `RollingSync` 전략이 매 조정 시 전체 Application 상태를 순회

#### 제안 솔루션

**A) Generator 결과 캐싱 및 증분 diff**

Generator 실행 결과를 해시와 함께 캐싱하고, 입력(클러스터 목록, Git 디렉토리 등)이 변경된 Generator만 재실행:

```go
type generatorCache struct {
    inputHash  uint64
    params     []map[string]interface{}
    generatedAt time.Time
}
```

**B) 템플릿 렌더링 결과의 batch 처리**

공통 템플릿 부분을 한 번만 파싱하고, 파라미터만 교체하여 Application 스펙을 생성:

```go
// 템플릿을 한 번 컴파일
compiled := template.Must(template.New("app").Parse(appSetTemplate))
// 파라미터별로 Execute만 반복
for _, params := range generatorResults {
    compiled.Execute(&buf, params)
}
```

#### 예상 효과
- ApplicationSet 조정 시간 80% 이상 단축 (변경된 Generator만 재실행)
- 대규모 ApplicationSet(1,000+ Application) 환경에서 API Server 부하 대폭 감소

#### 구현 난이도: **중** (Generator 인터페이스에 캐시 레이어 추가 필요)

---

## 4. 우선순위 및 로드맵

### Phase 1: 즉시 적용 가능한 최적화 (1-2개월)

| 순번 | 개선 항목 | 난이도 | 기대 효과 |
|------|-----------|--------|-----------|
| 1 | 3.4-A: HasSame() xxHash 기반 변경 감지 | 하 | CPU 70% 감소 |
| 2 | 3.3-B: IterateHierarchyV2 sync.Pool 적용 | 하 | GC 60% 감소 |
| 3 | 3.1-B: reflect.DeepEqual 제거, 해시 기반 비교 | 하 | CPU 50% 감소 |
| 4 | 3.3-C: Watch 이벤트 배치 크기 상한 도입 | 하 | 안정성 향상 |

### Phase 2: 중기 구조 개선 (3-6개월)

| 순번 | 개선 항목 | 난이도 | 기대 효과 |
|------|-----------|--------|-----------|
| 5 | 3.4-C: Redis 캐시 키 해싱 | 중 | 메모리 80% 절약 |
| 6 | 3.5: ApplicationSet Generator 캐싱 | 중 | 조정 80% 단축 |
| 7 | 3.3-A: 증분 재동기화 도입 | 중 | 메모리 스파이크 90% 감소 |
| 8 | 3.4-B: msgpack 직렬화 교체 | 중 | 직렬화 2-3배 빠름 |

### Phase 3: 장기 아키텍처 개선 (6-12개월)

| 순번 | 개선 항목 | 난이도 | 기대 효과 |
|------|-----------|--------|-----------|
| 9 | 3.2: Bare Repository + Worktree 풀 | 중상 | 동시성 5-10배 향상 |
| 10 | 3.1-A: ClusterCache 네임스페이스별 분할 잠금 | 상 | 잠금 경합 70% 감소 |

---

## 5. 기대 성과

### 정량적 성과 목표

| 지표 | 현재 (추정) | 개선 후 (목표) | 개선율 |
|------|------------|---------------|--------|
| 1,000 App reconcile 완료 시간 | 60-120초 | 10-20초 | 80% 개선 |
| Repo Server 동일 저장소 동시 처리 | 1 (직렬) | 5-10 (병렬) | 5-10배 |
| 전체 재동기화 시 메모리 스파이크 | 기존 대비 2배 | 기존 대비 1.1배 | 90% 감소 |
| TwoLevelClient HasSame() CPU | 높음 | 낮음 | 70-80% 감소 |
| ApplicationSet 1,000 App 조정 시간 | 수십 초 | 수 초 | 80% 단축 |
| Redis 캐시 키 메모리 사용량 | 기준 | 기준 대비 20% | 80% 절약 |

### 정성적 성과

1. **운영 안정성 향상**: 메모리 스파이크 감소로 OOM 발생 확률 대폭 낮아짐. Watch 이벤트 배치 상한으로 버스트 트래픽 시에도 안정적 동작.

2. **Monorepo 친화성 강화**: Bare Repository + Worktree 풀 아키텍처로 대형 Monorepo에서도 빠른 매니페스트 생성 가능. 기존 직렬 대기 문제 해소.

3. **대규모 환경 확장성**: ClusterCache 분할 잠금과 증분 재동기화로 수천 개 Application과 수백 개 클러스터 환경에서도 선형적 성능 유지.

4. **커뮤니티 기여 가치**: 위 개선 사항은 모두 Argo CD 업스트림에 기여 가능한 형태이며, 특히 Phase 1의 최적화들은 코드 변경 범위가 작아 PR 수용 가능성이 높음.

5. **GitOps Engine 독립 모듈 개선**: ClusterCache 최적화는 gitops-engine 모듈에 직접 기여하는 형태이므로, Argo CD 외의 GitOps 도구에도 혜택이 돌아감.
