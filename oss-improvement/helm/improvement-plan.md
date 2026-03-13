# Helm v4 구조적 개선 제안서

## 1. 프로젝트 개요

Helm은 Kubernetes 패키지 매니저로, 차트(Chart) 형식을 사용하여 애플리케이션을 정의, 설치, 업그레이드한다. v4에서는 클라이언트 전용 아키텍처를 채택하여 Tiller 없이 직접 Kubernetes API Server와 통신하며, Server-Side Apply(SSA)를 기본 적용 방식으로 채택하였다.

핵심 아키텍처는 4개 계층으로 구성된다:
- **CLI 계층** (`cmd/helm`, `pkg/cmd`): Cobra 기반 커맨드 파싱
- **Action 계층** (`pkg/action`): `Configuration` 의존성 컨테이너를 중심으로 한 비즈니스 로직
- **서비스 계층** (`pkg/engine`, `pkg/storage`, `pkg/kube`): 템플릿 렌더링, 릴리스 저장, K8s 리소스 관리
- **드라이버 계층** (`pkg/storage/driver`): Secret/ConfigMap/Memory/SQL 백엔드 구현

본 문서는 Helm v4의 소스코드 수준 분석을 기반으로 구조적 한계점을 식별하고, 구체적인 개선 방안을 제시한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 스토리지 드라이버의 Release 인코딩/디코딩 비효율

**현상**: 모든 스토리지 드라이버(Secrets, ConfigMaps, SQL)에서 Release 객체를 저장/조회할 때 `JSON Marshal -> gzip(BestCompression) -> base64 Encode` 파이프라인을 거친다 (`pkg/storage/driver/util.go`의 `encodeRelease()`/`decodeRelease()`).

**문제 상세**:
- `gzip.BestCompression` (레벨 9)은 CPU 소비가 높지만, `BestSpeed` (레벨 1) 대비 압축률 개선이 10-15% 수준에 불과
- `List()` 연산 시 모든 릴리스를 순회하며 개별적으로 `decodeRelease()`를 호출하므로 O(n) 디코딩 비용 발생
- Secrets 드라이버의 `List()`는 `owner=helm` 레이블로 전체 Secret을 가져온 후 인메모리에서 filter 콜백을 적용 -- 대규모 클러스터(수백 개 릴리스)에서 모든 릴리스를 디코딩한 후 필터링
- Release 객체에 `Chart` 전체(템플릿, 기본 Values, 메타데이터)가 포함되어 단일 릴리스가 수백 KB~수 MB에 달함

**영향 범위**: `helm list`, `helm history`, `Storage.Deployed()`, `Storage.Last()` 등 조회 계열 모든 명령

### 2.2 리포지토리 인덱스 파일의 메모리 전량 적재

**현상**: `pkg/repo/v1/index.go`의 `loadIndex()` 함수는 인덱스 파일 전체를 메모리에 파싱하여 `IndexFile.Entries` (타입: `map[string]ChartVersions`)에 적재한다.

**문제 상세**:
- Bitnami 같은 대형 리포지토리의 `index.yaml`은 수십 MB에 달하며, 수천 개의 차트 x 수십 개 버전 항목을 포함
- `helm search repo`, `helm dependency update`, `helm install repo/chart` 모두 `LoadIndexFile()`을 호출하여 전체 인덱스를 파싱
- `loadIndex()` 내부에서 모든 엔트리를 순회하며 `Validate()` 및 `SortEntries()` (SemVer 비교 기반 `sort.Sort(sort.Reverse(...))`)를 수행
- `IndexFile.Get()` 메서드는 정렬된 리스트를 선형 탐색(`constraint.Check()`)하여 매칭 -- 인덱스 구조가 아닌 순차 검색
- `jsonOrYamlUnmarshal()`에서 YAML 파싱은 JSON 대비 약 5-10배 느리지만, 대부분의 리포지토리가 YAML 형식 사용

**영향 범위**: `helm repo update`, `helm search repo`, `helm dependency update/build`, `helm install`

### 2.3 템플릿 엔진의 반복적 함수 맵 초기화 및 단일 스레드 렌더링

**현상**: `pkg/engine/engine.go`의 `render()` 메서드에서 매 렌더링마다 `funcMap()` (sprig 전체 함수 로드) -> `initFunMap()` (include/tpl/lookup 클로저 바인딩) 과정을 반복한다.

**문제 상세**:
- `funcMap()`은 `sprig.TxtFuncMap()`으로 100개 이상의 함수를 맵에 복사한 후, `env`/`expandenv`를 삭제하고, Helm 자체 함수(toYaml, fromYaml, include, tpl, lookup 등)를 추가
- 매 `Render()` 호출마다 이 과정이 반복되지만, sprig 함수 맵은 불변이므로 한 번만 생성하면 충분
- `allTemplates()` -> `recAllTpls()`의 재귀 호출에서 서브차트마다 Values를 복사하고 `renderable` 맵을 구성하는데, `copystructure.Copy()` (딥 카피)가 차트 계층 깊이만큼 호출됨
- 서브차트가 많은 대형 차트(예: umbrella chart with 20+ subcharts)에서 렌더링 시간이 선형 이상으로 증가
- `sortTemplates()`는 경로 깊이 기반 정렬을 매번 수행하지만, 차트 구조가 변경되지 않으면 캐싱 가능

**영향 범위**: `helm install`, `helm upgrade`, `helm template`, `helm lint` 등 렌더링이 수반되는 모든 명령

### 2.4 의존성 해석의 순차적 다운로드 및 캐시 미활용

**현상**: `pkg/downloader/manager.go`의 `downloadAll()` 함수는 의존성 차트를 순차적으로 다운로드한다. 리포지토리 인덱스 업데이트(`parallelRepoUpdate`)는 병렬이지만, 실제 차트 아카이브 다운로드는 `for _, dep := range deps` 루프에서 직렬 처리된다.

**문제 상세**:
- `ChartDownloader.DownloadTo()`가 순차적으로 호출되어 네트워크 레이턴시가 의존성 개수에 비례하여 누적
- `DiskCache` (`pkg/downloader/cache.go`)가 SHA256 기반 컨텐츠 어드레서블 캐시를 제공하지만, `downloadAll()` 내부에서 캐시 히트 확인 로직이 다운로드 이후에 적용됨 -- 캐시에 이미 존재하는 차트도 다운로드를 시도
- `Resolver.Resolve()`에서 OCI 레지스트리의 태그 목록 조회(`registryClient.Tags()`)가 의존성마다 개별 호출되어, 동일 레지스트리에 대한 연결 재사용이 이루어지지 않음
- `ensureMissingRepos()`에서 미등록 리포지토리 발견 시 SHA256 해시 기반 임시 이름을 생성하지만, 이 임시 인덱스는 명령 종료 후 삭제되지 않아 캐시 디렉토리에 잔류

**영향 범위**: `helm dependency update`, `helm dependency build`, `helm install` (의존성 자동 해석 시)

### 2.5 Values 병합(Coalescing)의 반복적 딥 카피와 비효율적 null 처리

**현상**: `pkg/chart/common/util/coalesce.go`의 `CoalesceValues()`/`MergeValues()`는 매 호출마다 `copystructure.Copy()`로 전체 Values 트리를 딥 카피한다. 또한 `coalesceValues()` 내부에서도 차트 기본값(`ch.Values()`)을 다시 딥 카피한다.

**문제 상세**:
- `copystructure.Copy()`는 리플렉션 기반 딥 카피로, 대규모 Values(수천 개 키)에서 상당한 GC 압력 유발
- Install 과정에서 Values는 최소 3번 딥 카피됨: (1) `MergeValues()` 진입 시, (2) `coalesceValues()` 내부의 차트 기본값 복사, (3) `coalesceDeps()` 재귀 시 서브차트별 복사
- `coalesceTablesFullKey()`에서 `srcOriginalNonNil` 맵을 매번 새로 할당하여 null 값 추적 -- 대부분의 경우 null 값이 없음에도 항상 맵 생성
- `childChartMergeTrue()` 함수가 매 키마다 서브차트 목록을 선형 탐색하여 해당 키가 서브차트인지 확인 -- O(keys * dependencies) 복잡도

**영향 범위**: `helm install`, `helm upgrade` (특히 서브차트가 많은 umbrella chart)

---

## 3. 개선 제안

### 3.1 스토리지 드라이버 인코딩 최적화 및 지연 디코딩

**문제 상세**:
`pkg/storage/driver/util.go`에서 `encodeRelease()`는 `gzip.BestCompression`(레벨 9)을 사용하고, `List()` 계열 메서드는 필터 적용 전에 모든 릴리스를 `decodeRelease()`로 완전 디코딩한다. `Storage.Deployed()`는 `DeployedAll()` -> `Query(labels)` -> 전체 디코딩 -> 필터링 -> 정렬이라는 긴 체인을 거친다.

**근본 원인**:
Release 조회 시 대부분의 경우 메타데이터(이름, 버전, 상태)만 필요하지만, 이 정보가 인코딩된 body 안에 묻혀 있어 전체 디코딩이 불가피하다. Kubernetes Secret의 레이블에 시스템 메타데이터가 별도로 저장되어 있지만, 현재 `List()` 구현은 이를 활용하지 않고 body를 디코딩하여 filter 콜백을 적용한다.

**제안 솔루션**:

1. **gzip 압축 레벨을 `BestSpeed`(레벨 1) 또는 `DefaultCompression`(레벨 6)으로 변경**
   - `gzip.BestCompression` -> `gzip.DefaultCompression`으로 변경하면 압축 시간 50-70% 감소, 압축률은 5-10% 감소에 그침
   - etcd 크기 제한(1.5MB)에 대한 여유가 충분한 경우가 대부분

2. **레이블 기반 사전 필터링 도입**
   - `Secrets.List()`에서 filter 콜백의 조건을 LabelSelector로 변환하여 Kubernetes API 레벨에서 필터링
   - 예: `StatusFilter(StatusDeployed)` -> `LabelSelector: "owner=helm,status=deployed"`
   - 디코딩 대상 Secret 수를 대폭 감소

3. **지연 디코딩(Lazy Decoding) 패턴 도입**
   - `List()` 반환값을 `LazyRelease` 래퍼로 감싸서, 레이블에서 읽을 수 있는 메타데이터는 즉시 제공하고, body 디코딩은 `Chart()`, `Manifest()`, `Config()` 접근 시에만 수행
   - `helm list` 등 메타데이터만 필요한 명령에서 디코딩 완전 회피

**예상 효과**:
- `helm list` 응답 시간 60-80% 감소 (대규모 클러스터)
- `encodeRelease()` CPU 사용량 50-70% 감소
- etcd 네트워크 트래픽 감소 (레이블 기반 필터링)

**구현 난이도**: 중 (레이블 필터링은 저, LazyRelease 래퍼는 중-상)

---

### 3.2 리포지토리 인덱스의 스트리밍 파싱 및 버전 인덱싱

**문제 상세**:
`pkg/repo/v1/index.go`의 `loadIndex()`가 수십 MB 인덱스 파일을 한 번에 메모리에 적재하고, 모든 `ChartVersions`를 SemVer 정렬한다. `IndexFile.Get()`은 정렬된 리스트를 선형 탐색하므로 대형 인덱스에서 O(versions) 비용이 발생한다.

**근본 원인**:
인덱스 파일 형식이 전체 로딩을 전제로 설계되었고, 버전 매칭을 위한 별도 인덱스 구조(해시맵, B-tree 등)가 없다. 또한 YAML 파싱이 기본인데, `jsonOrYamlUnmarshal()`에서 `json.Valid()` 검사 후 YAML로 폴백하는 구조라 대부분 YAML 경로를 타게 된다.

**제안 솔루션**:

1. **JSON 형식 인덱스 캐시 자동 생성**
   - `DownloadIndexFile()` 후 YAML 인덱스를 JSON으로 변환하여 별도 캐시 파일 저장 (`{repo}-index.json`)
   - 다음 로드 시 JSON 캐시가 존재하고 수정 시각이 같으면 JSON에서 직접 로드
   - JSON 파싱은 YAML 대비 5-10배 빠름

2. **SemVer 프리컴파일 인덱스 도입**
   - 인덱스 로드 시 각 차트의 버전 목록을 `*semver.Version` 슬라이스로 사전 파싱하여 캐시
   - `Get()` 호출 시 매번 `semver.NewVersion()` 파싱을 반복하는 것을 방지
   - 정렬 상태를 보장하여 이진 탐색 기반 범위 검색 가능

3. **차트 목록 레이지 로딩**
   - `helm search repo`처럼 특정 차트만 조회하는 경우, 전체 `Entries` 맵을 로드하지 않고 스트리밍 YAML 파서로 대상 차트만 추출
   - Go의 `yaml.Decoder`를 활용한 스트리밍 파싱

4. **인덱스 파일 크기 제한 및 경고**
   - 50MB 이상 인덱스 파일 로드 시 경고 메시지 출력
   - `--max-index-size` 옵션으로 제한 설정 가능

**예상 효과**:
- 인덱스 로드 시간 5-10배 감소 (JSON 캐시 활용 시)
- 메모리 사용량 30-50% 감소 (레이지 로딩)
- `helm search repo` 응답 시간 3-5배 개선

**구현 난이도**: 중-상 (JSON 캐시는 저, 스트리밍 파싱은 상)

---

### 3.3 템플릿 함수 맵 싱글턴화 및 서브차트 병렬 렌더링

**문제 상세**:
`pkg/engine/engine.go`의 `render()`에서 매번 `funcMap()` -> `sprig.TxtFuncMap()` 호출로 100+ 함수를 복사하고, `initFunMap()`에서 클로저를 바인딩한다. `allTemplates()`의 `recAllTpls()` 재귀에서 서브차트마다 `copystructure.Copy()`로 Values를 딥 카피한다.

**근본 원인**:
sprig 함수 맵은 호출마다 새 맵을 반환하는 설계이며, Helm이 이를 캐싱하지 않는다. `include`/`tpl` 함수가 `template.Template` 인스턴스에 대한 클로저이므로, 템플릿 인스턴스마다 새 함수 맵이 필요하다는 제약이 있다. 그러나 sprig 기본 함수 + Helm의 정적 함수(toYaml, fromYaml 등)는 상태가 없으므로 공유 가능하다.

**제안 솔루션**:

1. **정적 함수 맵 캐싱**
   - sprig 함수 + Helm 정적 함수(toYaml, fromYaml, toJson 등)를 패키지 레벨 `sync.Once`로 한 번만 생성
   - `initFunMap()`에서는 캐싱된 맵을 복사한 후, `include`/`tpl`/`lookup`/`required`/`fail` 등 상태 의존 함수만 오버라이드
   - 함수 맵 복사 비용이 100+ 함수 생성 비용 대비 최소 5배 이상 감소

2. **Values 딥 카피 최적화**
   - `recAllTpls()`에서 서브차트 Values 스코핑 시, 전체 딥 카피 대신 Copy-on-Write 래퍼 사용
   - 대부분의 서브차트는 부모 Values의 일부만 참조하므로, 실제 수정이 발생하는 키만 복사

3. **독립 서브차트 병렬 렌더링**
   - `recAllTpls()`에서 서로 의존하지 않는 서브차트(Subcharts 필드 참조가 없는 경우)를 `goroutine`으로 병렬 처리
   - `renderable` 맵은 `sync.Map` 또는 사전 할당된 슬라이스로 동시 쓰기 지원
   - 서브차트 간 `define` 블록 공유가 필요한 경우는 직렬 폴백

**예상 효과**:
- `funcMap()` 초기화 시간 80% 감소
- 대형 umbrella chart 렌더링 시간 30-50% 감소
- GC 압력 감소 (불필요한 맵/Values 복사 제거)

**구현 난이도**: 중 (함수 맵 캐싱은 저, 병렬 렌더링은 상)

---

### 3.4 의존성 차트 병렬 다운로드 및 캐시 우선 조회

**문제 상세**:
`pkg/downloader/manager.go`의 `downloadAll()`에서 의존성 차트를 `for` 루프로 순차 다운로드한다. `DiskCache.Get()` 호출이 다운로드 로직과 분리되어 있어, 캐시 히트 여부와 관계없이 네트워크 요청이 선행된다.

**근본 원인**:
`downloadAll()`이 단일 루프 내에서 `findChartURL()` -> `DownloadTo()` -> 파일 이동을 순차 수행하는 모놀리식 구조이다. 캐시 키(SHA256 digest)가 인덱스 파일의 `ChartVersion.Digest`에 있지만, `downloadAll()`에서 이를 먼저 확인하는 로직이 없다.

**제안 솔루션**:

1. **캐시 우선 조회(Cache-First) 패턴 도입**
   - `downloadAll()` 진입 시, 각 의존성의 digest를 인덱스에서 추출
   - `DiskCache.Get(digest)` 성공 시 다운로드를 건너뛰고 캐시에서 직접 복사
   - 캐시 미스인 의존성만 다운로드 대상으로 표시

2. **다운로드 대상 차트 병렬화**
   - 캐시 미스 의존성들을 `errgroup.Group`으로 병렬 다운로드
   - `HELM_MAX_PARALLEL_DOWNLOADS` 환경 변수로 동시 다운로드 수 제한 (기본값 4)
   - 다운로드 완료 후 일괄적으로 `safeMoveDeps()`로 원자적 이동

3. **OCI 태그 목록 조회 배치 처리**
   - 동일 레지스트리의 여러 차트에 대해 `registryClient.Tags()` 호출을 배치로 그룹화
   - HTTP/2 연결 재사용으로 TCP 핸드셰이크 비용 절감

4. **임시 인덱스 파일 자동 정리**
   - `ensureMissingRepos()`에서 생성된 `helm-manager-{SHA256}` 임시 인덱스를 명령 종료 시 `defer`로 정리
   - 주기적 캐시 정리를 위한 `helm cache clean` 명령 또는 TTL 기반 자동 삭제

**예상 효과**:
- 의존성 다운로드 시간 70-90% 감소 (캐시 히트 시 100% 감소)
- 병렬 다운로드로 네트워크 레이턴시 1/n 감소 (n = 병렬 수)
- 캐시 디렉토리 디스크 사용량 관리 개선

**구현 난이도**: 중 (캐시 우선 조회는 저, 병렬 다운로드는 중, OCI 배치는 상)

---

### 3.5 Values 병합 파이프라인 최적화

**문제 상세**:
`pkg/chart/common/util/coalesce.go`에서 `CoalesceValues()`는 진입 시 전체 Values를 `copystructure.Copy()`로 딥 카피하고, 내부 `coalesceValues()`에서 차트 기본값을 다시 딥 카피한다. `coalesceTablesFullKey()`에서 매 호출마다 `srcOriginalNonNil` 맵을 할당하고, `childChartMergeTrue()`가 매 키마다 O(dependencies) 탐색을 수행한다.

**근본 원인**:
Go의 `map[string]any` 참조 특성으로 인해 방어적 딥 카피가 필수이지만, 현재 구현은 필요 이상으로 광범위하게 복사한다. `childChartMergeTrue()`는 서브차트 이름을 해시셋 대신 슬라이스로 관리하여 매번 선형 탐색한다.

**제안 솔루션**:

1. **서브차트 이름 해시셋 사전 구축**
   - `coalesce()` 진입 시 서브차트 이름을 `map[string]struct{}`로 한 번만 구축
   - `childChartMergeTrue()`의 O(keys * deps) -> O(keys) 감소

2. **선택적 딥 카피 전략**
   - 차트 기본값(`ch.Values()`)이 immutable임을 보장하는 래퍼 타입 도입
   - 병합 과정에서 수정이 필요한 서브트리만 Copy-on-Write로 복사
   - 최상위 키 레벨에서 수정 여부를 먼저 판단하여 불필요한 깊은 복사 방지

3. **null 값 추적 최적화**
   - `coalesceTablesFullKey()`에서 `srcOriginalNonNil` 맵 생성 전에 src에 nil 값이 있는지 사전 검사
   - nil 값이 없으면 맵 생성을 건너뛰고 단순 병합 경로로 분기
   - 실제 사용에서 null 값이 포함된 경우는 5% 미만으로 추정

4. **Values 병합 결과 캐싱**
   - `helm upgrade --reuse-values` 시 이전 릴리스의 병합 결과를 캐시로 활용
   - 변경된 키만 증분 병합하여 전체 재계산 회피

**예상 효과**:
- Values 병합 시간 40-60% 감소 (대형 차트)
- GC 할당량 50% 이상 감소
- `childChartMergeTrue()` 호출 비용 O(1)로 감소

**구현 난이도**: 중 (해시셋은 저, Copy-on-Write는 상, 증분 병합은 상)

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 기대 효과 | 구현 난이도 | 예상 기간 |
|---------|----------|----------|-----------|----------|
| **P0** | 3.1 - gzip 압축 레벨 변경 | CPU 50-70% 감소 | 저 | 1주 |
| **P0** | 3.1 - 레이블 기반 사전 필터링 | `helm list` 60-80% 개선 | 저 | 2주 |
| **P1** | 3.4 - 캐시 우선 조회 | 의존성 다운로드 70-90% 감소 | 저 | 2주 |
| **P1** | 3.3 - 정적 함수 맵 싱글턴화 | 함수 맵 초기화 80% 감소 | 저 | 1주 |
| **P1** | 3.5 - 서브차트 해시셋 + null 최적화 | 병합 40-60% 개선 | 저-중 | 2주 |
| **P2** | 3.2 - JSON 인덱스 캐시 | 인덱스 로드 5-10배 개선 | 중 | 3주 |
| **P2** | 3.4 - 병렬 다운로드 | 다운로드 시간 1/n | 중 | 3주 |
| **P2** | 3.1 - LazyRelease 래퍼 | 메타데이터 조회 디코딩 회피 | 중 | 4주 |
| **P3** | 3.3 - 서브차트 병렬 렌더링 | 렌더링 30-50% 개선 | 상 | 6주 |
| **P3** | 3.2 - 스트리밍 인덱스 파싱 | 메모리 30-50% 감소 | 상 | 6주 |
| **P3** | 3.5 - Copy-on-Write Values | GC 압력 대폭 감소 | 상 | 8주 |

### 로드맵

**Phase 1 (1-2개월): Quick Wins**
- gzip 압축 레벨 변경 (코드 한 줄 변경, 벤치마크 추가)
- 레이블 기반 사전 필터링 (Secrets/ConfigMaps 드라이버의 `List()` 리팩토링)
- 함수 맵 싱글턴화 (`sync.Once` 패턴 적용)
- 서브차트 해시셋 도입 (3줄 변경으로 O(n) -> O(1))
- 캐시 우선 조회 패턴 (`downloadAll()` 진입부에 캐시 체크 추가)

**Phase 2 (3-4개월): 구조적 개선**
- JSON 인덱스 캐시 시스템 구축
- 의존성 병렬 다운로드 (`errgroup` 적용)
- LazyRelease 래퍼 도입 (Accessor 패턴 확장)
- null 값 사전 검사 최적화

**Phase 3 (5-8개월): 아키텍처 진화**
- 서브차트 병렬 렌더링 프레임워크
- 스트리밍 인덱스 파서
- Copy-on-Write Values 시스템
- 종합 벤치마크 스위트 구축 및 성능 회귀 테스트 CI 통합

---

## 5. 기대 성과

### 정량적 성과

| 시나리오 | 현재 성능 (추정) | 개선 후 성능 (추정) | 개선율 |
|---------|---------------|-----------------|-------|
| `helm list` (500개 릴리스) | ~15초 | ~3초 | 80% |
| `helm install` (20개 서브차트 umbrella) | ~45초 | ~20초 | 55% |
| `helm dependency update` (10개 의존성) | ~30초 | ~5초 | 83% |
| `helm search repo` (대형 인덱스) | ~8초 | ~1초 | 87% |
| `helm template` (대형 차트) | ~5초 | ~2초 | 60% |
| Release 저장 (encodeRelease) | ~200ms | ~60ms | 70% |

### 정성적 성과

1. **대규모 클러스터 운영 효율화**: 수백~수천 개 릴리스를 관리하는 엔터프라이즈 환경에서 Helm CLI 응답성 대폭 개선
2. **CI/CD 파이프라인 최적화**: 빌드/배포 파이프라인에서 Helm 단계의 실행 시간 단축으로 전체 파이프라인 효율 향상
3. **리소스 사용량 절감**: 메모리/CPU 사용량 감소로 CI 러너 및 운영자 워크스테이션의 리소스 압박 완화
4. **개발자 경험(DX) 향상**: `helm template` 등 개발 시 빈번히 사용하는 명령의 응답 속도 개선으로 차트 개발 생산성 향상
5. **CNCF 커뮤니티 기여**: 성능 개선 PR을 통해 Helm 프로젝트에 직접 기여하고, 현대자동차 오토에버의 오픈소스 참여도 제고
