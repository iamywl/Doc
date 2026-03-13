# Kubernetes 오픈소스 개선 계획서

## 1. 프로젝트 개요

Kubernetes는 선언적 API와 컨트롤 루프 패턴을 핵심으로 하는 분산 컨테이너 오케스트레이션 시스템이다. Control Plane(kube-apiserver, kube-controller-manager, kube-scheduler, etcd)과 Worker Node(kubelet, kube-proxy, container runtime)로 구성되며, 모든 컴포넌트는 kube-apiserver를 통해서만 통신하는 허브-스포크(hub-spoke) 아키텍처를 따른다.

본 개선 계획서는 Kubernetes 소스코드의 구조적 한계점과 성능 병목을 분석하고, 구체적인 개선 방안을 제시한다. 분석 대상 버전은 Kubernetes 1.32 기준이며, `pkg/scheduler/`, `pkg/kubelet/`, `staging/src/k8s.io/apiserver/pkg/storage/etcd3/`, `staging/src/k8s.io/client-go/tools/cache/` 등의 핵심 코드 경로를 기반으로 한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 etcd Cacher의 watchCache 슬라이딩 윈도우 메모리 비효율

**영향 컴포넌트**: `staging/src/k8s.io/apiserver/pkg/storage/cacher/`

etcd Cacher 레이어의 watchCache는 슬라이딩 윈도우 방식으로 최근 이벤트를 인메모리에 보관한다. 이 캐시는 Watch 클라이언트가 특정 ResourceVersion부터 이벤트를 재수신할 수 있도록 지원하는데, 대규모 클러스터(5,000+ 노드, 150,000+ Pod)에서 다음과 같은 문제가 발생한다:

- **watchCache의 고정 크기 한계**: 리소스 타입별로 단일 watchCache가 존재하며, 이벤트 윈도우 크기가 리소스 수에 비례하여 증가한다. Pod 리소스의 경우 watchCache가 수 GB의 메모리를 점유할 수 있다.
- **GetList 시 전체 캐시 순회**: `staging/src/k8s.io/apiserver/pkg/storage/etcd3/store.go`의 `GetList()` 메서드에서 Predicate 필터링(label/field selector)이 캐시 레이어가 아닌 etcd3 store 레이어에서 수행되어, 캐시 히트율이 낮은 경우 전체 아이템을 순회하며 `reflect.Append()`로 결과를 조립한다.
- **Encryption at Rest와 watchCache 이중 변환**: `transformer.TransformFromStorage()` 호출이 캐시 미스 시마다 발생하여, EncryptionConfig 사용 클러스터에서 LIST 응답 지연이 3~5배 증가한다.

### 2.2 스케줄러의 단일 스레드 Scheduling Cycle 직렬 병목

**영향 컴포넌트**: `pkg/scheduler/schedule_one.go`, `pkg/scheduler/scheduler.go`

스케줄러의 `ScheduleOne()` 메서드는 `wait.UntilWithContext(ctx, sched.ScheduleOne, 0)` 루프로 실행되며, Scheduling Cycle 전체가 **단일 goroutine에서 직렬**로 수행된다(`schedule_one.go:66-95`). 구체적 병목:

- **ScheduleOne의 직렬 실행**: Pod 하나의 Scheduling Cycle(PreFilter -> Filter -> PreScore -> Score -> Reserve -> Permit)이 완료되어야 다음 Pod의 스케줄링이 시작된다. Binding Cycle만 `go sched.runBindingCycle()`로 비동기 처리된다(`schedule_one.go:303`).
- **nodeInfoSnapshot 갱신 오버헤드**: 매 Scheduling Cycle마다 `sched.nodeInfoSnapshot`이 갱신되며, 노드 수가 많을수록 스냅샷 복사 비용이 증가한다.
- **percentageOfNodesToScore 한계**: 대규모 클러스터에서 Score 단계의 노드 수를 줄이기 위해 `percentageOfNodesToScore`(기본값: `schedulerapi.DefaultPercentageOfNodesToScore`)를 사용하지만, Filter 단계는 여전히 `parallelize.Until()`로 모든 feasible 노드를 순회해야 한다.
- **PriorityQueue의 3-큐 구조 Lock 경합**: `backend/queue/scheduling_queue.go`의 activeQ, backoffQ, unschedulablePods 간 이동 시 전역 Lock이 필요하며, 초당 수백 개의 Pod 스케줄링 시 Lock 경합이 발생한다.

### 2.3 Kubelet PLEG의 주기적 폴링 기반 상태 감지 지연

**영향 컴포넌트**: `pkg/kubelet/pleg/generic.go`, `pkg/kubelet/kubelet.go`

GenericPLEG(`pkg/kubelet/pleg/generic.go:53-85`)는 1초 주기로 `runtime.GetPods(ctx, true)` CRI 호출을 수행하여 컨테이너 상태 변화를 감지한다(`Relist()`, 라인 234-369). 이 설계의 한계:

- **CRI 호출 비용**: Relist 시 `GetPods(ctx, true)`로 전체 컨테이너 목록을 조회하고, `podRecords`와 비교하여 `computeEvents()`로 이벤트를 생성한다. 노드당 100+ 컨테이너 환경에서 Relist 1회에 50~200ms 소요되며, 이 시간 동안 PLEG 이벤트 채널(`plegChannelCapacity=1000`)이 포화될 수 있다.
- **PLEG 건강 체크 임계값**: `Healthy()` 메서드(`generic.go:180-192`)는 `relistDuration.RelistThreshold`(기본 3분) 기준으로 건강 상태를 판단한다. CRI 응답 지연이 누적되면 PLEG unhealthy -> 노드 NotReady -> Pod eviction의 연쇄 장애가 발생한다.
- **EventedPLEG 미완성**: CRI 이벤트 스트리밍 기반의 EventedPLEG가 feature gate로 존재하지만, 아직 GA(General Availability)에 도달하지 않아 대부분의 프로덕션 환경에서 GenericPLEG의 폴링 방식에 의존한다.
- **syncLoop의 5채널 select 경합**: `syncLoopIteration()`(`kubelet.go:2580-2655`)에서 configCh, plegCh, syncCh, probe updates, housekeepingCh의 5개 채널이 단일 select문에서 경합하며, PLEG 이벤트 폭주 시 다른 채널의 처리가 지연된다.

### 2.4 client-go DeltaFIFO의 단일 Lock 병목 및 Informer 메모리 중복

**영향 컴포넌트**: `staging/src/k8s.io/client-go/tools/cache/delta_fifo.go`, `staging/src/k8s.io/client-go/tools/cache/shared_informer.go`

client-go의 Informer 파이프라인(Reflector -> DeltaFIFO -> Indexer -> EventHandler -> WorkQueue)에서 다음 병목이 존재한다:

- **DeltaFIFO의 전역 Mutex**: `DeltaFIFO` 구조체(`delta_fifo.go:108-158`)는 `sync.RWMutex`로 `items map[string]Deltas`와 `queue []string`을 보호한다. `queueActionInternalLocked()`에서 모든 Add/Update/Delete 작업이 이 Lock을 획득해야 하며, `Pop()`도 같은 Lock을 사용한다. 대량 이벤트 발생 시(예: 노드 장애로 인한 수백 개 Pod 재스케줄링) producer(Reflector)와 consumer(processLoop) 간 Lock 경합이 심화된다.
- **Informer간 객체 메모리 중복**: SharedInformerFactory가 리소스 타입별로 단일 Informer를 공유하지만, controller-manager 내부의 DeploymentController, ReplicaSetController, GarbageCollector 등이 각각 별도의 EventHandler를 등록하고, WorkQueue에 동일 객체의 키를 중복 삽입한다. `CreateControllerContext()`(`controllermanager.go:475`)에서 `WithTransform(trim)`으로 ManagedFields를 제거하는 최적화가 있지만, 전체 객체 본문은 여전히 Indexer에 보관된다.
- **dedupDeltas의 제한적 중복 제거**: `dedupDeltas()`(`delta_fifo.go:443-478`)는 연속된 Deleted 이벤트만 중복 제거한다. 빈번한 Status 업데이트로 인해 같은 객체에 대한 Updated Delta가 누적되어 불필요한 reconcile 호출이 발생한다.

---

## 3. 개선 제안

### 3.1 etcd Cacher watchCache의 계층적 인덱싱 및 지연 직렬화 도입

#### 문제 상세

`GetList()` 호출 시 watchCache에서 Predicate 매칭이 아이템 단위로 수행되며, 각 아이템에 대해 `transformer.TransformFromStorage()` -> `decoder.Decode()` -> `opts.Predicate.Matches()` 파이프라인이 순차 실행된다. 150,000개 Pod 클러스터에서 `kubectl get pods -l app=nginx` 수행 시, 전체 Pod를 디코딩한 후 label 매칭하므로 응답 시간이 2~5초에 달할 수 있다.

#### 근본 원인

watchCache의 Indexer가 namespace 기반 인덱싱만 지원하고, label/field selector에 대한 보조 인덱스가 없다. 또한 Encryption at Rest 사용 시 캐시 계층에서 복호화된 객체를 보관하지 않아, 동일 객체가 반복적으로 복호화/디코딩된다.

#### 제안 솔루션

1. **Label 기반 보조 인덱스 추가**: watchCache에 자주 사용되는 label key(예: `app`, `release`, `tier`)에 대한 보조 인덱스를 B-tree 구조로 유지한다. `Predicate.Matches()` 전에 인덱스 lookup으로 후보 집합을 줄인다.

2. **복호화 결과 LRU 캐시**: `TransformFromStorage()` 결과를 etcd revision 기반의 LRU 캐시에 보관하여, 동일 revision의 객체에 대한 반복 복호화를 방지한다.

3. **지연 직렬화(Lazy Deserialization)**: LIST 응답 시 클라이언트가 요청한 필드만 디코딩하는 부분 디코딩 최적화를 도입한다. Protobuf 포맷의 경우 필드 번호 기반으로 label metadata만 먼저 파싱하여 Predicate 매칭 후, 매칭된 객체만 전체 디코딩한다.

#### 예상 효과

- Label selector 기반 LIST 응답 시간 70~80% 단축 (전체 순회 -> 인덱스 기반 조회)
- Encryption at Rest 환경에서 반복 LIST의 CPU 사용량 50% 감소
- API Server의 메모리 GC 압력 30% 감소 (불필요한 객체 생성 감소)

#### 구현 난이도

**상 (High)** - watchCache의 인덱스 구조 변경은 `staging/src/k8s.io/apiserver/pkg/storage/cacher/` 전반에 영향을 미치며, 인덱스 일관성 유지와 메모리 오버헤드 간의 균형 설계가 필요하다. 기존 `storage.Interface` 계약을 변경하지 않으면서 내부 최적화로 구현해야 한다.

---

### 3.2 스케줄러 Scheduling Cycle의 파이프라인 병렬화

#### 문제 상세

현재 스케줄러는 `ScheduleOne()` 루프에서 한 번에 하나의 Pod만 스케줄링한다(`schedule_one.go:66-95`). 1,000 노드 클러스터에서 Pod 하나의 Scheduling Cycle이 평균 5~20ms 소요되므로, 초당 최대 50~200개 Pod만 스케줄링 가능하다. 대규모 배포(예: 1,000개 Pod 동시 생성) 시 스케줄링 큐에 Pod가 적체되어 배포 완료까지 5~20초가 소요된다.

#### 근본 원인

Scheduling Cycle이 직렬인 이유는 `Assume()` 단계에서 스케줄러 캐시에 Pod를 "가정(assume)"하여 후속 스케줄링의 리소스 계산에 반영하기 때문이다. 두 Pod가 동시에 같은 노드에 Assume되면 리소스 초과 배치가 발생할 수 있다. 그러나 이 제약은 노드 단위로 격리 가능하다.

#### 제안 솔루션

1. **노드 단위 Lock 분할**: 현재 스케줄러 캐시(`backend/cache/`)의 전역 Lock을 노드별 fine-grained Lock으로 분할한다. 서로 다른 노드에 배치되는 Pod는 동시에 Scheduling Cycle을 수행할 수 있다.

2. **다중 Scheduling Worker**: `ScheduleOne()` 루프를 N개(기본 `parallelism` 값인 16)의 Worker goroutine으로 확장한다. 각 Worker는 독립적으로 Pod를 Pop하여 Filter/Score를 수행하고, Assume 단계에서만 노드별 Lock을 획득한다.

3. **배치 스케줄링 최적화**: 동일 Deployment에서 생성된 Pod 그룹을 감지하여, nodeInfoSnapshot 갱신을 배치 시작 시 1회만 수행하고, 동일 Filter/Score 결과를 재사용한다. 이미 `scheduleOnePodGroup()` 코드 경로가 존재하므로(`schedule_one.go:243-251`), 이를 확장한다.

4. **PriorityQueue Lock 최적화**: activeQ를 lock-free concurrent priority queue로 교체하거나, shard 방식으로 분할하여 Lock 경합을 줄인다.

#### 예상 효과

- 스케줄링 처리량(throughput) 3~5배 향상 (50 Pod/s -> 150~250 Pod/s)
- 대규모 배포 시 스케줄링 대기 시간 60~80% 단축
- Burst 워크로드 시나리오에서의 P99 스케줄링 레이턴시 개선

#### 구현 난이도

**상 (High)** - 동시성 제어의 정확성이 핵심이다. 노드별 Lock 분할 시 교착(deadlock) 방지, Assume/Forget 일관성 유지, 선점(Preemption) 로직과의 상호작용 검증이 필요하다. 기존 스케줄러 프레임워크 플러그인(`framework/plugins/`)의 thread-safety도 검증해야 한다.

---

### 3.3 Kubelet PLEG의 이벤트 기반 전환 가속화 및 syncLoop 채널 우선순위 도입

#### 문제 상세

GenericPLEG의 1초 주기 `Relist()`는 노드당 컨테이너 수에 비례하는 CRI 호출 비용을 발생시킨다. 100개 컨테이너 노드에서 Relist 1회에 100~200ms, 300개 컨테이너 노드에서 300~500ms가 소요된다. Relist 시간이 1초를 초과하면 다음 Relist가 지연되어 컨테이너 상태 감지가 2초 이상 지연되고, 3분 이상 누적 지연 시 PLEG unhealthy가 발생한다.

또한 `syncLoopIteration()`의 5채널 select문은 Go의 select 공정성(fairness) 특성에 의해 모든 채널이 동등한 우선순위를 가진다. PLEG 이벤트 폭주 시 configCh의 새 Pod 생성 이벤트가 지연될 수 있다.

#### 근본 원인

- GenericPLEG가 "pull" 모델(주기적 폴링)에 의존하며, CRI의 `GetContainerEvents()` 스트리밍 API(EventedPLEG)가 아직 프로덕션 준비 수준에 도달하지 않았다.
- syncLoopIteration의 단일 select문이 채널 간 우선순위를 구분하지 않아, 중요한 설정 변경(Pod 삭제 등)이 덜 중요한 이벤트(housekeeping)에 밀릴 수 있다.

#### 제안 솔루션

1. **EventedPLEG GA 기여 가속화**: CRI의 `GetContainerEvents()` 스트리밍 API를 containerd/CRI-O에서 안정화하고, EventedPLEG의 fallback 메커니즘(이벤트 유실 감지 시 GenericPLEG으로 자동 전환)을 강화한다. `pkg/kubelet/pleg/generic.go`의 `Relist()` 주기를 EventedPLEG 활성 시 10초로 완화하여 backup 전용으로 전환한다.

2. **Relist 증분 조회**: 전체 `GetPods(ctx, true)` 대신, 마지막 Relist 이후 변경된 컨테이너만 조회하는 증분 API를 CRI에 추가한다. containerd의 `ListContainers`에 `since` 타임스탬프 필터를 활용한다.

3. **syncLoop 우선순위 기반 채널 처리**: 단일 select문을 우선순위 기반 다중 select로 변환한다:
   - 최우선: configCh (Pod 생성/삭제), liveness probe failure
   - 중간: plegCh, syncCh
   - 최하위: housekeepingCh, readiness/startup probes

   구현 방식: 내부 루프에서 고우선순위 채널을 먼저 비블로킹(non-blocking) select로 소비한 후, 남은 채널을 블로킹 select로 처리한다.

4. **PLEG 이벤트 채널 동적 버퍼링**: 현재 `plegChannelCapacity=1000` 고정값을 노드의 컨테이너 수 기반으로 동적 조정한다.

#### 예상 효과

- EventedPLEG 전환 시 컨테이너 상태 감지 지연 1초 -> 100ms 이하로 단축
- Relist 증분 조회로 CRI 호출 비용 80~90% 감소
- syncLoop 우선순위 도입으로 고부하 상황에서의 Pod 생성/삭제 응답성 40% 개선
- PLEG unhealthy 발생률 95% 감소

#### 구현 난이도

**중 (Medium)** - EventedPLEG 안정화는 Kubernetes 외부 프로젝트(containerd, CRI-O)와의 협업이 필요하다. syncLoop 우선순위 변경은 Kubelet 내부 코드 변경으로 범위가 제한적이며, 기존 테스트 프레임워크로 검증 가능하다.

---

### 3.4 client-go DeltaFIFO의 Lock-Free 큐 전환 및 Informer 메모리 최적화

#### 문제 상세

DeltaFIFO의 `sync.RWMutex`가 producer(Reflector의 `watchHandler()`)와 consumer(`controller.processLoop()`)의 동시 접근을 보호한다. 노드 장애나 대규모 롤아웃 시 초당 수천 개의 이벤트가 DeltaFIFO에 유입되면, `queueActionInternalLocked()`의 Lock 획득 대기 시간이 이벤트 처리 전체 지연의 30~50%를 차지한다.

또한 controller-manager의 SharedInformer가 보관하는 전체 객체 본문이 GarbageCollector(`pkg/controller/garbagecollector/`)의 `GraphBuilder`가 필요로 하는 메타데이터(UID, ownerReferences)보다 수배 크며, 이로 인해 controller-manager의 메모리 사용량이 클러스터 규모에 비례하여 급증한다.

#### 근본 원인

- DeltaFIFO가 `items map[string]Deltas`과 `queue []string`을 하나의 Lock으로 보호하여, read-heavy(Pop)와 write-heavy(Add/Update) 작업이 상호 배제된다.
- SharedInformer의 Indexer(`cache.ThreadSafeStore`)가 전체 객체를 저장하며, 컨트롤러별로 필요한 필드만 추출하는 메커니즘이 없다.

#### 제안 솔루션

1. **DeltaFIFO Lock 분할**: `items` map의 키를 해시 기반으로 N개 shard로 분할하고, 각 shard에 독립 Mutex를 부여한다. `queue`는 lock-free MPSC(Multi-Producer Single-Consumer) 큐로 교체한다. Pop은 단일 consumer이므로 MPSC 큐의 성능 특성에 정확히 부합한다.

2. **Partial Object Informer 확장**: 현재 `ObjectOrMetadataInformerFactory`(`controllermanager.go:408-443`)를 확장하여, GarbageCollector 등 메타데이터만 필요한 컨트롤러에 `metav1.PartialObjectMetadata` 기반 Informer를 기본 제공한다. 이미 `controllerContext.ObjectOrMetadataInformerFactory` 필드가 존재하므로, 이를 더 많은 컨트롤러에 적용한다.

3. **DeltaFIFO의 Updated 이벤트 병합**: `dedupDeltas()`를 확장하여, 동일 객체에 대한 연속 Updated 이벤트를 최신 상태로 병합한다. reconcile 로직이 level-triggered이므로 중간 상태 유실은 의미론적으로 안전하다. 단, Deleted 이벤트는 절대 건너뛰지 않는다.

4. **Informer Transform 함수 표준화**: `CreateControllerContext()`의 `WithTransform(trim)` 패턴을 확장하여, 각 컨트롤러가 필요한 필드만 유지하는 transform 함수를 등록할 수 있도록 한다. 예: ReplicaSetController는 `spec.replicas`, `status.replicas`, `metadata` 필드만 필요하므로 나머지 필드를 nil로 설정하여 GC가 회수하도록 한다.

#### 예상 효과

- DeltaFIFO Lock 경합 80% 감소 (shard + lock-free 큐)
- controller-manager 메모리 사용량 40~60% 감소 (Partial Object + Transform)
- 이벤트 폭주 시 reconcile 처리량 2~3배 향상 (Updated 병합)
- Watch reconnection 후 초기 동기화 시간 30% 단축

#### 구현 난이도

**중 (Medium)** - DeltaFIFO의 Lock 분할은 기존 인터페이스를 유지하면서 내부 구현만 변경하므로 하위 호환성 위험이 낮다. Updated 이벤트 병합은 level-triggered 의미론에 의존하므로 edge-triggered 동작을 기대하는 비표준 컨트롤러와의 호환성 테스트가 필요하다.

---

## 4. 우선순위 및 로드맵

### Phase 1: 단기 (0~3개월) - 저위험 고효과 최적화

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 1 | syncLoop 우선순위 기반 채널 처리 (3.3-3) | Kubelet 내부 변경, 외부 의존성 없음, 즉시 효과 |
| 2 | DeltaFIFO Updated 이벤트 병합 (3.4-3) | `dedupDeltas()` 확장, 하위 호환성 유지 가능 |
| 3 | Informer Transform 함수 표준화 (3.4-4) | 기존 `WithTransform` 패턴 확장, 메모리 즉시 절감 |
| 4 | PLEG 이벤트 채널 동적 버퍼링 (3.3-4) | 상수값 변경 수준, 위험도 최소 |

### Phase 2: 중기 (3~6개월) - 구조적 개선

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 5 | DeltaFIFO Lock 분할 (3.4-1) | client-go 내부 변경, API 호환성 유지 |
| 6 | Partial Object Informer 확장 (3.4-2) | 기존 인프라 활용, 점진적 마이그레이션 가능 |
| 7 | watchCache Label 보조 인덱스 (3.1-1) | API Server 내부 최적화, 스토리지 인터페이스 불변 |
| 8 | 복호화 결과 LRU 캐시 (3.1-2) | Encryption 사용 클러스터 한정, 범위 제한적 |

### Phase 3: 장기 (6~12개월) - 아키텍처 레벨 변경

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 9 | EventedPLEG GA 기여 (3.3-1) | 외부 프로젝트 협업 필요, 장기 릴리스 사이클 |
| 10 | 스케줄러 다중 Scheduling Worker (3.2-2) | 동시성 설계 복잡도 높음, 광범위 테스트 필요 |
| 11 | 스케줄러 노드 단위 Lock 분할 (3.2-1) | 스케줄러 캐시 근본 구조 변경 |
| 12 | Relist 증분 조회 CRI API (3.3-2) | CRI spec 변경 필요, SIG-Node 합의 필수 |
| 13 | 지연 직렬화 (3.1-3) | Protobuf 파서 수준 변경, 복잡도 최상 |

---

## 5. 기대 성과

### 정량적 성과 목표

| 지표 | 현재 수준 (5,000 노드 기준) | 목표 수준 | 개선율 |
|------|----------------------------|----------|--------|
| LIST pods -l app=X 응답 시간 | 2~5초 | 0.5~1초 | 70~80% 단축 |
| 스케줄링 처리량 | 50~200 Pod/s | 150~500 Pod/s | 3~5배 향상 |
| 컨테이너 상태 감지 지연 | 1~2초 (PLEG relist) | 100ms 이하 (EventedPLEG) | 90% 단축 |
| controller-manager 메모리 | 8~16 GB | 4~8 GB | 50% 감소 |
| DeltaFIFO 이벤트 처리량 | 5,000 events/s | 15,000 events/s | 3배 향상 |
| PLEG unhealthy 발생률 | 월 2~5회 (대규모 클러스터) | 월 0~1회 | 80~95% 감소 |

### 정성적 성과

- **대규모 클러스터 안정성 향상**: etcd 부하 감소와 스케줄러 처리량 향상으로 5,000+ 노드 클러스터의 안정적 운영이 가능해진다.
- **CNCF 커뮤니티 기여 확대**: 핵심 컴포넌트(scheduler, kubelet, apiserver, client-go)에 대한 구조적 개선 기여로 현대자동차의 오픈소스 영향력을 강화한다.
- **엔터프라이즈 환경 최적화**: Encryption at Rest, 대규모 CRD, 멀티테넌시 환경에서의 성능 병목 해소로 엔터프라이즈 Kubernetes 배포의 운영 품질을 높인다.
- **기술 역량 축적**: Kubernetes 코어 코드베이스에 대한 심층 이해를 기반으로, 향후 자체 Kubernetes 확장(Operator, Scheduler Plugin 등) 개발 역량을 확보한다.
