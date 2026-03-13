# Jenkins 코어 아키텍처 개선 제안서

> 작성일: 2026-03-13
> 대상 버전: Jenkins 2.553-SNAPSHOT (소스 기준)
> 분석 범위: jenkins-core (`core/src/main/java/`)

---

## 1. 프로젝트 개요

Jenkins는 Java 기반 오픈소스 CI/CD 자동화 서버로, WAR 형태로 패키징되어 내장 Winstone(Jetty) 서버 또는 외부 서블릿 컨테이너에서 구동된다. 핵심 아키텍처는 `Jenkins.java` 싱글턴을 루트로 하여, Stapler 웹 프레임워크(URL-Object 트리 매핑), XStream 기반 XML 파일시스템 영속성, 5단계 상태 머신 빌드 큐(`Queue.java`), Node-Computer-Executor 실행 모델, 그리고 `@Extension`/SezPoz 기반 플러그인 시스템으로 구성된다.

본 문서는 소스코드 수준의 심층 분석을 기반으로 Jenkins 코어의 구조적 한계점을 식별하고, 구체적인 개선 방안을 제안한다.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 빌드 큐(Queue) 단일 락 병목 및 O(N) 자료구조

**위치**: `core/src/main/java/hudson/model/Queue.java` (3,252줄)

Jenkins 빌드 큐는 단일 `ReentrantLock`으로 모든 큐 조작을 직렬화한다. `maintain()` 메서드(1593~1829줄)가 5단계(parked Executor 수집, blocked->buildable 전이, waitingList 처리, QueueSorter 적용, LoadBalancer 매핑)를 하나의 락 안에서 순차 실행한다.

핵심 문제점:

- **`ItemList`가 `ArrayList`를 확장**하며 `get(Task)` 메서드가 O(N) 선형 탐색을 수행한다 (Queue.java 3042~3083줄). `blockedProjects`, `buildables`, `pendings` 세 컬렉션 모두 이 구조를 사용한다.
- **`maintain()` 내부에서 `updateSnapshot()`이 반복 호출**된다. Phase 2의 blocked->buildable 전이에서 각 항목마다 스냅샷을 갱신하며(JENKINS-28926 대응), 이는 큐 항목 수에 비례하여 오버헤드가 증가한다.
- **`schedule2()` -> `scheduleInternal()` 경로**에서 중복 검사 시 `liveGetItems(Task)`가 모든 live 컬렉션을 순회하며 O(N) 스캔한다(611~676줄).
- **중첩 `try { try { ... } finally { updateSnapshot(); } } finally { lock.unlock(); }` 패턴**이 모든 공개 메서드에 적용되어, 짧은 조작에도 스냅샷 갱신 비용이 발생한다.

대규모 인스턴스(수천 개 Job, 수백 개 에이전트)에서 큐에 수백 항목이 동시 존재하면, `maintain()` 한 번 호출에 수백 ms가 소요되며 이 시간 동안 모든 `schedule2()` 호출이 블로킹된다.

### 2.2 XML 파일시스템 영속성의 I/O 병목 및 동시성 제한

**위치**: `core/src/main/java/hudson/XmlFile.java`, `hudson/util/AtomicFileWriter.java`, `hudson/util/XStream2.java`

Jenkins의 모든 설정 데이터(전역 설정, Job 정의, 빌드 메타데이터)가 XStream XML 직렬화를 통해 파일시스템에 저장된다. 이 설계의 구조적 한계:

- **`Saveable.save()`가 `synchronized` 메서드**: `Jenkins.save()`(3561줄)와 `AbstractItem.save()`(618줄) 모두 인스턴스 락을 잡고 전체 XML 직렬화 + fsync를 수행한다. NFS나 느린 스토리지에서 `FileChannel.force(true)` 호출이 수십~수백 ms 소요된다.
- **`AtomicFileWriter`의 `commit()`에서 디렉토리 fsync**: Linux 환경에서 `REQUIRES_DIR_FSYNC=true`가 기본이며, 파일 rename 후 부모 디렉토리의 `FileChannel.force()`까지 수행한다. 빌드당 최소 2회(config.xml + build.xml) fsync가 발생한다.
- **XStream2 리플렉션 기반 직렬화**: `RobustReflectionConverter`가 모든 필드를 리플렉션으로 순회하며, 대형 Job 설정(수백 개 빌드 스텝, 파라미터)에서 직렬화 시간이 선형 증가한다.
- **`beingWritten` 글로벌 Map**: `XmlFile.write()`에서 `Collections.synchronizedMap(new IdentityHashMap<>())`을 사용하여 중첩 직렬화를 추적하는데, 모든 save 경로에서 이 글로벌 맵에 경합이 발생한다.
- **쿼리 불가**: Job 목록 조회, 빌드 이력 검색 등에서 모든 XML 파일을 개별 역직렬화해야 한다. 인덱싱이 없으므로 `Jenkins.allItems()`는 전체 JENKINS_HOME 트리를 순회한다.

### 2.3 Jenkins 싱글턴의 God Object 문제

**위치**: `core/src/main/java/jenkins/model/Jenkins.java` (5,990줄)

`Jenkins.java`는 싱글턴이면서 동시에 웹 루트, Item 컨테이너, 보안 허브, 플러그인 호스트, 설정 저장소, 노드 관리자, 빌드 큐 소유자 역할을 겸한다. 이 구조적 문제:

- **`items` 맵이 `CopyOnWriteMap.Tree`**: 읽기 최적화이나 Job 추가/삭제 시 전체 TreeMap을 복사한다(489줄). 수천 개 Job 환경에서 Job 생성/삭제가 빈번하면 GC 압력이 급증한다.
- **`save()` 메서드가 전역 설정 전체를 직렬화**: 보안 설정, 노드 설정, View 설정, 클라우드 설정이 모두 하나의 `config.xml`에 저장된다. 사소한 설정 변경에도 전체 직렬화가 수행된다.
- **`computers` ConcurrentHashMap과 `nodes` Nodes 객체의 이중 관리**: Node(설정)와 Computer(런타임)의 매핑이 Jenkins 싱글턴 내부에서 관리되며, `updateComputerList()` 호출 시 전체 노드를 순회하며 Computer 생성/삭제를 판단한다.
- **`extensionLists`와 `descriptorLists` ConcurrentHashMap**: 모든 `ExtensionList.lookup()`이 `Jenkins.get()`을 경유하므로, 싱글턴 인스턴스에 대한 의존성이 시스템 전체에 퍼져 있다. 테스트 격리와 모듈화를 근본적으로 방해한다.
- **초기화 시 `Reactor` 기반 10단계 InitMilestone**: 단계 간 의존성이 Task 그래프로 표현되나, 실패 시 롤백 메커니즘이 없어 부분 초기화 상태에서 시스템이 불안정해진다.

### 2.4 플러그인 ClassLoader 격리 및 ExtensionFinder 성능

**위치**: `core/src/main/java/hudson/PluginManager.java` (2,697줄), `hudson/ExtensionFinder.java` (802줄), `hudson/ClassicPluginStrategy.java`

- **`PluginFirstClassLoader2`의 parent-last 전략**: 각 플러그인이 독립 ClassLoader를 가지나, 의존성 플러그인 간에는 `PluginManager.uberClassLoader`를 통해 클래스를 공유한다. 2,000개 이상 플러그인 생태계에서 ClassLoader 체인이 깊어지면 `Class.forName()` 호출이 연쇄적 위임을 발생시킨다.
- **`ExtensionFinder.GuiceFinder`가 Guice Injector를 사용**: 모든 `@Extension` 컴포넌트를 시작 시점에 일괄 인스턴스화한다. 플러그인 수가 증가하면 Guice binding 해석 시간이 비선형적으로 증가한다.
- **`CyclicGraphDetector`로 의존성 DAG 검증**: `PluginManager.resolveDependencies()`에서 DFS 기반 순환 검출을 수행하나, 의존성 그래프가 밀집(dense)해지면 O(V+E)의 E 부분이 급증한다.
- **SezPoz 어노테이션 인덱스 파일 스캔**: `META-INF/annotations/` 디렉토리의 인덱스 파일을 각 플러그인 JAR에서 읽어 Extension을 발견한다. 디스크 I/O와 파싱 비용이 플러그인 수에 비례한다.
- **`DescriptorExtensionList`의 지연 초기화 락**: `ensureLoaded()`에서 `synchronized(Jenkins.get())`을 사용하여 Descriptor를 로드하는데, 이는 Jenkins 싱글턴에 대한 글로벌 동기화를 유발한다.

---

## 3. 개선 제안

### 3.1 빌드 큐 스케줄링 엔진 현대화

#### 문제 상세
`Queue.maintain()` 메서드가 단일 `ReentrantLock` 하에서 5단계를 순차 실행하며, `ItemList`(ArrayList 확장)의 `get(Task)` 메서드가 O(N) 선형 탐색을 수행한다. 대규모 인스턴스에서 큐 항목이 수백 개에 달하면 `maintain()` 실행 시간이 길어지고, 이 시간 동안 모든 `schedule2()` 호출이 블로킹된다.

#### 근본 원인
- Hudson 시절 설계: 큐 항목이 수십 개 수준이던 환경에서 설계되어 단순한 ArrayList + 단일 락으로 충분했음
- `ItemList.get(Task)`의 O(N) 탐색이 `maintain()` 내부에서 반복 호출됨
- 스냅샷 갱신(`updateSnapshot()`)이 불변 객체 전체 재생성 방식

#### 제안 솔루션

**Phase 1: 자료구조 최적화**
- `ItemList<T extends Item>`에 `Map<Task, List<T>>` 인덱스를 추가하여 `get(Task)` 조회를 O(1)로 개선
- `blockedProjects`와 `buildables`에 Task 기반 HashMap 보조 인덱스 유지
- `updateSnapshot()`을 증분(incremental) 방식으로 변경: 변경된 컬렉션만 스냅샷에 반영

**Phase 2: 락 세분화**
- 단일 `ReentrantLock`을 Phase별 `ReadWriteLock`으로 분리
- `waitingList`는 독립 락으로 관리 (schedule2와 maintain의 Phase 3이 경합 감소)
- `buildables` -> `pendings` 전이는 CAS(Compare-And-Swap) 기반으로 변경 가능

**Phase 3: 비동기 스케줄링**
- `maintain()` 내 Phase 5(LoadBalancer 매핑)를 별도 스레드 풀로 분리
- `MappingWorksheet` 생성과 `LoadBalancer.map()` 호출을 큐 락 외부에서 수행
- 매핑 결과를 락 내에서 원자적으로 적용

#### 예상 효과
- `schedule2()` 응답 시간: 평균 50ms -> 5ms 이하 (O(N) -> O(1) 조회)
- `maintain()` 실행 시간: 500ms -> 50ms (100개 항목 기준)
- 동시 빌드 트리거 처리량: 3~5배 향상

#### 구현 난이도
**상** - `Queue.java`가 3,252줄이며 내부 클래스 간 결합도가 높음. `ItemList`의 인덱스 추가는 중간 난이도이나, 락 세분화는 데드락 위험이 있어 면밀한 동시성 테스트 필요.

---

### 3.2 영속성 계층 추상화 및 비동기 쓰기 도입

#### 문제 상세
모든 설정 변경이 동기적 `save()` -> `XmlFile.write()` -> `AtomicFileWriter.commit()` -> `FileChannel.force()` 경로를 거치며, 이 전체가 인스턴스 `synchronized` 블록 안에서 실행된다. NFS 환경에서 fsync 하나에 100ms 이상 소요될 수 있고, 이 시간 동안 해당 객체에 대한 모든 접근이 블로킹된다.

#### 근본 원인
- `Saveable.save()`가 즉시 일관성(immediate consistency)을 보장하도록 설계됨
- XML 파일 기반이므로 부분 업데이트가 불가능 (전체 재직렬화 필수)
- `BulkChange` 패턴이 존재하나 수동으로 try-with-resources 사용 필요

#### 제안 솔루션

**Phase 1: 비동기 쓰기 큐 도입**
```
save() -> WriteQueue(메모리 버퍼) -> 백그라운드 Writer Thread -> AtomicFileWriter
```
- `Saveable.save()` 호출 시 직렬화된 XML을 메모리 버퍼에 기록하고 즉시 반환
- 백그라운드 스레드가 버퍼를 배치로 디스크에 플러시
- `Jenkins.save()`의 `synchronized` 블록 범위를 직렬화까지로 축소하고, I/O를 분리

**Phase 2: 변경 감지 기반 선택적 저장**
- `Saveable` 구현체에 dirty flag 패턴 도입
- `@DataBoundSetter` 호출 시 자동으로 dirty 마킹
- `save()` 호출 시 dirty가 아니면 I/O 스킵
- `Jenkins.save()`에서 `config.xml`을 보안/노드/뷰/클라우드 섹션으로 분리 저장 검토

**Phase 3: 메타데이터 인덱스 도입**
- JENKINS_HOME에 SQLite 또는 H2 기반 경량 인덱스 DB 도입
- Job 이름, 마지막 빌드 번호, 빌드 상태 등 자주 조회되는 필드를 인덱싱
- `Jenkins.allItems()` 호출 시 인덱스 DB에서 필터링 후 필요한 XML만 역직렬화
- 기존 XML 파일은 source of truth로 유지 (하위 호환성 보장)

#### 예상 효과
- `save()` 응답 시간: 50~200ms -> 1ms 이하 (메모리 버퍼 기록만)
- Job 목록 조회: O(N) 파일 스캔 -> O(1) 인덱스 조회
- NFS 환경 전체 성능: 2~5배 향상 (fsync 배치화)

#### 구현 난이도
**상** - `Saveable` 인터페이스가 시스템 전체에 사용되므로 비동기화 시 일관성 모델 변경이 필요. 인덱스 DB 도입은 외부 의존성 추가로 커뮤니티 합의 필요. Phase 1(비동기 쓰기 큐)은 중간 난이도로 단독 진행 가능.

---

### 3.3 Jenkins 싱글턴 분해 및 모듈화

#### 문제 상세
`Jenkins.java`(5,990줄)가 시스템의 모든 핵심 기능을 하나의 클래스에 집중시키고 있다. `items` 맵(`CopyOnWriteMap.Tree`), `computers` 맵, `extensionLists`, `descriptorLists`, `queue`, `pluginManager` 등이 모두 이 싱글턴의 필드이며, 시스템 전체가 `Jenkins.get()`에 직접 의존한다.

#### 근본 원인
- Hudson 초기 설계: 단일 프로세스, 단일 인스턴스 가정
- Stapler 프레임워크가 URL 트리의 루트 객체로 Jenkins 싱글턴을 요구
- 점진적 기능 추가로 God Object 안티패턴 고착화

#### 제안 솔루션

**Phase 1: 서비스 인터페이스 추출**
- `Jenkins.java`에서 독립적인 관심사를 인터페이스로 추출:
  - `ItemRegistry` -- Job/프로젝트 CRUD
  - `NodeRegistry` -- 노드/에이전트 관리
  - `SecurityHub` -- SecurityRealm, AuthorizationStrategy 관리
  - `PluginRegistry` -- ExtensionList, DescriptorExtensionList 조회
- Jenkins 싱글턴이 이 인터페이스들을 구현하되, 실제 로직은 위임 객체로 이동
- `Jenkins.get().getItems()` 대신 `ItemRegistry.get().getItems()` 패턴 도입

**Phase 2: CopyOnWriteMap.Tree 대체**
- `items` 맵을 `ConcurrentSkipListMap<String, TopLevelItem>`으로 교체
- `String.CASE_INSENSITIVE_ORDER`는 그대로 유지
- CopyOnWrite 시맨틱이 불필요한 경우 ConcurrentNavigableMap으로 전환하여 쓰기 시 전체 복사 제거

**Phase 3: config.xml 분할**
- 전역 `config.xml`을 관심사별로 분리:
  - `jenkins-config.xml` (코어 설정: numExecutors, mode, version)
  - `security-config.xml` (SecurityRealm, AuthorizationStrategy)
  - `clouds-config.xml` (Cloud 인스턴스)
  - `views-config.xml` (View 정의)
- 각 설정 파일에 대해 독립적인 `save()` 경로 구현
- 하위 호환: 기존 `config.xml` 존재 시 마이그레이션 로직 수행

#### 예상 효과
- Job 추가/삭제 시 GC 압력: CopyOnWriteMap 전체 복사 제거로 50% 이상 감소
- 설정 저장 I/O: 전체 직렬화 -> 섹션별 직렬화로 70% 감소
- 코드 유지보수성: 5,990줄 단일 클래스 -> 1,000줄 이하 모듈 4~5개

#### 구현 난이도
**최상** - `Jenkins.get()` 패턴이 코어 + 2,000개 플러그인에 걸쳐 사용되므로 API 호환성 유지가 핵심 과제. Phase 1은 기존 API를 deprecated로 마킹하고 새 인터페이스를 점진 도입하는 방식으로 3~6개월 단위 진행. Phase 2는 중간 난이도로 독립 진행 가능.

---

### 3.4 플러그인 로딩 최적화 및 Lazy Extension 도입

#### 문제 상세
Jenkins 시작 시 `PluginManager`가 모든 플러그인의 `@Extension` 컴포넌트를 일괄 인스턴스화한다. `ExtensionFinder.GuiceFinder`가 Guice Injector를 구성하며, SezPoz 인덱스 파일 스캔 + 클래스 로딩 + 의존성 주입이 순차적으로 수행된다. 100개 플러그인 환경에서 시작 시간이 30초, 200개 이상에서 60초를 초과하는 경우가 빈번하다.

#### 근본 원인
- `ExtensionList`가 최초 접근 시(lazy) 로드되나, `InitMilestone.EXTENSIONS_AUGMENTED` 단계에서 대부분의 ExtensionList가 한꺼번에 resolve됨
- `ClassicPluginStrategy`가 각 플러그인 HPI/JPI를 개별 unpack + ClassLoader 생성하여 I/O 증폭
- Guice Injector 구성 시 모든 바인딩을 한 번에 해석하므로 메모리 사용량이 비선형 증가

#### 제안 솔루션

**Phase 1: 병렬 플러그인 로딩 강화**
- `PluginManager.loadPlugins()`에서 의존성 DAG를 활용한 병렬 로딩 도입
- 독립적인 플러그인(상호 의존성 없음)을 `ForkJoinPool`로 병렬 unpack 및 ClassLoader 생성
- 의존성 체인이 있는 플러그인은 위상 정렬 순서로 레벨별 병렬 처리

**Phase 2: Lazy Extension 인스턴스화**
- `ExtensionList`의 실제 인스턴스화를 최초 사용 시점까지 지연
- `@Extension(lazy = true)` 어노테이션 옵션 도입
- Guice Injector를 플러그인 그룹별로 분할하여 부분 바인딩 해석 가능

**Phase 3: Extension 인덱스 캐싱**
- SezPoz 스캔 결과를 `JENKINS_HOME/caches/extension-index.bin`에 이진 캐시로 저장
- 플러그인 버전이 변경되지 않았으면 캐시에서 직접 로드 (JAR 스캔 스킵)
- 캐시 무효화: 플러그인 HPI의 mtime 또는 SHA-256 해시 기반

#### 예상 효과
- Jenkins 시작 시간: 60초 -> 20초 (200개 플러그인 기준, Phase 1+3)
- 메모리 사용량: Guice Injector 분할로 초기 힙 10~20% 감소
- 재시작 없는 플러그인 업데이트 기반 마련 (Phase 2)

#### 구현 난이도
**중상** - Phase 1(병렬 로딩)은 기존 DAG 해석 코드(`CyclicGraphDetector`)를 활용하여 비교적 안전하게 구현 가능. Phase 2는 ExtensionList의 lazy 시맨틱이 기존 코드의 초기화 순서 가정을 깨뜨릴 수 있어 광범위한 호환성 테스트 필요. Phase 3은 중간 난이도.

---

### 3.5 DependencyGraph 재구축 비용 최적화

#### 문제 상세
`DependencyGraph.build()` 메서드(`hudson/model/DependencyGraph.java` 85~98줄)가 `Jenkins.get().allItems(AbstractProject.class)`로 **모든 프로젝트를 순회**하며 의존성 그래프를 전체 재구축한다. Tarjan SCC 위상 정렬까지 포함하면 O(V+E) 비용이 발생한다. 이 재구축은 Job 생성/삭제/설정 변경 시마다 트리거되며, 수천 개 프로젝트 환경에서 수 초가 소요될 수 있다.

#### 근본 원인
- `DependencyGraph`가 불변(immutable) 객체로 설계되어 부분 업데이트가 불가능
- `build()` 이후 `forward`/`backward` 맵이 `Collections.unmodifiable`로 래핑됨
- `topologicalDagSort()`가 매번 전체 SCC 분석을 수행

#### 제안 솔루션

**Phase 1: 증분 그래프 업데이트**
- `DependencyGraph`를 가변(mutable) 구조로 변경하되, 읽기는 스냅샷 기반
- Job 변경 시 해당 프로젝트의 엣지만 제거 후 재추가
- Tarjan SCC를 전체 재실행하지 않고, 변경된 노드 주변의 로컬 SCC만 재계산

**Phase 2: 이벤트 기반 그래프 갱신**
- `ItemListener.onCreated/onDeleted/onUpdated` 이벤트에 연동
- 변경된 프로젝트만 `buildDependencyGraph(this)` 재호출
- 배치 변경 시 `BulkChange` 패턴과 연동하여 그래프 재구축을 한 번만 수행

#### 예상 효과
- Job 설정 변경 후 그래프 갱신: 2초 -> 50ms (5,000개 프로젝트 기준)
- 연쇄 빌드 트리거 지연 감소

#### 구현 난이도
**중** - `DependencyGraph`가 465줄로 비교적 작고 독립적. 증분 Tarjan SCC는 학술적으로 연구된 알고리즘이 있으나 구현 복잡도가 있음. Phase 2(이벤트 기반)만으로도 유의미한 개선.

---

## 4. 우선순위 및 로드맵

| 우선순위 | 개선 항목 | 난이도 | 예상 기간 | 선행 조건 |
|----------|-----------|--------|-----------|-----------|
| **P0** | 3.1 Phase 1: ItemList 인덱스 추가 | 중 | 2~3주 | 없음 |
| **P0** | 3.2 Phase 1: 비동기 쓰기 큐 | 중 | 3~4주 | 없음 |
| **P1** | 3.4 Phase 1: 병렬 플러그인 로딩 | 중 | 3~4주 | 없음 |
| **P1** | 3.4 Phase 3: Extension 인덱스 캐싱 | 중 | 2~3주 | 없음 |
| **P1** | 3.5 Phase 2: 이벤트 기반 그래프 갱신 | 중 | 2~3주 | 없음 |
| **P2** | 3.1 Phase 2: 큐 락 세분화 | 상 | 4~6주 | P0 3.1 |
| **P2** | 3.3 Phase 1: 서비스 인터페이스 추출 | 상 | 6~8주 | 없음 |
| **P2** | 3.3 Phase 2: CopyOnWriteMap 대체 | 중 | 2~3주 | 없음 |
| **P2** | 3.2 Phase 2: dirty flag 기반 저장 | 중 | 3~4주 | P0 3.2 |
| **P3** | 3.1 Phase 3: 비동기 LoadBalancer 매핑 | 상 | 6~8주 | P2 3.1 |
| **P3** | 3.2 Phase 3: 메타데이터 인덱스 DB | 상 | 8~12주 | P0 3.2 |
| **P3** | 3.3 Phase 3: config.xml 분할 | 최상 | 8~12주 | P2 3.3 |
| **P3** | 3.4 Phase 2: Lazy Extension | 상 | 6~8주 | P1 3.4 |
| **P3** | 3.5 Phase 1: 증분 그래프 업데이트 | 중상 | 4~6주 | P1 3.5 |

### 추천 실행 순서

```
Quarter 1 (즉시 착수 가능, 독립적)
├── [P0] ItemList 인덱스 추가 (3.1-P1)
├── [P0] 비동기 쓰기 큐 (3.2-P1)
├── [P1] 병렬 플러그인 로딩 (3.4-P1)
├── [P1] Extension 인덱스 캐싱 (3.4-P3)
└── [P1] 이벤트 기반 DependencyGraph (3.5-P2)

Quarter 2 (Q1 결과 기반)
├── [P2] 큐 락 세분화 (3.1-P2)
├── [P2] CopyOnWriteMap 대체 (3.3-P2)
├── [P2] dirty flag 기반 저장 (3.2-P2)
└── [P2] 서비스 인터페이스 추출 시작 (3.3-P1)

Quarter 3~4 (장기)
├── [P3] 비동기 LoadBalancer 매핑 (3.1-P3)
├── [P3] 메타데이터 인덱스 DB (3.2-P3)
├── [P3] config.xml 분할 (3.3-P3)
└── [P3] Lazy Extension (3.4-P2)
```

---

## 5. 기대 성과

### 정량적 성과

| 지표 | 현재 (추정) | 개선 후 (목표) | 개선율 |
|------|-------------|---------------|--------|
| 빌드 큐 스케줄링 응답 시간 | 50~200ms | 5ms 이하 | 10~40x |
| `maintain()` 실행 시간 (100 항목) | 500ms | 50ms | 10x |
| Job 설정 저장 응답 시간 | 50~200ms | 1ms 이하 | 50~200x |
| Jenkins 시작 시간 (200 플러그인) | 60초 | 20초 | 3x |
| DependencyGraph 갱신 (5,000 프로젝트) | 2초 | 50ms | 40x |
| Job 생성/삭제 시 GC 부하 | CopyOnWrite 전체 복사 | 증분 업데이트 | 50%+ 감소 |

### 정성적 성과

- **대규모 인스턴스 안정성 향상**: 수천 개 Job, 수백 개 에이전트 환경에서 큐 지연 및 설정 저장 타임아웃 문제 해소
- **개발자 경험 개선**: 싱글턴 분해로 단위 테스트 격리성 향상, 모듈별 독립 개발/배포 가능
- **NFS/클라우드 스토리지 호환성**: 비동기 쓰기로 느린 스토리지 환경에서의 UX 개선
- **플러그인 생태계 확장성**: Lazy Extension과 병렬 로딩으로 더 많은 플러그인을 안정적으로 운용
- **기여자 진입 장벽 완화**: 5,990줄 God Object 대신 1,000줄 이하 모듈로 분리하여 신규 기여자의 코드 이해도 향상
