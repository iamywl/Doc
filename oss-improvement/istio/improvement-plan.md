# Istio 오픈소스 개선 계획서

## 1. 프로젝트 개요

Istio는 Kubernetes 환경에서 서비스 간 통신을 투명하게 관리하는 서비스 메시 플랫폼이다. 컨트롤 플레인(Istiod)과 데이터 플레인(Envoy Sidecar, Ztunnel)으로 구성되며, xDS 프로토콜을 통해 프록시 설정을 동적으로 배포한다.

핵심 컴포넌트:
- **Pilot (DiscoveryServer)**: xDS gRPC 스트림 관리, 디바운싱, PushQueue를 통한 설정 배포
- **ConfigGenerator**: Kubernetes CRD(VirtualService, DestinationRule 등)를 Envoy xDS 리소스(CDS/LDS/RDS/EDS)로 변환
- **Service Registry**: Aggregate Controller 패턴으로 다중 클러스터 서비스/엔드포인트 통합
- **krt 프레임워크**: 선언적 데이터 변환 파이프라인 (Kubernetes Declarative Controller Runtime)
- **Ambient Mesh**: Ztunnel(Rust, L4) + Waypoint(Envoy, L7) 2계층 아키텍처

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 PushContext 전체 재구성 비용

**현상**: 설정 변경 시 `PushContext`를 완전히 새로 구성한다. `pilot/pkg/model/push_context.go`의 `InitContext()` 메서드는 `ServiceIndex`, `VirtualServiceIndex`, `DestRuleIndex` 등 모든 인덱스를 처음부터 재구축한다. `pilot_pushcontext_init_seconds` 메트릭으로 측정되며, 대규모 클러스터(서비스 5,000개 이상, 엔드포인트 100,000개 이상)에서 수백ms~수초 소요된다.

**영향 경로**: `ConfigUpdate()` -> `pushChannel` -> `debounce()` -> `Push()` -> `InitContext()` -> 전체 인덱스 재구축 -> `PushQueue.Enqueue()`. 이 과정에서 디바운싱 최대 대기시간(기본 10초)을 초과하여 설정 수렴 지연이 발생할 수 있다.

### 2.2 SotW(State of the World) xDS의 대역폭 비효율

**현상**: SotW ADS 모드에서 `pushConnection()` (`pilot/pkg/xds/ads.go:472`)은 매 푸시마다 해당 타입의 전체 리소스를 전송한다. CDS 푸시 시 클러스터가 3,000개이면 3,000개 전부를 매번 직렬화하여 전송한다. Delta xDS(`StreamDeltas`, `pilot/pkg/xds/delta.go`)가 구현되어 있지만, 모든 Generator가 `XdsDeltaResourceGenerator` 인터페이스를 구현하지는 않으며, 미구현 시 `Generate()` 폴백으로 SotW 동작을 한다.

**영향 경로**: `pushDeltaXds()`에서 `XdsResourceGenerator`로 폴백 시 -> `Generate()` 호출 -> 전체 리소스 반환 -> 이전 `ResourceNames`와 diff하여 `RemovedResources` 계산. 이 폴백 경로는 Delta의 이점을 상쇄한다.

### 2.3 Aggregate Controller의 O(N*M) 서비스 병합

**현상**: `pilot/pkg/serviceregistry/aggregate/controller.go`의 `Services()` 메서드(340행)는 모든 레지스트리를 순회하며 호스트명 기반으로 서비스를 병합한다. 멀티클러스터 환경(클러스터 5개, 클러스터당 서비스 2,000개)에서 `mergeService()`가 `ClusterVIPs.SetAddressesFor()`와 `ServiceAccounts` 슬라이스 합치기를 반복 수행한다. `Services()`는 PushContext 초기화, CDS/LDS 생성 등 핫 패스에서 빈번히 호출된다.

**영향 경로**: `PushContext.InitContext()` -> `env.Services()` -> `Aggregate.Services()` -> 각 레지스트리 `Services()` 호출 -> `smap` 맵 조회 + `mergeService()` -> `ShallowCopy()` + `slices.FilterDuplicates()`. 매 PushContext 재구성마다 전체 서비스 목록을 새로 합성한다.

### 2.4 Envoy 설정 생성 파이프라인의 프록시별 반복 연산

**현상**: `pilot/pkg/networking/core/` 디렉토리의 `ConfigGeneratorImpl`은 각 프록시 연결에 대해 CDS(`cluster.go:BuildClusters`), LDS(`listener.go`), RDS(`route/route.go`) 설정을 독립적으로 생성한다. 동일한 네임스페이스에 있는 수백 개의 동일 Deployment 프록시들이 사실상 같은 설정을 받지만, 각각 별도로 `ClusterBuilder.buildOutboundClusters()`, `TranslateRouteMatch()`, `applyTrafficPolicy()`를 실행한다. XDS 캐시(`model.XdsCache`)가 존재하지만, 캐시 키가 프록시의 `SidecarScope`와 `ServiceTargets`에 의존하여 캐시 적중률이 제한적이다.

**영향 경로**: `sendPushes()` -> `PushQueue.Dequeue()` -> `pushConnection()` -> `pushXds()` -> `findGenerator()` -> `CdsGenerator.Generate()` / `LdsGenerator.Generate()` -> 프록시별 전체 설정 생성. `concurrentPushLimit` 세마포어(최대 100)로 병렬 처리하지만, 프록시 수가 수천 개면 순차적 대기가 발생한다.

### 2.5 멀티클러스터 Secret Controller의 단일 장애점 및 동기화 지연

**현상**: `pkg/kube/multicluster/secretcontroller.go`의 Secret Controller는 `istio-system` 네임스페이스의 `istio/multiCluster=true` Secret을 감시하여 원격 클러스터를 등록한다. `addSecret()`에서 각 클러스터별 `createRemoteCluster()` -> `kubeClient` 생성 -> Informer 시작 -> 캐시 동기화 대기가 순차적으로 수행된다. `ClusterStore`는 `sync.RWMutex`로 보호되는 2-레벨 맵(`secretKey -> clusterID -> *Cluster`)이며, 클러스터 추가/삭제 시 전체 잠금이 발생한다.

**영향 경로**: Secret 변경 감지 -> `queue.AddObject()` -> `addSecret()` -> `createRemoteCluster()` (순차) -> `Cluster.Run()` (고루틴) -> Informer `WaitForCacheSync()` -> `Aggregate.AddRegistryAndRun()`. `Make-Before-Break` 패턴(`PendingClusterSwap`)이 있지만, 새 클러스터 동기화 완료 전까지 해당 클러스터의 서비스/엔드포인트가 누락된다.

---

## 3. 개선 제안

### 3.1 PushContext 증분 업데이트 도입

**문제 상세**: PushContext 초기화(`InitContext()`)는 불변 스냅샷 생성을 위해 매번 전체 인덱스를 재구축한다. `ServiceIndex`, `VirtualServiceIndex`, `DestRuleIndex`, `EnvoyFilterIndex` 등 모든 인덱스가 대상이다. 서비스 1개가 추가되어도 전체 인덱스를 재구축하는 것은 O(N) 비용이 발생한다.

**근본 원인**: PushContext의 "불변 스냅샷" 설계 원칙이 COW(Copy-on-Write) 또는 증분 업데이트를 고려하지 않았다. `model.PushRequest`에 `ConfigsUpdated` 필드(변경된 설정 집합)가 있지만, `InitContext()`에서 이 정보를 활용하여 부분 재구축을 수행하는 로직이 없다.

**제안 솔루션**:
1. `PushRequest.ConfigsUpdated`의 `ConfigKey` 정보를 활용하여 영향받는 인덱스만 선별적으로 재구축
2. 이전 PushContext에서 변경되지 않은 인덱스는 포인터 공유(COW 패턴) 적용
3. `ServiceIndex`를 불변 트리(persistent data structure) 기반으로 재구현하여 O(log N) 갱신 지원
4. `InitContext()`에 `prevContext *PushContext` 파라미터 추가, 변경 범위에 따라 `initServiceRegistry()`, `initVirtualServices()` 등을 선택적으로 호출

**예상 효과**:
- 단일 서비스/엔드포인트 변경 시 PushContext 초기화 시간 70-90% 감소
- `pilot_pushcontext_init_seconds` 메트릭 개선
- 대규모 클러스터에서 `pilot_proxy_convergence_time` 단축

**구현 난이도**: 상 (PushContext 핵심 설계 변경, 동시성 안전성 검증 필요, 기존 테스트 케이스 전면 수정)

---

### 3.2 Delta xDS Generator 전면 확대 및 SotW 폴백 최적화

**문제 상세**: `pilot/pkg/xds/delta.go`의 `pushDeltaXds()`에서 Generator가 `XdsDeltaResourceGenerator` 인터페이스를 구현하지 않으면 `XdsResourceGenerator.Generate()`로 폴백한다. 이 경우 전체 리소스를 생성한 후 `w.ResourceNames`와 diff하여 추가/삭제 목록을 계산한다(`removed := w.ResourceNames.Copy()` -> 새 리소스에서 `removed.Delete()`). CDS는 `BuildDeltaClusters()`가 구현되어 있지만, LDS와 RDS는 Delta Generator가 없다.

**근본 원인**: LDS와 RDS의 설정 생성이 프록시의 `SidecarScope`, `MergedGateway` 등 복잡한 컨텍스트에 의존하여, 변경된 리소스만 식별하는 Delta 로직 구현이 복잡하다. `pilot/pkg/networking/core/listener.go`의 리스너 빌드 로직이 인바운드/아웃바운드를 통합 생성하므로 부분 생성이 어렵다.

**제안 솔루션**:
1. `LdsGenerator`에 `GenerateDeltas()` 구현: `PushRequest.ConfigsUpdated`에서 변경된 서비스/VirtualService를 추출하고, 영향받는 리스너만 재생성
2. `RdsGenerator`에 `GenerateDeltas()` 구현: 변경된 VirtualService의 호스트와 매칭되는 라우트만 재생성
3. SotW 폴백 경로에서 이전 응답과의 바이너리 diff를 캐시하여 실제 변경된 리소스만 직렬화
4. `pilot/pkg/xds/xdsgen.go`의 `pushXds()`에 리소스 해시 기반 중복 전송 방지 로직 추가

**예상 효과**:
- VirtualService 1개 변경 시 LDS/RDS 전송량 90% 이상 감소
- Envoy의 리스너 드레인 및 재구성 비용 절감
- 네트워크 대역폭 절약 (특히 멀티클러스터 환경)

**구현 난이도**: 중상 (기존 CDS Delta 패턴을 참조하여 LDS/RDS에 적용, 리스너 구조의 복잡성이 난이도를 높임)

---

### 3.3 Aggregate Controller 서비스 캐시 레이어 도입

**문제 상세**: `Aggregate.Services()`는 호출될 때마다 모든 레지스트리를 순회하고 `smap` 해시맵을 새로 구축한다. 멀티클러스터 환경에서 레지스트리 5개 x 서비스 2,000개 = 10,000회 맵 조회 + 병합 연산이 매번 발생한다. `mergeService()`의 `ShallowCopy()`는 `ClusterVIPs`가 2개 미만일 때 수행되며, `slices.FilterDuplicates()`는 ServiceAccount 슬라이스를 매번 재정렬한다.

**근본 원인**: Aggregate Controller에 캐시 레이어가 없다. 각 레지스트리의 `Services()` 호출 결과가 매번 새로 합성되며, 레지스트리 이벤트(서비스 추가/삭제/수정)와 캐시 무효화가 연계되지 않는다. `storeLock`(RWMutex)는 레지스트리 목록 보호용이지 서비스 캐시 보호용이 아니다.

**제안 솔루션**:
1. `Aggregate Controller`에 `mergedServicesCache map[host.Name]*model.Service` 필드 추가
2. 각 레지스트리의 `AppendServiceHandler()`를 통해 서비스 변경 이벤트 수신 시 해당 호스트명의 캐시 엔트리만 무효화
3. `Services()` 호출 시 캐시 유효하면 즉시 반환, 무효화된 엔트리만 해당 레지스트리에서 재조회/재병합
4. 캐시 구현은 `sync.Map` 또는 단일 `RWMutex` + `map`으로 경합 최소화
5. `mergeService()`의 `ServiceAccounts` 병합을 `sets.String` 기반으로 변경하여 O(N) 중복 제거 -> O(1) 집합 연산

**예상 효과**:
- `Services()` 호출 비용: O(전체 서비스) -> O(변경된 서비스) (캐시 적중 시 O(1))
- PushContext 초기화 시간 30-50% 감소 (대규모 멀티클러스터 환경)
- GC 압력 감소 (임시 슬라이스/맵 할당 제거)

**구현 난이도**: 중 (캐시 일관성 보장이 핵심, 레지스트리 이벤트와 캐시 무효화의 정확한 매핑 필요)

---

### 3.4 xDS 설정 생성의 프록시 그룹 기반 캐싱 강화

**문제 상세**: `sendPushes()` 루프에서 `PushQueue.Dequeue()` -> `pushConnection()` -> `pushXds()` -> `Generator.Generate()`가 프록시별로 호출된다. 동일 Deployment의 50개 Pod는 같은 `SidecarScope`, 같은 레이블, 같은 `ServiceTargets`를 가지므로 사실상 동일한 CDS/LDS/RDS 설정을 받는다. 현재 `XdsCache`의 캐시 키는 `proxy.SidecarScope`와 관련 설정의 조합이지만, `proxy.IPAddresses`나 `proxy.ID` 등 프록시 고유 속성이 설정 생성에 영향을 주어 캐시 미스가 발생한다.

**근본 원인**: EDS를 제외한 CDS/LDS/RDS 설정은 프록시의 "논리적 그룹"(같은 Deployment, 같은 네임스페이스, 같은 SidecarScope)에 대해 동일하다. 그러나 캐시 키 설계가 이 그룹 단위가 아닌 개별 프록시 속성에 의존한다. `pilot/pkg/model/context.go`의 `Proxy` 구조체에서 설정 생성에 영향을 주는 필드와 주지 않는 필드가 명시적으로 분리되지 않았다.

**제안 솔루션**:
1. `Proxy`에 `ConfigGroupKey() string` 메서드 추가: `SidecarScope.Name`, `ConfigNamespace`, `Labels`(설정 관련 레이블만), `Type`의 해시값 반환
2. `XdsCache` 키에 `ConfigGroupKey`를 1차 키로 사용, 프록시 고유 속성(IP 등)은 EDS에서만 2차 키로 사용
3. `pushConnection()`에서 동일 `ConfigGroupKey`의 첫 프록시 설정 생성 결과를 캐시하고, 이후 프록시는 캐시된 결과를 직렬화만 수행
4. `PushQueue`에 그룹 기반 배치 디큐 기능 추가: 같은 `ConfigGroupKey`의 프록시를 한꺼번에 꺼내어 설정 생성 1회 + N회 전송

**예상 효과**:
- 동일 Deployment 50개 Pod 기준 설정 생성 연산 98% 감소 (50회 -> 1회)
- `pilot_xds_push_time` 메트릭 대폭 개선
- CPU 사용량 감소로 Istiod 수평 확장 지연

**구현 난이도**: 중상 (프록시 그룹 식별 정확도가 핵심, EDS는 프록시별 차이가 있으므로 별도 처리 필요)

---

### 3.5 멀티클러스터 Secret Controller 병렬화 및 점진적 동기화

**문제 상세**: `addSecret()`에서 `s.Data`의 각 `clusterID`에 대해 `createRemoteCluster()`가 순차 호출된다. 새 Secret에 5개 클러스터 kubeconfig가 있으면 5개 `kubeClient`가 순차 생성된다. 각 `Cluster.Run()`은 고루틴으로 시작되지만, `WaitForCacheSync()`가 완료되기 전까지 해당 클러스터의 서비스가 Aggregate Controller에 반영되지 않는다. `ClusterStore`의 `sync.RWMutex`는 클러스터 추가/삭제 시 쓰기 잠금으로 다른 읽기 연산을 차단한다.

**근본 원인**: Secret Controller의 이벤트 처리가 단일 워커 큐(`queue.AddObject`)를 사용하여 순차 처리된다. `createRemoteCluster()`의 kubeconfig 파싱 + TLS 핸드셰이크 + 초기 API 호출이 클러스터별로 1-5초 소요되며, 이것이 직렬화된다.

**제안 솔루션**:
1. `addSecret()`의 클러스터별 처리를 `errgroup.Group`으로 병렬화 (`MaxGoroutines` 제한 적용)
2. `ClusterStore`를 `sync.Map` 기반으로 변경하여 클러스터별 독립적인 잠금 범위 확보
3. 점진적 동기화: 클러스터 추가 즉시 빈 레지스트리를 Aggregate Controller에 등록하고, Informer 캐시 동기화가 완료되면 서비스 이벤트를 트리거하여 xDS 푸시
4. 클러스터 Health Check 강화: `createRemoteCluster()` 전에 API 서버 연결 확인, 실패 시 지수 백오프 재시도 (현재는 `Cluster.Run()` 내부에서만 재시도)
5. `ClusterStore.clustersAwaitingSync` 상태를 Prometheus 메트릭으로 노출하여 동기화 지연 가시성 확보

**예상 효과**:
- 5개 클러스터 동시 추가 시 동기화 시간: 직렬 15-25초 -> 병렬 3-5초
- 클러스터 추가/제거 중 읽기 연산(서비스 조회) 차단 제거
- 운영자의 클러스터 동기화 상태 가시성 향상

**구현 난이도**: 중 (errgroup 병렬화는 직관적, ClusterStore 잠금 범위 축소 시 일관성 검증 필요)

---

## 4. 우선순위 및 로드맵

### Phase 1: 단기 (1-2개월) - 높은 ROI, 낮은 리스크

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 1 | 3.3 Aggregate Controller 서비스 캐시 | 구현 난이도 중, PushContext 경로의 즉각적 성능 개선 |
| 2 | 3.5 멀티클러스터 Secret Controller 병렬화 | 구현 범위가 명확, 멀티클러스터 환경에서 체감 효과 큼 |

### Phase 2: 중기 (2-4개월) - 핵심 아키텍처 개선

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 3 | 3.4 프록시 그룹 기반 캐싱 강화 | 대규모 클러스터에서 Istiod CPU 사용량 직접 감소 |
| 4 | 3.2 Delta xDS Generator 확대 | LDS/RDS Delta 구현은 CDS Delta 패턴 참조 가능 |

### Phase 3: 장기 (4-6개월) - 근본적 설계 혁신

| 순위 | 개선 항목 | 근거 |
|------|----------|------|
| 5 | 3.1 PushContext 증분 업데이트 | 가장 큰 효과이나 핵심 설계 변경으로 리스크 높음, 충분한 테스트 필요 |

### 마일스톤 다이어그램

```
Month 1          Month 2          Month 3          Month 4          Month 5          Month 6
|---Phase 1------|                |                |                |                |
|  캐시 레이어    |---Phase 2------|----------------|                |                |
|  Secret 병렬화  |  프록시 그룹    |  Delta LDS/RDS |---Phase 3------|----------------|
|                |  캐싱           |                |  PushContext   |  테스트/안정화  |
|                |                |                |  증분 업데이트  |                |
```

---

## 5. 기대 성과

### 정량적 성과 목표

| 메트릭 | 현재 (서비스 5,000개 기준 추정) | 목표 | 개선율 |
|--------|-------------------------------|------|--------|
| `pilot_pushcontext_init_seconds` | 500ms - 2s | 50ms - 200ms | 75-90% |
| `pilot_proxy_convergence_time` (P99) | 5-15s | 1-3s | 70-80% |
| `pilot_xds_push_time` (P99) | 200-500ms | 20-50ms | 85-90% |
| `pilot_xds_config_size_bytes` (LDS/RDS) | 전체 리소스 크기 | 변경분만 | 80-95% |
| Istiod CPU 사용량 (프록시 1,000개) | 높음 | 60% 감소 | 60% |
| 멀티클러스터 동기화 시간 (5 클러스터) | 15-25s | 3-5s | 75-80% |

### 정성적 성과

1. **확장성 향상**: 단일 Istiod 인스턴스가 관리할 수 있는 프록시 수 증가 (현재 ~5,000 -> 목표 ~15,000)
2. **운영 안정성**: 설정 변경 시 수렴 시간 단축으로 롤링 업데이트 중 불일치 윈도우 축소
3. **멀티클러스터 신뢰성**: 클러스터 추가/제거 시 서비스 가용성 영향 최소화
4. **리소스 효율**: Istiod의 CPU/메모리 사용량 감소로 운영 비용 절감
5. **커뮤니티 기여 가치**: PushContext 증분 업데이트와 Delta LDS/RDS는 Istio 커뮤니티에서 오랜 기간 요청된 개선 사항으로, 업스트림 기여 시 높은 영향력

### 리스크 및 완화 전략

| 리스크 | 완화 전략 |
|--------|----------|
| PushContext 증분 업데이트의 일관성 버그 | 기존 전체 재구축과 증분 결과 비교하는 shadow 모드 도입 |
| 캐시 무효화 누락으로 stale 설정 배포 | 주기적 캐시 전체 무효화 메커니즘 (failsafe) + 메트릭 알림 |
| Delta Generator 구현 오류로 Envoy warming 무한 대기 | `forceEDSPush()` 패턴 확대 적용, 타임아웃 기반 SotW 폴백 |
| 멀티클러스터 병렬화 시 Race Condition | `errgroup` + 클러스터별 독립 잠금으로 격리, 통합 테스트 강화 |
