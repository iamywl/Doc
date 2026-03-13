# Grafana OSS 구조적 개선 계획서

> 작성일: 2026-03-13
> 대상 버전: Grafana OSS (main branch 기준)
> 분석 범위: 백엔드 아키텍처, 쿼리 실행 엔진, 대시보드 시스템, 알림 시스템, 캐싱, 스토리지

---

## 1. 프로젝트 개요

Grafana는 Go 백엔드 + TypeScript/React 프론트엔드로 구성된 모노레포 기반 오픈소스 모니터링/옵저버빌리티 플랫폼이다. Google Wire 기반 컴파일 타임 DI, XORM ORM, gRPC 플러그인 시스템, DAG 기반 SSE(Server-Side Expressions) 엔진 등으로 구성되어 있으며, 현재 레거시 SQL 스토리지에서 Kubernetes 스타일 통합 스토리지(Unified Storage)로의 대규모 아키텍처 전환을 진행 중이다.

본 문서는 Grafana의 핵심 서브시스템에서 식별된 구조적 한계점과 성능 병목을 분석하고, 구체적인 개선 방안을 제시한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 대시보드 버전 관리의 전체 JSON 중복 저장 문제

`dashboard_version` 테이블(`pkg/services/dashboardversion/model.go`)은 매 저장 시마다 대시보드의 **전체 JSON**(`Data *simplejson.Json`)을 `dashboard_version.data` 컬럼에 복사 저장한다. 하나의 대시보드가 50개 패널을 포함하고 JSON 크기가 500KB인 경우, 100회 수정 시 약 50MB의 중복 데이터가 생성된다.

- **코드 경로**: `pkg/services/dashboards/service/dashboard_service.go` → `Store.SaveDashboard()` → `INSERT dashboard_version`
- **정리 로직**: `pkg/services/dashboardversion/dashverimpl/xorm_store.go`의 `GetBatch()` 함수가 `versionsToKeep` 기준으로 오래된 버전을 삭제하지만, `DELETE` 쿼리가 대량 실행 시 DB 락 경합을 유발
- **구조적 문제**: `Dashboard` 구조체의 이중 데이터 구조(메타데이터 필드 + `Data` JSON 내부에 동일 필드 중복 존재, `SetID()`, `SetUID()`, `SetVersion()` setter로 동기화)가 직렬화 비용 증가

### 2.2 OSS 쿼리 캐싱의 No-op 구현과 데이터소스 커넥션 풀 비효율

`pkg/services/caching/service.go`의 `OSSCachingService`는 완전한 no-op 구현이다. `HandleQueryRequest()`와 `HandleResourceRequest()` 모두 `(false, empty, "")` 를 반환하며, 이로 인해 동일 대시보드를 10명이 동시에 열면 10회의 동일한 데이터소스 쿼리가 발생한다.

또한 `instancemgmt.InstanceManager` 패턴으로 데이터소스 인스턴스별 독립 `http.Client`를 생성하는데, Go 기본값인 `MaxIdleConnsPerHost = 2`가 적용되어 동시 쿼리가 많은 환경에서 TCP 연결이 과도하게 생성/소멸된다.

- **코드 경로**: `pkg/tsdb/loki/loki.go`의 `datasourceInfo` 구조체 → 인스턴스별 `HTTPClient`
- **영향**: Prometheus/Loki 데이터소스에 대한 커넥션 재사용률 저하, TLS 핸드셰이크 반복

### 2.3 알림 스케줄러의 동기식 상태 영속화 병목

알림 시스템(`pkg/services/ngalert/schedule/schedule.go`)의 `processTick()`은 매 틱(기본 10초)마다 모든 활성 규칙을 순회하고, 상태 관리자(`pkg/services/ngalert/state/manager.go`)의 `ProcessEvalResults()`에서 `persister.Sync()`를 호출한다.

기본 구성인 `SyncStatePersister`는 **매 평가마다 즉시 DB 저장**을 수행하여, 1,000개 규칙 환경에서 10초마다 최소 1,000회의 DB INSERT/UPDATE가 발생한다. `AsyncRuleStatePersister`(압축 + 주기적 배치)가 존재하지만 피처 플래그(`FlagAlertingSaveStateCompressed`, `FlagAlertingSaveStatePeriodic`)로 제어되어 기본 비활성 상태다.

- **코드 경로**: `pkg/services/ngalert/state/manager.go` → `persister.Sync()` → DB
- **추가 병목**: `stateManager.cache`는 인메모리 맵(`map[fingerprint]*State`)으로, 대량 레이블 조합 시 GC 압력 증가. `data.Fingerprint` 계산이 매 평가마다 반복

### 2.4 검색 시스템의 RDBMS 의존 전문 검색 한계

`pkg/services/search/service.go`의 `SearchHandler()`는 `dashboardService.SearchDashboards()`에 위임하며, 이는 최종적으로 XORM 기반 SQL `LIKE` 쿼리로 수행된다. 대시보드 수가 10,000개 이상인 환경에서 `LIKE '%keyword%'` 패턴은 인덱스를 활용하지 못하고 풀 테이블 스캔이 발생한다.

- **코드 경로**: `pkg/services/search/service.go` → `dashboardService.SearchDashboards()` → `Store.FindDashboards()` → SQL LIKE
- **정렬 로직**: `pkg/services/search/model/model.go`의 `HitList.Less()`가 인메모리 정렬을 수행하여, 대량 결과 시 메모리 사용량 급증
- **한계**: 태그 기반 검색은 별도 `dashboard_tag` 테이블 JOIN 필요, 전문 검색(Full-Text Search) 미지원

### 2.5 프론트엔드 Provider 중첩과 대시보드 초기화 병목

`public/app/AppWrapper.tsx`에서 **12단계의 React Provider 중첩** 구조가 존재한다 (Redux → ErrorBoundary → OpenFeature → GrafanaContext → ThemeProvider → CacheProvider → KBarProvider → TimeRangeProvider → ScopesContext → ExtensionRegistries → ExtensionSidebar → PortalProvider). 각 Provider의 컨텍스트 값 변경 시 하위 전체 트리가 리렌더링될 수 있다.

또한 `DashboardPage.tsx`의 초기화 흐름에서 `initDashboard()` Redux 액션이 API 호출, `DashboardModel` 생성, 변수 초기화를 순차적으로 수행하여, 변수가 많은 대시보드에서 초기 로딩이 지연된다.

- **코드 경로**: `public/app/features/dashboard/containers/DashboardPage.tsx` → `initDashboard` → API → `DashboardModel.ts` 생성 → 변수 초기화
- **40+ 리듀서**: `public/app/core/reducers/root.ts`에서 결합되는 40개 이상의 리듀서가 스토어 크기와 디스패치 오버헤드 증가

---

## 3. 개선 제안

### 3.1 대시보드 버전 관리: 증분(Delta) 저장 방식 도입

#### 문제 상세
`dashboard_version.data` 컬럼에 매번 전체 JSON을 저장하여 스토리지 사용량이 선형적으로 증가하고, 버전 정리 시 대량 DELETE로 인한 DB 락 경합이 발생한다.

#### 근본 원인
현재 설계는 각 버전을 독립적으로 복원 가능하게 하기 위해 전체 스냅샷을 저장하지만, 대시보드 수정의 대부분은 패널 하나의 쿼리 변경이나 레이아웃 조정 같은 소규모 변경이다.

#### 제안 솔루션
- N번째 버전마다 전체 스냅샷을 저장하고, 그 사이 버전은 JSON Patch(RFC 6902) 형식의 증분만 저장
- 예: 매 10번째 버전은 전체 스냅샷, 중간 버전은 이전 버전 대비 diff만 저장
- `DashboardVersion` 모델에 `IsSnapshot bool` 필드와 `Patch []byte` 필드 추가
- 복원 시: 가장 가까운 스냅샷에서 패치를 순차 적용

```
v1 (스냅샷, 500KB)
v2 (패치, 2KB)  ← JSON Patch: [{"op":"replace","path":"/panels/0/targets/0/expr","value":"new_query"}]
v3 (패치, 1KB)
...
v10 (스냅샷, 510KB)
v11 (패치, 3KB)
```

#### 예상 효과
- 스토리지 사용량 70-90% 감소 (일반적인 수정 패턴 기준)
- 버전 정리 빈도 감소로 DB 락 경합 완화
- 버전 간 실제 변경 내용을 명시적으로 추적 가능 (git diff와 유사한 사용자 경험)

#### 구현 난이도
**중-상** — `SaveDashboard` 흐름 전체에 diff 계산 로직 추가 필요. 복원 시 패치 적용 순서 보장 및 스냅샷 손실 시 복구 전략 설계 필요. CUE 스키마 버전 마이그레이션(`schemaVersion`)과의 호환성 검증 필요.

---

### 3.2 OSS 레벨 쿼리 결과 캐싱 도입

#### 문제 상세
`OSSCachingService`가 완전한 no-op이므로, 동일 시간 범위의 동일 쿼리가 반복 실행된다. 특히 `now-1h to now` 같은 상대 시간 쿼리는 데이터가 빈번히 변하지 않음에도 매번 데이터소스에 쿼리를 전달한다.

#### 근본 원인
Enterprise 상업적 차별화를 위해 쿼리 캐싱이 OSS에서 비활성화되어 있으며, `CachingService` 인터페이스 자체는 잘 설계되어 있으나 OSS 구현체가 없다.

#### 제안 솔루션
1. **인메모리 LRU 캐시 기반 OSS 구현체** 추가:
   - `pkg/infra/localcache/cache.go`의 기존 `CacheService`를 활용
   - 캐시 키: `SHA256(datasource_uid + query_json + time_range_aligned)`
   - 시간 범위 정렬(alignment): 쿼리의 `from`/`to`를 `step` 단위로 내림하여 캐시 히트율 향상
   - TTL: 데이터소스 타입별 기본값 설정 (Prometheus: 15초, Loki: 30초, SQL: 60초)

2. **Request Coalescing(요청 통합)** 구현:
   - 동일 캐시 키의 동시 요청을 `singleflight.Group`으로 통합
   - 10명이 동일 대시보드를 열어도 데이터소스에는 1회만 쿼리

3. **커넥션 풀 최적화**:
   - `instancemgmt.InstanceManager`에서 생성하는 `http.Client`의 `Transport` 설정 조정
   - `MaxIdleConnsPerHost`를 데이터소스 설정(`JsonData`)에서 구성 가능하게 확장
   - 기본값을 `2 → 10`으로 상향

#### 예상 효과
- 데이터소스 쿼리 횟수 60-80% 감소 (대시보드 동시 조회 기준)
- CloudWatch, BigQuery 등 쿼리 과금형 데이터소스 비용 절감
- 데이터소스 부하 분산으로 전체 응답 시간 개선
- 커넥션 풀 최적화로 TLS 핸드셰이크 횟수 감소

#### 구현 난이도
**중** — `CachingService` 인터페이스가 이미 잘 정의되어 있으므로 새 구현체만 추가. `singleflight`는 Go 표준 라이브러리. 시간 범위 정렬 로직이 핵심 난이도.

---

### 3.3 알림 상태 영속화 비동기 배치 기본 활성화 및 인메모리 캐시 최적화

#### 문제 상세
기본 `SyncStatePersister`가 매 평가마다 DB 동기 쓰기를 수행하여, 규칙 수 증가 시 DB 부하가 선형적으로 증가한다. 또한 `state.cache`의 `data.Fingerprint` 계산이 레이블 맵을 매번 정렬/해시하여 CPU 오버헤드가 발생한다.

#### 근본 원인
- `SyncStatePersister`가 기본값인 것은 데이터 안정성 우선 설계이나, 대부분의 평가 결과는 이전과 동일한 상태(`Alerting` 유지, `Normal` 유지)여서 불필요한 DB 쓰기가 대다수
- `data.Fingerprint` 계산 시 `data.Labels`의 `Fingerprint()` 메서드가 매번 정렬을 수행

#### 제안 솔루션
1. **상태 변경 시에만 DB 쓰기 (Dirty Flag 패턴)**:
   - `State` 구조체에 `isDirty bool` 필드 추가
   - `transition()` 함수에서 실제 상태 변경이 있을 때만 `isDirty = true`
   - `persister.Sync()`에서 dirty 상태만 배치로 저장
   - 변경이 없는 평가 사이클에서는 DB 쓰기 완전 생략

2. **Fingerprint 캐싱**:
   - `State` 구조체 생성 시 `CacheID`(Fingerprint)를 한 번만 계산하고 캐싱
   - 레이블이 변경되지 않는 한 재계산 방지
   - 현재도 `CacheID data.Fingerprint` 필드가 존재하므로, 계산 시점을 생성 시로 제한

3. **`AsyncRuleStatePersister`를 기본으로 전환**:
   - 피처 플래그 의존성 제거, 기본 영속화 전략으로 비동기 배치 사용
   - 강제 동기 모드는 옵션으로 유지 (HA 환경 등)

#### 예상 효과
- DB 쓰기 횟수 80-95% 감소 (대부분의 평가 결과가 상태 변경 없음)
- 1,000개 규칙 환경에서 10초당 DB 쓰기가 1,000회 → 50-100회로 감소
- Fingerprint 캐싱으로 평가 루프 CPU 사용량 10-15% 감소

#### 구현 난이도
**중-하** — Dirty flag 패턴은 단순하며, `AsyncRuleStatePersister` 구현이 이미 존재. 기본값 전환 시 기존 배포 환경과의 호환성 검증 필요.

---

### 3.4 검색 시스템 전문 검색 엔진 통합

#### 문제 상세
SQL `LIKE '%keyword%'` 기반 검색은 대규모 환경에서 풀 테이블 스캔이 발생하고, 유사도 기반 검색(fuzzy search), 형태소 분석, 관련도 정렬 등이 불가능하다.

#### 근본 원인
검색 시스템이 `dashboardService.SearchDashboards()`에 완전히 위임되어 있어, 검색 로직이 RDBMS의 SQL 기능에 제한된다. 별도의 검색 인덱스가 없다.

#### 제안 솔루션
1. **Bleve 내장 검색 엔진 도입**:
   - Go 네이티브 전문 검색 라이브러리 [Bleve](https://github.com/blevesearch/bleve) 활용
   - 외부 의존성(Elasticsearch 등) 없이 OSS 환경에서 즉시 사용 가능
   - 대시보드 저장/삭제 시 인덱스 자동 업데이트 (이벤트 기반)

2. **인덱스 구조 설계**:
   ```
   Document {
     uid:         string (keyword, 정확 매칭)
     title:       string (analyzed, 한국어/영어 토크나이저)
     description: string (analyzed)
     tags:        []string (keyword array)
     folder_uid:  string (keyword)
     folder_path: string (path hierarchy)
     panel_titles: []string (analyzed, 패널 제목)
     datasource_types: []string (keyword)
     updated_at:  time (정렬용)
   }
   ```

3. **검색 API 확장**:
   - `SearchHandler()`에 검색 엔진 분기 추가
   - 피처 플래그로 SQL 폴백과 Bleve 검색 전환
   - 초기 인덱스 빌드는 백그라운드 서비스로 실행

#### 예상 효과
- 검색 응답 시간: 10,000개 대시보드 기준 500ms → 10ms 이하
- Fuzzy 검색, 패널 내용 검색, 데이터소스 타입별 필터링 가능
- RDBMS 검색 쿼리 부하 완전 제거

#### 구현 난이도
**중-상** — Bleve 통합 자체는 Go 라이브러리라 간단하지만, 인덱스 일관성 유지(대시보드 수정/삭제 시 동기화), 클러스터 환경에서의 인덱스 복제 전략 설계 필요.

---

### 3.5 SSE 쿼리 파이프라인의 데이터소스 노드 병렬 실행 기본 활성화

#### 문제 상세
`pkg/expr/graph.go`의 `DataPipeline.execute()` 함수에서 `FlagSseGroupByDatasource` 피처 플래그가 비활성화되면, 동일 데이터소스에 대한 여러 쿼리가 **개별 네트워크 요청**으로 전송된다. 하나의 대시보드 패널에 동일 Prometheus 데이터소스의 쿼리 A, B, C가 있으면 3회의 별도 gRPC 호출이 발생한다.

#### 근본 원인
`FlagSseGroupByDatasource`가 피처 플래그로 제어되어 기본 비활성 상태. `executeDSNodesGrouped()`(`pkg/expr/nodes.go`) 함수가 데이터소스 인스턴스별로 그룹화하여 단일 `QueryDataRequest`로 묶는 최적화가 구현되어 있으나 기본 경로가 아님.

#### 제안 솔루션
1. **`FlagSseGroupByDatasource` 기본 활성화**:
   - 안정성 검증 후 피처 플래그를 기본 활성으로 전환
   - 개별 실행 모드는 비활성화 옵션으로 유지

2. **독립 데이터소스 간 병렬 실행 추가**:
   - 현재 위상 정렬 결과에서 데이터소스 노드를 먼저 배치하지만, **서로 다른 데이터소스의 노드는 goroutine으로 병렬 실행** 가능
   - 예: Prometheus 쿼리 그룹과 Loki 쿼리 그룹을 동시 실행
   - `errgroup.Group`으로 병렬 실행 후 결과를 `mathexp.Vars`에 병합

3. **DAG 기반 최적 병렬화**:
   - 위상 정렬 결과에서 동일 레벨(의존성이 없는 노드 집합)을 식별
   - 같은 레벨의 노드를 모두 병렬 실행 후 다음 레벨로 진행
   - 이론적으로 파이프라인 실행 시간이 최장 경로(critical path) 수준으로 단축

#### 예상 효과
- 데이터소스 간 쿼리 대기 시간 제거 (직렬 → 병렬)
- 3개 데이터소스 쿼리 시: 응답 시간 `sum(각 쿼리 시간)` → `max(각 쿼리 시간)`
- 동일 데이터소스 내 네트워크 왕복 횟수 3회 → 1회 (그룹 실행)

#### 구현 난이도
**중-하** — 그룹 실행 코드가 이미 존재하므로 기본 활성화는 설정 변경 수준. 데이터소스 간 병렬 실행은 `errgroup` 활용으로 구현 가능하나, `mathexp.Vars` 맵의 동시 쓰기 보호를 위한 동기화 필요.

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 예상 소요 | 영향 범위 | 위험도 |
|---------|----------|----------|----------|--------|
| **P0** | 3.3 알림 상태 영속화 비동기 배치 기본 활성화 | 2-3주 | 알림 시스템 | 낮음 (기존 구현 활용) |
| **P0** | 3.5 SSE 데이터소스 그룹 실행 기본 활성화 | 1-2주 | 쿼리 엔진 | 낮음 (기존 구현 활용) |
| **P1** | 3.2 OSS 쿼리 결과 캐싱 + 커넥션 풀 최적화 | 4-6주 | 전체 쿼리 경로 | 중간 |
| **P1** | 3.1 대시보드 버전 증분 저장 | 4-6주 | 대시보드 시스템 | 중간 (마이그레이션 필요) |
| **P2** | 3.4 검색 시스템 전문 검색 엔진 | 6-8주 | 검색 시스템 | 중간 (새 의존성) |

### 단계별 로드맵

```
Phase 1 (1-3주): Quick Wins
├── 알림 AsyncRuleStatePersister 기본 활성화
├── SSE FlagSseGroupByDatasource 기본 활성화
├── http.Client MaxIdleConnsPerHost 기본값 상향 (2 → 10)
└── 성능 벤치마크 기준선(baseline) 수립

Phase 2 (4-9주): 핵심 개선
├── OSS 인메모리 LRU 쿼리 캐시 구현
├── singleflight 기반 Request Coalescing 구현
├── 대시보드 버전 JSON Patch 증분 저장 PoC
├── SSE 데이터소스 간 병렬 실행 구현
└── 성능 벤치마크 비교 측정

Phase 3 (10-17주): 고급 개선
├── 대시보드 버전 증분 저장 프로덕션 적용 + 마이그레이션 도구
├── Bleve 검색 엔진 통합 PoC
├── 검색 인덱스 백그라운드 빌더 구현
├── 검색 API 확장 (fuzzy search, 패널 내용 검색)
└── 종합 성능 검증 및 문서화
```

---

## 5. 기대 성과

### 정량적 성과

| 지표 | 현재 (예상) | 개선 후 (예상) | 개선율 |
|------|-----------|-------------|--------|
| 동일 대시보드 10명 동시 조회 시 데이터소스 쿼리 수 | 10회 | 1회 | 90% 감소 |
| 알림 1,000 규칙 환경 DB 쓰기 (10초당) | ~1,000회 | ~50-100회 | 90-95% 감소 |
| 대시보드 100 버전 스토리지 사용량 (500KB 기준) | ~50MB | ~5-7MB | 85-90% 감소 |
| 대시보드 검색 응답 시간 (10,000개 기준) | ~500ms | ~10ms | 98% 감소 |
| 3개 데이터소스 SSE 파이프라인 실행 시간 | sum(각 쿼리) | max(각 쿼리) | 50-66% 감소 |

### 정성적 성과

- **운영 안정성 향상**: DB 부하 감소로 대규모 환경에서의 안정성 확보
- **비용 효율성**: 쿼리 과금형 데이터소스(CloudWatch, BigQuery) 비용 절감
- **사용자 경험 개선**: 대시보드 로딩 속도 향상, 검색 정확도 향상
- **확장성 확보**: 규칙 수 10,000+ 환경에서도 안정적인 알림 시스템 운영 가능
- **OSS 경쟁력 강화**: Enterprise 전용이었던 핵심 기능(캐싱)의 OSS 레벨 제공으로 커뮤니티 기여 확대
