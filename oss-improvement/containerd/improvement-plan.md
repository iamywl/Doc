# containerd 구조적 개선 계획서

## 1. 프로젝트 개요

containerd는 CNCF 졸업 프로젝트로, Docker, Kubernetes(kubelet CRI), BuildKit 등 상위 플랫폼의 백엔드로 동작하는 산업 표준 컨테이너 런타임이다. gRPC/TTRPC 기반 클라이언트-서버 아키텍처를 채택하며, 30여 개의 플러그인이 DFS 토폴로지 정렬로 초기화되는 플러그인 시스템을 핵심 설계 원칙으로 삼고 있다.

주요 서브시스템: Content Store(CAS 기반 블롭 저장), Snapshotter(overlay CoW 파일시스템), Metadata Store(BoltDB), GC Scheduler(Tricolor Mark-Sweep), Runtime Shim v2(TTRPC 기반 프로세스 격리), CRI Plugin(Kubernetes 통합).

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 BoltDB 단일 Writer 병목 및 GC wlock 전역 차단

**현상**: containerd의 메타데이터 계층은 단일 BoltDB 인스턴스(`meta.db`)에 모든 네임스페이스의 이미지, 컨테이너, 스냅샷, 리스, 콘텐츠 메타데이터를 저장한다. BoltDB는 내부적으로 한 번에 하나의 쓰기 트랜잭션만 허용하며(`core/metadata/db.go` Update 메서드), GC 실행 시 `wlock.Lock()`으로 배타적 잠금을 획득하여 모든 쓰기 트랜잭션을 차단한다.

**병목 경로**:
- `core/metadata/db.go:260-272` - `Update()`: `wlock.RLock()` 획득 후 `db.Update()` 호출. BoltDB 내부에서 쓰기 직렬화
- `core/metadata/db.go:371-479` - `GarbageCollect()`: `wlock.Lock()` 획득 후 Mark(View 트랜잭션) + Sweep(Update 트랜잭션) 수행. 이 기간 동안 모든 `Update()` 호출이 차단됨
- Mark 단계에서 `scanRoots()`가 모든 네임스페이스의 Lease, Image, Content, Snapshot을 순회하므로, 대규모 클러스터에서 수천 개 이미지/컨테이너가 있을 때 Mark 시간이 수 초 이상 소요 가능

**영향**: 대규모 Kubernetes 노드(수백 Pod)에서 GC 실행 중 컨테이너 생성/삭제 API 응답 지연. `containerStore.Create()`, `containerStore.Delete()` 등 모든 메타데이터 변경 작업이 GC 완료까지 대기.

### 2.2 Content Store Writer 재개 시 전체 해시 재계산

**현상**: Content Store의 Writer 재개(resume) 메커니즘에서, 기존에 기록된 데이터의 다이제스트를 처음부터 전부 다시 읽어 재계산한다. 소스 코드 주석에서도 "slow slow slow!!"라고 명시되어 있다.

**병목 경로**:
- `plugins/content/local/store.go:501-529` - `resumeStatus()`: 기존 data 파일을 열어 `io.CopyBuffer(digester.Hash(), fp, *p)`로 전체 내용을 해시 재계산
- 1GB 레이어를 50% 다운로드 후 네트워크 중단 시, 재개 시점에 500MB를 다시 읽어 SHA256 해시 상태를 복원해야 함
- `bufPool`의 1MB 버퍼(`plugins/content/local/store.go:43-47`)를 사용하므로, 500MB 재해시에 약 500회의 Read + Hash 반복 필요

**영향**: 대용량 이미지 레이어(수 GB) Pull 중 네트워크 불안정 환경에서 재개 성능 저하. 특히 엣지/IoT 환경이나 불안정한 네트워크 환경에서의 이미지 배포 시간 증가.

### 2.3 GC Tricolor Mark 단계의 레이블 기반 참조 순회 비효율

**현상**: GC의 Mark 단계에서 모든 오브젝트의 레이블을 BoltDB 커서로 순회하며 `containerd.io/gc.ref.*` 프리픽스를 검색하여 참조 그래프를 구축한다. 참조 관계가 오브젝트의 레이블에 분산 저장되어 있으므로, 참조 그래프 구축 시 전체 오브젝트를 순회해야 한다.

**병목 경로**:
- `core/metadata/gc.go:875-895` - `sendLabelRefs()`: BoltDB 커서의 `Seek()`/`Next()`로 각 오브젝트의 레이블 버킷을 순회. `labelHandlers` 배열의 각 핸들러(gc.ref.content, gc.ref.snapshot, gc.ref.image, gc.bref.* 등)에 대해 프리픽스 매칭 수행
- `core/metadata/gc.go:606-699` - `references()`: 각 리소스 타입별로 참조 해석. ResourceImage의 경우 target.digest + 레이블 참조, ResourceSnapshot의 경우 parent + 레이블 참조를 모두 추적
- `core/metadata/gc.go:336-603` - `scanRoots()`: 모든 네임스페이스의 Lease, Image, Content, Snapshot, Container, Sandbox를 순회하며 루트 노드 식별
- `pkg/gc/gc.go:64-100` - `Tricolor()`: DFS 기반 도달 가능성 분석에서 `seen` 맵과 `reachable` 맵에 모든 Node를 저장하므로, 오브젝트 수에 비례하는 메모리 사용

**영향**: 이미지 수천 개, 레이어 수만 개 환경에서 GC Mark 단계 소요 시간 증가. `wlock` 보유 시간이 길어져 2.1의 문제를 악화시킴.

### 2.4 Shim 프로세스 per-Pod 모델의 메모리 오버헤드

**현상**: containerd-shim-runc-v2는 Pod당 하나의 shim 프로세스를 운영한다. 각 shim은 TTRPC 서버, processExits goroutine, forward goroutine, OOM watcher를 내장하며, `GOMAXPROCS=2`, `GCPercent=40`으로 설정해도 프로세스당 약 10-15MB의 RSS를 차지한다.

**병목 경로**:
- `cmd/containerd-shim-runc-v2/task/service.go:64-108` - `NewTaskService()`: 128 크기의 events 채널, reaper 구독, containers 맵, running 맵, exitSubscribers 맵 등 프로세스별 데이터 구조 할당
- `pkg/shim/shim.go:166-178` - `setRuntime()`: 30초마다 `debug.FreeOSMemory()` 호출로 OS에 메모리 반환 시도하지만, Go 런타임의 최소 힙 오버헤드는 불가피
- `cmd/containerd-shim-runc-v2/manager/manager_linux.go:184-283` - `Start()`: Pod 그룹핑으로 같은 Pod의 컨테이너는 shim을 공유하지만, Pod 수 자체가 많으면 shim 프로세스 수도 비례 증가

**영향**: 노드당 200+ Pod 운영 시 shim 프로세스만으로 2-3GB 메모리 소비. 고밀도 컨테이너 노드에서 시스템 가용 메모리 감소.

### 2.5 이미지 언팩 시 순차적 bottomHalf 커밋 병목

**현상**: 이미지 언팩 시스템(`core/unpack/unpacker.go`)은 topHalf(Prepare + Apply)와 bottomHalf(Commit + 라벨링)을 분리하여 topHalf는 병렬화하지만, bottomHalf는 스냅샷 체인 순서를 보장하기 위해 반드시 순차 실행해야 한다. 또한 병렬 모드는 스냅샷터의 "rebase" 기능을 요구하며, 기본 overlay 스냅샷터는 이를 지원하지 않는다.

**병목 경로**:
- `core/unpack/unpacker.go:732-741` - `supportParallel()`: `SnapshotterCapabilities`에 "rebase"가 없으면 병렬 모드 비활성화. 기본 overlay 스냅샷터는 rebase 미지원
- `core/unpack/unpacker.go:441-458` - fetch 동기화: 각 레이어의 fetchC 채널을 통해 fetch 완료를 대기한 후 Apply 진행. 순차 모드에서는 L0 fetch+apply 완료 후 L1 시작
- Content Store의 Walk(`plugins/content/local/store.go:248-306`)가 파일시스템 순회 기반이므로, GC 연동 시 블롭 수에 비례하는 순회 비용

**영향**: 10+ 레이어를 가진 대형 이미지 Pull 시 언팩 시간이 네트워크 다운로드 시간보다 오래 걸리는 경우 발생. 특히 느린 디스크(HDD, 원격 스토리지) 환경에서 bottleneck 심화.

---

## 3. 개선 제안

### 3.1 BoltDB GC 쓰기 차단 시간 최소화

**문제 상세**: GC의 `wlock.Lock()`이 Mark + Sweep + Backend Cleanup 시작까지 전체를 차단. Mark 단계가 View 트랜잭션(읽기 전용)임에도 wlock 배타적 잠금 하에 수행됨.

**근본 원인**: Mark-Sweep 사이에 새 참조가 추가되면 아직 Mark되지 않은 오브젝트를 잘못 삭제할 수 있으므로, 두 단계 사이의 쓰기를 방지해야 함. 현재는 가장 보수적인 방법인 전역 배타 잠금을 사용.

**제안 솔루션**:
1. **Write Barrier 도입**: GC Mark 중에는 쓰기를 허용하되, 쓰기 트랜잭션 내에서 새로 추가되는 참조를 별도 "write barrier set"에 기록. Sweep 시 write barrier set에 있는 노드를 추가로 reachable로 간주. Java G1 GC의 Write Barrier와 유사한 접근.
2. **점진적(Incremental) Mark 구현**: `pkg/gc/gc.go`의 `Tricolor()` 함수를 수정하여, 한 번에 전체 그래프를 순회하지 않고 N개 노드씩 처리 후 잠금 해제 -> 재획득 반복. 각 증분 단계 사이에 쓰기를 허용하고, 새 참조는 다음 증분에서 처리.
3. **네임스페이스별 분할 GC**: `scanRoots()`와 `scanAll()`을 네임스페이스별로 분할 실행. 네임스페이스 간 리소스가 독립적(ContentSharingPolicy=Isolated 경우)이면 네임스페이스별로 독립 GC 가능. Shared 정책에서도 Content는 공유하되, 나머지 리소스는 분할 가능.

**예상 효과**: GC 중 쓰기 차단 시간 50-80% 감소. 대규모 클러스터에서 컨테이너 생성/삭제 API p99 레이턴시 개선.

**구현 난이도**: 상 (Write Barrier는 BoltDB 트랜잭션 모델과의 정합성 검증 필요. 점진적 Mark는 GC 정확성 증명 필요)

---

### 3.2 Resumable Hash를 통한 Content Writer 재개 최적화

**문제 상세**: `plugins/content/local/store.go:501-529`의 `resumeStatus()`에서 기존 데이터를 전부 재읽기하여 SHA256 해시 상태를 재구축. 대용량 블롭 재개 시 I/O 및 CPU 낭비.

**근본 원인**: SHA256 해시 함수의 중간 상태를 직렬화/역직렬화하는 메커니즘이 없어, 재개 시 처음부터 다시 계산해야 함. Go 표준 라이브러리의 `crypto/sha256`은 `encoding.BinaryMarshaler`/`BinaryUnmarshaler`를 구현하지만, containerd에서 이를 활용하지 않음.

**제안 솔루션**:
1. **SHA256 상태 주기적 체크포인트**: Writer의 `Write()` 호출 시, 일정 간격(예: 매 64MB)마다 `digester.Hash().(encoding.BinaryMarshaler).MarshalBinary()`로 해시 상태를 직렬화하여 ingest 디렉토리에 `hashstate` 파일로 저장.
2. **재개 시 체크포인트 복원**: `resumeStatus()`에서 `hashstate` 파일이 있으면 해시 상태를 역직렬화(`UnmarshalBinary`)하고, 체크포인트 이후의 데이터만 추가 해싱. 500MB 중 64MB 체크포인트가 있다면 최대 64MB만 재해시.
3. **ingest 메타파일 확장**: 기존 `ref`, `startedat`, `updatedat`, `total` 파일에 `hashstate`, `hashoffset` 파일 추가. 커밋 시 이 파일들도 함께 정리.

**예상 효과**: 대용량 블롭(1GB+) 재개 시 재해시 시간 최대 95% 감소 (예: 1GB 블롭, 64MB 체크포인트 → 최대 64MB 재해시). 엣지/불안정 네트워크 환경에서 이미지 배포 안정성 향상.

**구현 난이도**: 중 (Go 표준 라이브러리의 BinaryMarshaler 지원 확인 필요. ingest 디렉토리 포맷 하위 호환성 유지)

---

### 3.3 GC 참조 그래프 인덱스 구축

**문제 상세**: GC Mark 시 모든 오브젝트의 `containerd.io/gc.ref.*` 레이블을 BoltDB 커서로 순회. 오브젝트 수에 비례하는 O(N) 순회 비용.

**근본 원인**: 참조 관계가 각 오브젝트의 레이블에 분산 저장되어 있어, 역방향 인덱스(어떤 오브젝트가 이 오브젝트를 참조하는가)가 없음. 전방 참조(`gc.ref.*`)와 역방 참조(`gc.bref.*`) 모두 레이블 순회로만 접근 가능.

**제안 솔루션**:
1. **참조 인덱스 버킷 추가**: BoltDB 스키마에 `v1/{namespace}/__gc_refs__/` 버킷을 추가하여, 레이블 설정 시 참조 관계를 인덱스에도 기록. 키: `{source_type}/{source_key}/{target_type}/{target_key}`.
2. **레이블 변경 시 인덱스 동기 업데이트**: `contentStore.Update()`, `imageStore.Create()` 등에서 `gc.ref.*` 레이블 변경 시 인덱스 버킷도 함께 갱신. 같은 BoltDB 트랜잭션 내에서 수행하므로 원자성 보장.
3. **Mark 단계 최적화**: `references()` 함수에서 레이블 순회 대신 인덱스 버킷의 프리픽스 스캔으로 참조 노드 조회. O(N) -> O(R) 개선 (R = 해당 노드의 참조 수).
4. **마이그레이션**: `core/metadata/db.go`의 `Init()` 마이그레이션 배열에 인덱스 구축 마이그레이션 추가. 기존 레이블을 스캔하여 인덱스 초기 구축.

**예상 효과**: GC Mark 단계 소요 시간 60-70% 감소 (이미지 5000개, 레이어 30000개 기준). wlock 보유 시간 단축으로 쓰기 차단 감소.

**구현 난이도**: 상 (스키마 마이그레이션, 인덱스 일관성 유지, 기존 레이블 기반 로직과의 호환성)

---

### 3.4 경량 Shim Pool 아키텍처

**문제 상세**: Pod당 독립 shim 프로세스 모델로 인한 메모리 오버헤드. 200 Pod = 200 shim 프로세스 = 2-3GB RSS.

**근본 원인**: shim v2 아키텍처가 containerd 데몬 재시작 시 컨테이너 영속성을 보장하기 위해 독립 프로세스를 사용. 각 프로세스에 Go 런타임(스케줄러, GC, 메모리 관리)의 최소 오버헤드가 발생.

**제안 솔루션**:
1. **Shim Pool 데몬 도입**: 단일 장수명(long-lived) shim 데몬이 다수의 Pod/컨테이너를 관리하는 모델. `cmd/containerd-shim-runc-v2/task/service.go`의 `containers map[string]*runc.Container`를 확장하여 여러 Pod의 컨테이너를 하나의 프로세스에서 관리.
2. **goroutine 기반 격리**: 현재 shim 프로세스별 `processExits()`, `forward()` goroutine을 Pod별 goroutine 그룹으로 재구성. 단일 reaper가 모든 컨테이너의 exit를 수거.
3. **장애 격리**: shim pool 데몬이 크래시해도 subreaper로 설정된 containerd가 컨테이너 프로세스를 인수. 또는 shim pool을 2-3개로 분할하여 장애 영향 범위 제한.
4. **TTRPC 멀티플렉싱**: 단일 TTRPC 연결에서 Pod ID를 식별자로 사용하여 요청을 멀티플렉싱. 소켓 수를 Pod 수에서 shim pool 수로 감소.

**예상 효과**: 200 Pod 기준 메모리 사용량 2-3GB -> 200-300MB (약 90% 감소). 프로세스 수 감소로 커널 스케줄링 오버헤드 감소.

**구현 난이도**: 최상 (shim v2 아키텍처의 근본적 변경. containerd 재시작 시 복구 로직 재설계 필요. 기존 shim 프로토콜과의 호환성)

---

### 3.5 Overlay Snapshotter Rebase 지원 및 병렬 언팩 활성화

**문제 상세**: 이미지 언팩 시 `supportParallel()`이 overlay 스냅샷터에서 false를 반환하여, topHalf가 순차 실행됨. 10개 레이어 이미지의 경우 L0~L9를 직렬로 Prepare+Apply.

**근본 원인**: overlay 스냅샷터(`plugins/snapshots/overlay/overlay.go`)가 Commit 시 parent 변경(rebase)을 지원하지 않음. overlay 스냅샷의 parent-child 관계는 Prepare 시점에 결정되며, Commit 후 parent를 변경하는 API가 없음.

**제안 솔루션**:
1. **Rebase Commit 구현**: overlay 스냅샷터에 `Commit()` 시 `snapshots.WithParent(newParent)` 옵션을 처리하는 로직 추가. 메타스토어에서 parent ID를 업데이트하고, lowerdir 경로를 재구성.
2. **임시 독립 Prepare + 후속 Rebase**: 병렬 모드에서 각 레이어를 parent 없이 독립적으로 Prepare+Apply한 후, bottomHalf에서 순차적으로 parent 관계를 설정(rebase)하고 Commit.
3. **SnapshotterCapabilities 등록**: overlay 스냅샷터 초기화 시 capabilities에 "rebase" 추가하여 `supportParallel()` 활성화.

**예상 효과**: 10레이어 이미지 언팩 시 Apply 단계(CPU/IO 바운드) 병렬화로 언팩 시간 40-60% 단축. SSD 환경에서 효과 극대화.

**구현 난이도**: 중상 (overlay 스냅샷 메타스토어의 parent 관계 변경 안전성 검증. overlayfs lowerdir 순서가 스냅샷 parent 체인과 일치하는지 확인)

---

## 4. 우선순위 및 로드맵

### Phase 1: 단기 (1-3개월) - 즉시 효과, 낮은 리스크

| 순위 | 개선 항목 | 예상 공수 | 영향도 |
|------|----------|----------|--------|
| 1 | 3.2 Resumable Hash | 2-3주 | 엣지/불안정 네트워크 환경 개선 |
| 2 | 3.1-3 네임스페이스별 분할 GC (부분 구현) | 3-4주 | Isolated 정책 환경에서 GC 성능 개선 |

### Phase 2: 중기 (3-6개월) - 핵심 성능 개선

| 순위 | 개선 항목 | 예상 공수 | 영향도 |
|------|----------|----------|--------|
| 3 | 3.5 Overlay Rebase + 병렬 언팩 | 4-6주 | 이미지 Pull 성능 개선 |
| 4 | 3.3 GC 참조 인덱스 | 6-8주 | 대규모 환경 GC 성능 개선 |

### Phase 3: 장기 (6-12개월) - 아키텍처 수준 개선

| 순위 | 개선 항목 | 예상 공수 | 영향도 |
|------|----------|----------|--------|
| 5 | 3.1-1,2 Write Barrier / 점진적 Mark | 8-12주 | GC 쓰기 차단 근본 해결 |
| 6 | 3.4 경량 Shim Pool | 12-16주 | 고밀도 노드 메모리 효율 |

### 의존성 그래프

```
Phase 1: [3.2 Resumable Hash] ─────────────────────────────────────→
         [3.1-3 분할 GC] ──→ Phase 2: [3.3 GC 인덱스] ──→ Phase 3: [3.1 Write Barrier]
                              [3.5 병렬 언팩] ──────────→
                                                          [3.4 Shim Pool] ──→
```

---

## 5. 기대 성과

### 정량적 성과

| 지표 | 현재 추정치 | 개선 목표 | 개선 항목 |
|------|-----------|----------|----------|
| GC 중 API 응답 지연 (p99) | 수 초 (대규모 환경) | < 200ms | 3.1, 3.3 |
| 1GB 블롭 재개 시 재해시 시간 | ~5초 (전체 재해시) | < 0.5초 | 3.2 |
| GC Mark 단계 소요 시간 (5000 이미지) | 수 초 | < 1초 | 3.3 |
| 10레이어 이미지 언팩 시간 | 순차 N초 | 0.4N~0.6N초 | 3.5 |
| 200 Pod shim 메모리 | 2-3GB | 200-300MB | 3.4 |

### 정성적 성과

- **엣지 컴퓨팅 환경 적합성 향상**: Resumable Hash로 불안정 네트워크에서의 이미지 배포 안정성 확보
- **대규모 Kubernetes 클러스터 지원 강화**: GC 최적화로 노드당 수백 Pod 환경에서의 안정성 향상
- **고밀도 컨테이너 노드 지원**: Shim Pool로 노드당 컨테이너 집적도 증가
- **containerd 커뮤니티 기여**: 업스트림 PR을 통한 오픈소스 생태계 기여 및 조직 기술력 입증
- **CNCF 프로젝트 깊이 있는 이해**: 향후 다른 CNCF 런타임(CRI-O, Kata Containers) 분석 및 기여의 기반 마련
