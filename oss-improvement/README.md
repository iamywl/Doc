# OSS 개선 프로젝트 계획서 총괄

> 16개 오픈소스 프로젝트의 성능 개선 및 구조적 한계점 분석/개선 계획

## 프로젝트 현황

| # | 프로젝트 | 분류 | 핵심 개선 포인트 | 계획서 |
|---|---------|------|----------------|--------|
| 1 | **Cilium** | 네트워킹/보안 | FQDN DNS Proxy 병목, Policy Engine 락 경합, ConnTrack LRU 메모리 압박, Hubble 이벤트 파이프라인 | [계획서](cilium/improvement-plan.md) |
| 2 | **Kubernetes** | 오케스트레이션 | etcd Cacher watchCache 비효율, Scheduler 단일스레드 직렬화, PLEG 폴링 지연, DeltaFIFO 락 경합 | [계획서](kubernetes/improvement-plan.md) |
| 3 | **Argo CD** | GitOps/CD | Application Controller RWMutex 경합, Repo Server repoLock 직렬화, ClusterCache 전체 재동기화 | [계획서](argo-cd/improvement-plan.md) |
| 4 | **Jenkins** | CI/CD | Queue 단일 락/O(N) 탐색, XML 영속성 I/O, 싱글턴 God Object, ClassLoader 메모리 | [계획서](jenkins/improvement-plan.md) |
| 5 | **containerd** | 컨테이너 런타임 | BoltDB GC 글로벌 락, Content Store 재해시, GC 라벨 순회, Per-Pod shim 메모리 | [계획서](containerd/improvement-plan.md) |
| 6 | **gRPC-Go** | RPC 프레임워크 | controlBuffer 뮤텍스 경합, 고정 워커풀, pickerWrapper 썬더링 허드, DNS Resolver 폴링 | [계획서](grpc-go/improvement-plan.md) |
| 7 | **Tart** | macOS 가상화 | OCI Pull 직렬화, DiskV2 Push 메모리 압박, 토큰 캐싱, 프루닝 stat 오버헤드 | [계획서](tart/improvement-plan.md) |
| 8 | **Helm** | 패키지 매니저 | Storage 인코딩 비효율, Repository 인덱스 전체 로딩, 템플릿 funcMap 초기화, 순차 다운로드 | [계획서](helm/improvement-plan.md) |
| 9 | **Terraform** | IaC | DAG TransitiveReduction O(V²+VE), SyncState DeepCopy, Provider gRPC 직렬화, State v4 단일 JSON | [계획서](terraform/improvement-plan.md) |
| 10 | **Alertmanager** | 알림 관리 | nflog 선형 스캔, Silence 캐시 무효화, Inhibitor 선형 규칙 순회, 고정 버퍼 드롭 | [계획서](alertmanager/improvement-plan.md) |
| 11 | **Hubble** | 네트워크 관측 | Ring Buffer uint16 용량 제한, Relay PriorityQueue 병목, Parser Mutex 직렬화, 메트릭 카디널리티 | [계획서](hubble/improvement-plan.md) |
| 12 | **Kafka** | 메시징/스트리밍 | Log Compaction SkimpyOffsetMap, Processor 스레드 모델, ISR HW 전파 지연, KRaft 메타데이터 | [계획서](kafka/improvement-plan.md) |
| 13 | **Grafana** | 시각화/모니터링 | 대시보드 버전 JSON 중복, OSS 쿼리 캐싱 no-op, Alert 상태 영속화, 검색 LIKE 풀스캔 | [계획서](grafana/improvement-plan.md) |
| 14 | **Loki** | 로그 수집 | Ingester flush 직렬화, Querier Loser Tree 병합, Compactor 리더 선출, Bloom 필터 FPR | [계획서](loki/improvement-plan.md) |
| 15 | **Jaeger** | 분산 추적 | v1/v2 어댑터 변환, Cassandra 인덱스 fan-out, Memory 스토리지 O(n), 적응형 샘플링 지연 | [계획서](jaeger/improvement-plan.md) |
| 16 | **Istio** | 서비스 메시 | PushContext 전체 재구성, SotW xDS 대역폭, Aggregate Controller O(N*M) 병합, 멀티클러스터 직렬 처리 | [계획서](istio/improvement-plan.md) |

## 카테고리별 분류

### 인프라/오케스트레이션
- Kubernetes, containerd, Terraform

### 네트워킹/서비스 메시
- Cilium, Istio, gRPC-Go

### CI/CD/GitOps
- Argo CD, Jenkins, Helm

### 관측성(Observability)
- Grafana, Loki, Jaeger, Hubble, Alertmanager

### 메시징/런타임
- Kafka, Tart

## 공통 패턴 분석

분석을 통해 발견된 반복적인 구조적 문제 패턴:

1. **글로벌 락/뮤텍스 경합** - Kubernetes, Jenkins, Cilium, Hubble, containerd 등 다수 프로젝트에서 단일 RWMutex/Lock이 병목
2. **선형 탐색 O(N)** - Jenkins Queue, Alertmanager Inhibitor, Hubble 필터 등에서 인덱스 없는 순차 탐색
3. **전체 재구성/재동기화** - Istio PushContext, Argo CD ClusterCache, Kubernetes PLEG에서 증분 업데이트 부재
4. **직렬화/인코딩 오버헤드** - Helm gzip, Terraform JSON, Jenkins XML에서 반복적 직렬화 비용
5. **고정 크기/폴링 기반 설계** - gRPC-Go 워커풀, Hubble Ring Buffer, gRPC DNS Resolver에서 적응형 조절 부재

