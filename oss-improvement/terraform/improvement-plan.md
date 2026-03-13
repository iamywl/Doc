# Terraform 코어 엔진 개선 계획서

> 작성일: 2026-03-13
> 대상: HashiCorp Terraform (OpenTofu 포함) 코어 엔진
> 분석 범위: DAG 그래프 엔진, State Management, Plan/Apply 엔진, Provider Plugin System, HCL Config Loading, Module System

---

## 1. 프로젝트 개요

Terraform은 Infrastructure as Code(IaC) 도구로, HCL 선언적 설정 파일을 통해 클라우드 인프라를 정의하고 관리한다. 핵심 아키텍처는 4개 레이어(CLI Layer, Core Engine, Plugin Layer, Storage Layer)로 구성되며, `terraform.Context`가 모든 실행을 조율한다.

본 개선 계획서는 Terraform 코어 엔진의 내부 아키텍처를 심층 분석하여 발견한 구조적 한계점과 성능 병목을 식별하고, 각각에 대한 구체적인 개선 방안을 제시한다. 분석은 `internal/dag/`, `internal/states/`, `internal/terraform/`, `internal/plugin/`, `internal/configs/`, `internal/backend/` 등 핵심 패키지를 대상으로 수행되었다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 DAG 그래프 엔진의 이행적 축소(Transitive Reduction) 시간 복잡도 문제

**위치**: `internal/dag/dag.go` - `TransitiveReduction()` 메서드

현재 이행적 축소 알고리즘은 각 정점 `u`에 대해 `u`의 직접 자식(`downEdgesNoCopy(u)`)으로부터 DFS를 수행하고, 탐색 중 방문하는 모든 정점 `v`에 대해 `u`의 자식 집합과 `v`의 자식 집합의 교집합(Intersection)을 계산하여 불필요한 간선을 제거한다.

```
전체 시간 복잡도: O(V * (V + E)) = O(V^2 + VE)
```

대규모 인프라(리소스 1,000개 이상)에서 그래프의 정점과 간선 수가 급증하면, 이 단계가 전체 Plan/Apply 그래프 빌드 시간의 상당 부분을 차지한다. 특히 모듈 중첩이 깊고 `for_each`/`count`로 확장된 리소스가 많은 환경에서 병목이 심화된다.

### 2.2 Set 자료구조의 `interface{}` 기반 설계로 인한 타입 안전성 및 성능 저하

**위치**: `internal/dag/set.go` - `Set` 타입 정의

```go
type Set map[interface{}]interface{}
```

현재 `Set`은 Go 제네릭 도입 이전에 설계되어 `map[interface{}]interface{}`로 구현되어 있다. 이로 인해:

- **런타임 타입 단언(type assertion)** 비용이 모든 Set 연산(Add, Delete, Include, Intersection, Difference)에서 발생
- `interface{}` 값의 **boxing/unboxing** 오버헤드로 GC 압력 증가
- `Hashable` 인터페이스를 통한 `hashcode()` 호출이 매 연산마다 필요하며, 이는 `downEdgesNoCopy()`, `upEdgesNoCopy()` 등 그래프 탐색의 핵심 경로에서 빈번하게 실행됨
- Intersection 연산 시 두 Set의 크기를 비교하여 작은 쪽을 순회하는 최적화가 있지만, `interface{}` 기반이므로 컴파일러 최적화(인라인, 이스케이프 분석)가 제한됨

Walker가 `V*2`개의 고루틴을 생성하고 각 고루틴이 Set 연산을 수행하므로, 이 오버헤드는 병렬 실행 환경에서 더욱 증폭된다.

### 2.3 SyncState의 DeepCopy 기반 읽기 패턴으로 인한 메모리 오버헤드

**위치**: `internal/states/sync.go` - `SyncState` 구조체의 읽기 메서드들

```go
func (s *SyncState) Module(addr addrs.ModuleInstance) *Module {
    s.lock.RLock()
    ret := s.state.Module(addr).DeepCopy()  // 매 읽기마다 깊은 복사
    s.lock.RUnlock()
    return ret
}
```

Graph Walk 중 병렬로 실행되는 각 노드가 State를 조회할 때마다 `DeepCopy()`를 수행한다. 이는 동시성 안전을 보장하기 위한 설계이지만:

- 대규모 State(수천 개 리소스)에서 `ResourceInstanceObjectSrc`의 `AttrsJSON []byte` 필드가 반복 복사됨
- `RWMutex`의 읽기 잠금 보유 시간 중 DeepCopy가 포함되어 쓰기 대기 시간이 증가
- Graph Walk의 parallelism(기본 10)에 비례하여 동시 복사 발생, GC 부하 급증
- 실제로 대부분의 읽기는 조회 후 수정하지 않음에도 불구하고 전체 복사가 이루어짐

State 계층 구조(State -> Module -> Resource -> ResourceInstance -> ResourceInstanceObjectSrc)의 4단계 깊이를 모두 복사하므로, 모듈이 많은 프로젝트에서 메모리 사용량이 급격히 증가한다.

### 2.4 Provider Plugin gRPC 통신의 직렬화/역직렬화 반복 오버헤드

**위치**: `internal/plugin/grpc_provider.go` - `GRPCProvider` 메서드들

모든 Provider RPC 호출이 6단계 번역 패턴을 따른다:
1. `GetProviderSchema()`로 스키마 확보
2. 타입별 스키마에서 `ImpliedType()` 추출
3. `cty.Value` -> `msgpack` 직렬화
4. Proto Request 구조체 구성
5. gRPC 호출
6. Proto Response -> `providers.XXXResponse` 변환

`PlanResourceChange`와 `ApplyResourceChange` 호출 시 `PriorState`, `ProposedNewState`, `Config` 등 여러 `cty.Value`를 각각 msgpack으로 직렬화해야 한다. 리소스 수가 많을 때 이 직렬화/역직렬화 비용이 누적된다. 특히 AWS Provider처럼 스키마가 큰(수 MB) Provider에서는 `GetProviderSchema` 호출 자체가 64MB 수신 크기 제한을 활용할 정도로 무겁다.

2단계 스키마 캐시(글로벌 `SchemaCache` + 로컬 `GRPCProvider.schema`)가 존재하지만, `GetProviderSchemaOptional`을 지원하지 않는 레거시 Provider에서는 인스턴스마다 gRPC 호출이 필요하며, 로컬 캐시도 `sync.Mutex`로 보호되어 동시 접근 시 직렬화가 발생한다.

### 2.5 GraphTransformer 체인의 선형 적용 구조

**위치**: `internal/terraform/graph_builder_plan.go` - `Steps()` 메서드

PlanGraphBuilder는 25개 이상의 GraphTransformer를 순차적으로 적용하여 DAG를 구축한다:

```go
func (b *PlanGraphBuilder) Steps() []GraphTransformer {
    return []GraphTransformer{
        &ConfigTransformer{...},           // 1
        &RootVariableTransformer{...},     // 3
        ...
        &ReferenceTransformer{},           // 15
        &TransitiveReductionTransformer{}, // 25
    }
}
```

각 Transformer가 그래프 전체를 순회하므로, 총 순회 횟수가 `25 * (V + E)`에 달한다. 일부 Transformer는 독립적이어서 병렬 적용이 가능함에도 불구하고(예: `RootVariableTransformer`와 `LocalTransformer`는 서로 다른 노드 타입을 생성), 현재는 모두 순차 적용된다.

또한 Transformer 간 암묵적 순서 의존성(예: `AttachSchemaTransformer`가 `ReferenceTransformer`보다 먼저 실행되어야 함)이 코드 주석으로만 문서화되어 있어, 새로운 Transformer 추가 시 순서 오류가 발생할 위험이 있다.

### 2.6 상태 파일의 단일 JSON 직렬화 병목

**위치**: `internal/states/statefile/version4.go` - `readStateV4()`, `writeStateV4()`

State 파일(v4 형식)은 모든 리소스를 단일 JSON 문서로 직렬화한다. `readStateV4()`는 `json.Unmarshal(src, sV4)`로 전체 파일을 한 번에 파싱하고, 이후 `prepareStateV4()`에서 모든 리소스를 순회하며 State 트리를 재구성한다.

대규모 인프라(리소스 10,000개 이상)에서:
- 전체 State JSON의 크기가 수십~수백 MB에 달할 수 있음
- `json.Unmarshal`은 전체 문서를 메모리에 로드하고 파싱해야 함
- `prepareStateV4`의 리소스별 순회에서 `addrs.ParseModuleInstanceStr()`, `addrs.ParseAbsProviderConfigStr()` 등 문자열 파싱이 매번 발생
- Plan 시 State를 읽고, Apply 후 State를 쓰는 양쪽 모두에서 전체 직렬화/역직렬화가 필요
- 원격 백엔드(S3, GCS 등)에서는 네트워크 전송 크기도 문제

---

## 3. 개선 제안

### 3.1 DAG 이행적 축소 알고리즘 최적화

**문제 상세**

`TransitiveReduction()`의 O(V^2 + VE) 복잡도는 리소스 1,000개 기준으로 수십만 회의 Set Intersection 연산을 유발한다. 현재 알고리즘은 각 정점에서 DFS를 수행하면서 발견되는 모든 노드에 대해 원본 정점의 자식 집합과 교집합을 계산하는 "brute-force" 방식이다.

**근본 원인**

위상 정렬된 순서에서의 도달 가능성(reachability) 정보를 사전 계산하지 않고, 매 정점마다 독립적으로 DFS를 수행하기 때문이다.

**제안 솔루션**

1. **위상 정렬 기반 도달 가능성 매트릭스 사전 계산**: 위상 정렬 순서로 정점을 순회하면서 각 정점의 도달 가능 집합(reachability set)을 비트셋(bitset)으로 관리한다. 자식의 도달 가능 집합을 OR 연산으로 병합하면 O(V * E / w) (w = 워드 크기, 보통 64)로 줄일 수 있다.

2. **증분적 이행적 축소**: 그래프 변경 시 전체 재계산 대신, 변경된 노드 주변만 국소적으로 이행적 축소를 수행하는 방식 도입.

3. **지연 실행(Lazy Evaluation)**: `terraform graph` 명령처럼 시각화 목적이 아닌 실행 경로에서는 이행적 축소를 생략하는 옵션 제공. Walker의 의존성 대기 메커니즘이 이미 정확한 실행 순서를 보장하므로, 축소 없이도 정확성에는 영향이 없다.

**예상 효과**
- 리소스 1,000개 기준 그래프 빌드 시간 40-60% 단축
- 리소스 5,000개 이상의 대규모 프로젝트에서 효과 극대화

**구현 난이도**: 중 (기존 알고리즘 교체, 비트셋 자료구조 도입 필요)

---

### 3.2 제네릭 기반 Set 자료구조 현대화

**문제 상세**

`Set`은 `map[interface{}]interface{}` 타입으로, 모든 값이 `interface{}` 박싱을 거치며, `hashcode()` 함수가 매번 타입 단언(`Hashable` 인터페이스 확인)을 수행한다. 이는 Go 컴파일러의 인라인 최적화와 이스케이프 분석을 방해하여 힙 할당이 불필요하게 증가한다.

**근본 원인**

Go 1.18 이전에 설계되어 제네릭이 불가능했던 시기의 코드가 그대로 유지되고 있다.

**제안 솔루션**

Go 제네릭을 활용한 타입 안전 Set으로 점진적 마이그레이션:

```go
// 제안 구조
type Set[T comparable] map[T]struct{}

// Vertex 전용 특화 Set
type VertexSet = Set[Vertex]

// Edge 전용 특화 Set (Hashcode 기반)
type EdgeSet struct {
    m map[[2]interface{}]Edge
}
```

마이그레이션 전략:
1. `Set[T]` 제네릭 타입을 `internal/dag/` 패키지에 추가
2. 내부 사용처(`downEdges`, `upEdges`, `vertices`)를 타입 특화 Set으로 교체
3. 외부 API(`DownEdges()`, `UpEdges()` 등)는 호환성 어댑터를 통해 점진적 전환
4. 기존 `Set` 타입은 deprecated 표시 후 1-2 릴리스 후 제거

**예상 효과**
- Set 연산 20-30% 성능 향상 (boxing/unboxing 제거)
- GC 압력 감소 (힙 할당 감소)
- 컴파일 타임 타입 검사로 런타임 오류 방지
- 코드 가독성 향상

**구현 난이도**: 중-고 (광범위한 리팩토링, 하위 호환성 관리 필요)

---

### 3.3 SyncState의 Copy-on-Write(CoW) 또는 불변 스냅샷 방식 도입

**문제 상세**

현재 `SyncState`의 모든 읽기 메서드(`Module()`, `Resource()`, `ResourceInstance()`)가 `DeepCopy()`를 수행한다. Graph Walk의 parallelism이 10이고 리소스가 1,000개인 경우, Plan 과정에서 수천 회의 DeepCopy가 발생할 수 있다.

**근본 원인**

`RWMutex` + `DeepCopy` 패턴은 동시성 안전을 보장하지만, 읽기 빈도가 쓰기 빈도보다 압도적으로 높은 Graph Walk 패턴에서 과도한 복사 비용을 유발한다.

**제안 솔루션**

**방안 A: Copy-on-Write 스냅샷**

```go
type SyncState struct {
    snapshot atomic.Pointer[State]  // 불변 스냅샷
    writeMu sync.Mutex             // 쓰기 전용 잠금
}

func (s *SyncState) ReadSnapshot() *State {
    return s.snapshot.Load()  // 잠금 없이 원자적 읽기
}

func (s *SyncState) ApplyWrite(fn func(*State)) {
    s.writeMu.Lock()
    defer s.writeMu.Unlock()
    newState := s.snapshot.Load().DeepCopy()
    fn(newState)
    s.snapshot.Store(newState)  // 원자적 교체
}
```

- 읽기: 잠금 없이 `atomic.Pointer`로 현재 스냅샷 참조 획득 (O(1), 복사 없음)
- 쓰기: 새 스냅샷을 생성하여 원자적으로 교체

**방안 B: ResourceInstance 레벨 세분화 잠금**

Module/Resource 단위가 아닌 ResourceInstance 단위의 세분화된 잠금 도입으로 DeepCopy 범위를 최소화.

**예상 효과**
- 읽기 작업 메모리 할당 90% 이상 감소
- Graph Walk 중 State 접근 지연시간 50-70% 단축
- GC 일시 정지(pause) 시간 감소

**구현 난이도**: 고 (State의 불변성 보장 메커니즘 설계, 기존 코드의 State 수정 패턴 전수 조사 필요)

---

### 3.4 Provider gRPC 직렬화 파이프라인 최적화

**문제 상세**

`GRPCProvider`의 모든 RPC 메서드가 `cty.Value` -> `msgpack` -> `DynamicValue` -> gRPC 전송 -> `DynamicValue` -> `msgpack` -> `cty.Value` 경로를 거친다. `PlanResourceChange`는 3개의 `cty.Value`(`PriorState`, `ProposedNewState`, `Config`)를 각각 직렬화하고, 응답의 `PlannedState`를 역직렬화한다.

**근본 원인**

1. gRPC 경계에서의 `cty.Value` <-> `msgpack` 변환이 스키마의 `ImpliedType()`을 매번 계산
2. 동일 리소스 타입의 여러 인스턴스가 같은 스키마에 대해 반복적으로 `ImpliedType()` 호출
3. `ResourceInstanceObjectSrc.Decode()`의 `decodeValueCache`가 인스턴스별로만 캐시되어 동일 JSON 패턴의 재활용 불가

**제안 솔루션**

1. **ImpliedType 캐시**: 리소스 타입별 `ImpliedType()` 결과를 캐시하여 반복 계산 방지

```go
type schemaTypeCache struct {
    mu    sync.RWMutex
    types map[string]cty.Type  // key: resource type name
}
```

2. **배치 직렬화**: 동일 타입의 여러 리소스 인스턴스를 한 번의 gRPC 스트림으로 처리하는 Batch API 도입 (Provider 프로토콜 v7 제안)

3. **Zero-copy DynamicValue 전달**: 이미 msgpack으로 인코딩된 State(`AttrsJSON`이 아닌 `AttrsMsgpack` 필드 추가)를 재직렬화 없이 gRPC에 직접 전달

4. **스키마 캐시 RWMutex 전환**: 현재 글로벌 `SchemaCache`의 `sync.Mutex`를 `sync.RWMutex`로 변경하여 읽기 동시성 향상

**예상 효과**
- Provider RPC 호출당 직렬화 시간 30-50% 단축
- 대규모 Plan(1,000+ 리소스) 시 전체 Plan 시간 15-25% 개선

**구현 난이도**: 중 (ImpliedType 캐시, RWMutex 전환은 낮음 / Batch API는 프로토콜 변경 필요로 높음)

---

### 3.5 GraphTransformer 체인의 의존성 기반 병렬화 및 타입 안전 순서 관리

**문제 상세**

PlanGraphBuilder의 25개 Transformer가 순차 적용되며, 각각이 그래프 전체를 순회한다. 일부 Transformer는 독립적이지만 순차 실행된다. 또한 Transformer 간 순서 의존성이 암묵적이다.

**근본 원인**

`Steps()` 메서드가 단순 배열을 반환하고, `BasicGraphBuilder.Build()`가 이를 순차 반복하는 구조이기 때문이다.

**제안 솔루션**

1. **Transformer 의존성 그래프 도입**: 각 Transformer가 자신의 전제 조건(Prerequisites)과 제공 사항(Provides)을 선언하는 인터페이스 추가

```go
type DependentTransformer interface {
    GraphTransformer
    Requires() []TransformerCapability  // 필요한 선행 변환
    Provides() []TransformerCapability  // 완료 후 제공하는 기능
}

type TransformerCapability string
const (
    CapSchemaAttached   TransformerCapability = "schema_attached"
    CapReferencesLinked TransformerCapability = "references_linked"
    CapNodesCreated     TransformerCapability = "nodes_created"
)
```

2. **의존성 기반 병렬 빌드**: Transformer 의존성 그래프를 구축하고, 독립적인 Transformer를 고루틴으로 병렬 실행. 단, 그래프 자체에 대한 동시 수정을 위해 세분화된 잠금 또는 단계별 병합 전략이 필요.

3. **최소한의 개선으로 단일 패스 병합**: 현재 별도의 Transformer인 `RootVariableTransformer`, `ModuleVariableTransformer`, `LocalTransformer`, `OutputTransformer`를 단일 "DynamicValueTransformer"로 병합하여 순회 횟수를 줄임.

**예상 효과**
- 그래프 빌드 시간 20-30% 단축 (독립 Transformer 병렬화)
- Transformer 순서 오류 발생 가능성 제거 (컴파일 타임 검증)
- 새로운 Transformer 추가 시 안전성 향상

**구현 난이도**: 중-고 (의존성 그래프 설계, 기존 Transformer 어노테이션 추가)

---

### 3.6 State 직렬화의 증분적 읽기/쓰기 및 압축 도입

**문제 상세**

v4 State 파일의 단일 JSON 직렬화는 대규모 인프라에서 읽기/쓰기 모두에서 병목이 된다. `readStateV4()`의 `json.Unmarshal`은 전체 문서를 메모리에 올려야 하며, `prepareStateV4()`의 모듈/리소스 주소 문자열 파싱이 리소스 수에 비례하여 증가한다.

**근본 원인**

State 형식이 단일 JSON 문서로 설계되어 부분 읽기/쓰기가 불가능하다.

**제안 솔루션**

1. **스트리밍 JSON 파서 도입**: `encoding/json.Decoder`의 `Token()` 기반 스트리밍 파싱으로 전체 문서를 메모리에 올리지 않고 리소스별로 처리

2. **State v5 형식 제안: 모듈별 분할 저장**

```
.terraform/state/
├── metadata.json          # lineage, serial, terraform_version
├── modules/
│   ├── root.json          # 루트 모듈 리소스
│   ├── module.vpc.json    # vpc 모듈 리소스
│   └── module.vpc.module.subnet.json  # 중첩 모듈
└── outputs.json           # 루트 출력 값
```

- 모듈별 독립 파일로 분할하여 변경된 모듈만 읽기/쓰기
- 각 파일의 크기를 관리 가능한 수준으로 유지
- 원격 백엔드에서는 모듈별 독립 키로 저장

3. **gzip 압축**: State JSON에 gzip 압축을 적용하여 원격 백엔드 전송 크기 60-80% 감소

4. **주소 파싱 캐시**: `addrs.ParseModuleInstanceStr()`, `addrs.ParseAbsProviderConfigStr()` 등 반복 파싱되는 문자열에 대한 인턴(interning) 적용

**예상 효과**
- State 로드 시간 50-70% 단축 (대규모 프로젝트)
- 원격 백엔드 네트워크 전송 크기 60-80% 감소 (압축)
- 메모리 사용량 30-40% 감소 (스트리밍 파싱)

**구현 난이도**: 고 (State 형식 변경은 하위 호환성 관리가 핵심, v4->v5 마이그레이션 필요)

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 영향도 | 구현 난이도 | 예상 기간 | 단계 |
|---------|----------|--------|-----------|----------|------|
| **P0** | 3.4 Provider gRPC ImpliedType 캐시 + SchemaCache RWMutex | 높음 | 낮음 | 2-3주 | Phase 1 |
| **P0** | 3.1 이행적 축소 알고리즘 최적화 (비트셋 도달 가능성) | 높음 | 중 | 3-4주 | Phase 1 |
| **P1** | 3.2 제네릭 기반 Set 현대화 | 중-높음 | 중-고 | 6-8주 | Phase 2 |
| **P1** | 3.5 GraphTransformer 단일 패스 병합 (DynamicValueTransformer) | 중 | 중 | 3-4주 | Phase 2 |
| **P2** | 3.3 SyncState CoW 스냅샷 도입 | 높음 | 고 | 8-10주 | Phase 3 |
| **P2** | 3.6 State 직렬화 증분 읽기 + 압축 | 높음 | 고 | 10-12주 | Phase 3 |
| **P3** | 3.4 Provider Batch API (프로토콜 v7) | 중-높음 | 고 | 12-16주 | Phase 4 |
| **P3** | 3.6 State v5 형식 (모듈별 분할) | 높음 | 매우 높음 | 16-20주 | Phase 4 |

### Phase 1 (Quick Wins, 0-2개월)
- Provider 스키마 캐시 최적화와 DAG 이행적 축소 알고리즘 개선
- 기존 API 변경 없이 내부 구현만 교체
- 벤치마크 테스트 구축 및 성능 기준선 수립

### Phase 2 (기반 현대화, 2-4개월)
- Set 제네릭화와 Transformer 병합으로 코드 품질 및 성능 동시 개선
- 광범위한 리팩토링이므로 기능 브랜치에서 진행
- 기존 테스트 스위트 전체 통과 검증

### Phase 3 (핵심 아키텍처 개선, 4-7개월)
- SyncState CoW와 State 직렬화 개선은 핵심 데이터 경로 변경
- 대규모 실제 프로젝트(1,000+ 리소스)에서의 성능 검증 필수
- 하위 호환성 보장을 위한 Feature Flag 도입

### Phase 4 (장기 혁신, 7-12개월)
- Provider 프로토콜 변경과 State 형식 변경은 생태계 전체에 영향
- RFC 작성 및 커뮤니티 리뷰 과정 필요
- 최소 2개 마이너 버전에 걸친 점진적 도입

---

## 5. 기대 성과

### 정량적 성과

| 지표 | 현재 (추정) | 개선 후 (목표) | 개선율 |
|------|-----------|--------------|--------|
| 1,000 리소스 Plan 시간 | ~45초 | ~20초 | 55% |
| 5,000 리소스 Plan 시간 | ~5분 | ~1.5분 | 70% |
| State 로드 시간 (10,000 리소스) | ~8초 | ~2초 | 75% |
| 원격 State 전송 크기 | 100MB | 25MB | 75% |
| Plan 중 최대 메모리 사용량 (1,000 리소스) | ~2GB | ~800MB | 60% |
| GC 일시정지 시간 (p99) | ~50ms | ~15ms | 70% |

### 정성적 성과

1. **개발자 경험 향상**: Plan/Apply 대기 시간 단축으로 인프라 코드 개발 반복 주기(iteration cycle) 가속화
2. **대규모 인프라 관리 가능성 확대**: 현재 성능 한계로 인해 State 분할(workspace splitting)을 강제당하는 프로젝트들이 단일 State로 관리 가능
3. **코드 유지보수성 향상**: 제네릭 Set, Transformer 의존성 그래프 등으로 새로운 기여자의 진입 장벽 감소
4. **생태계 경쟁력 강화**: Pulumi, CDK 등 경쟁 도구 대비 대규모 인프라에서의 성능 우위 확보
5. **OSS 커뮤니티 기여 기회**: 각 개선 항목을 독립적인 PR로 제출하여 OpenTofu/Terraform 생태계에 기여 가능
