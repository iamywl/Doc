# Tart 프로젝트 구조적 개선 계획서

## 1. 프로젝트 개요

Tart는 Apple Silicon(arm64) 기반 macOS 호스트에서 macOS 및 Linux 가상 머신을 생성, 실행, 관리하는 CLI 도구이다. Cirrus Labs에서 개발하였으며 CI/CD 자동화 환경에 특화되어 있다.

**핵심 기술 스택:**
- 언어: Swift 5.10
- 플랫폼: macOS 13.0 (Ventura) 이상, Apple Silicon 전용
- 가상화: Apple Virtualization.Framework (VZVirtualMachine)
- 이미지 배포: OCI(Open Container Initiative) 호환 레지스트리
- CLI: apple/swift-argument-parser

**핵심 서브시스템:**
- VM 라이프사이클 관리 (`VM.swift`, `craftConfiguration()`)
- 이중 스토리지 아키텍처 (`VMStorageLocal`, `VMStorageOCI`)
- OCI 레지스트리 클라이언트 (`Registry.swift`, `DiskV2.swift`)
- 파일 잠금 동시성 제어 (`FileLock`, `PIDLock`)
- 캐시/프루닝 시스템 (`Prune.swift`, `Prunable` 프로토콜)
- 네트워크 추상화 (`Network` 프로토콜, `Softnet`)

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 OCI Pull 파이프라인의 단일 호스트 직렬화 병목

**현상:** `VMStorageOCI.pull()` 메서드(`Sources/tart/VMStorageOCI.swift` 170~178행)에서 `FileLock(lockURL: hostDirectoryURL)`을 사용하여 동일 레지스트리 호스트에 대한 모든 Pull 작업을 직렬화한다. CI/CD 환경에서 동일 레지스트리(예: `ghcr.io`)에서 서로 다른 이미지를 동시에 Pull하려는 여러 워커가 있을 때, 첫 번째 Pull이 완료될 때까지 나머지 모든 Pull이 블로킹된다.

**영향 범위:** `tart pull`, `tart clone`(OCI 소스의 경우) 커맨드. 대규모 CI/CD 클러스터에서 10개 이상의 워커가 동시에 다른 이미지를 Pull할 때, 처리량이 사실상 단일 Pull과 동일하게 감소한다.

**관련 코드 경로:**
```
VMStorageOCI.pull() → FileLock(hostDirectoryURL) → lock()
→ temporaryDeterministic() → pullFromRegistry() → move() → link() → unlock()
```

### 2.2 DiskV2 Push 시 전체 디스크 mmap에 의한 메모리 압력

**현상:** `DiskV2.push()` 메서드(`Sources/tart/OCI/Layerizer/DiskV2.swift`)에서 `Data(contentsOf: diskURL, options: [.alwaysMapped])`로 전체 디스크 이미지를 mmap한 후, `mappedDisk.chunks(ofCount: layerLimitBytes)`로 512MB 청크를 순회하면서 각 청크에 대해 `(data as NSData).compressed(using: .lz4)`를 호출한다. LZ4 압축 시 입력 512MB + 출력 버퍼가 동시에 메모리에 존재하며, `concurrency` 개수만큼 병렬 실행되므로 최대 `concurrency * (512MB + 압축 출력)` 만큼의 물리 메모리가 사용된다.

**영향 범위:** 50GB 디스크를 concurrency=4로 Push할 때, 피크 메모리 사용량이 최소 2~3GB에 달할 수 있다. Apple Silicon Mac mini(8GB RAM)에서 다른 VM이 동시에 실행 중이면 메모리 부족으로 Push가 실패하거나 시스템 전체가 느려질 수 있다.

**관련 코드 경로:**
```
DiskV2.push() → Data(contentsOf:options:.alwaysMapped)
→ withThrowingTaskGroup → chunks(ofCount: 512MB) → NSData.compressed(using: .lz4)
→ registry.pushBlob()
```

### 2.3 OCI 레지스트리 인증 토큰의 단일 스코프 캐싱 한계

**현상:** `AuthenticationKeeper` actor(`Sources/tart/OCI/AuthenticationKeeper.swift`)는 `Authentication?` 타입의 단일 프로퍼티만 가지고 있다. OCI Distribution Spec에서 토큰의 스코프는 `repository:org/repo:pull` 형태로 특정 리포지토리와 권한에 한정된다. 하나의 `Registry` 인스턴스가 다른 스코프의 요청을 처리할 때, 이전 토큰이 새 토큰으로 덮어쓰기되어 기존 스코프에 대한 토큰이 무효화된다.

현재는 Pull/Push 작업이 하나의 namespace에 대해 단일 Registry 인스턴스를 사용하므로 실질적인 문제가 발생하지 않지만, 향후 멀티 리포지토리 동시 작업이나 manifest list(multi-arch) 지원 시 토큰 재발급으로 인한 불필요한 HTTP 401 왕복이 발생할 수 있다.

**관련 코드 경로:**
```
Registry.auth() → authenticationKeeper.set(TokenResponse)
→ 이전 Authentication 덮어쓰기 → 다른 scope 요청 시 401 재발생
```

### 2.4 프루닝 시스템의 O(N) 전체 순회와 accessDate 정렬 비효율

**현상:** `Prune.pruneSpaceBudget()` 메서드(`Sources/tart/Commands/Prune.swift` 83~105행)에서 모든 PrunableStorage의 `prunables()`를 flatMap한 후 `accessDate()` 기준으로 전체 정렬을 수행한다. `VMStorageOCI.prunables()`(`Sources/tart/VMStorageOCI.swift` 143~145행)는 내부적으로 `list()`를 호출하여 `~/.tart/cache/OCIs/` 전체 디렉토리 트리를 `FileManager.enumerator`로 순회하고, 각 항목에 대해 `isSymbolicLinkKey` 리소스 값을 조회한다. 그 후 `allocatedSizeBytes()`가 각 VM 디렉토리의 config.json + disk.img + nvram.bin에 대해 `URLResourceValues.totalFileAllocatedSize`를 개별 조회한다.

100개의 캐시된 OCI 이미지가 있으면, 순회(100회) + 심볼릭 링크 필터링 + allocatedSizeBytes(300회 파일 속성 조회) + accessDate(100회) + O(N log N) 정렬이 발생한다. 자동 프루닝(`reclaimIfNeeded`)은 `tart clone`과 `tart pull` 실행 중에 호출되므로, 이 오버헤드가 직접적으로 VM 시작 시간에 더해진다.

**관련 코드 경로:**
```
Prune.reclaimIfNeeded() → reclaimIfPossible()
→ prunableStorages.flatMap { $0.prunables() }
→ .sorted { $0.accessDate() < $1.accessDate() }
→ prunable.allocatedSizeBytes() (각 항목마다 3개 파일 stat)
```

### 2.5 fcntl(2) PIDLock의 "close시 전체 잠금 해제" 시맨틱스에 의한 잠금 취약성

**현상:** `PIDLock`(`Sources/tart/PIDLock.swift`)은 `fcntl(2)` 기반 파일 잠금을 사용한다. fcntl의 고유한 시맨틱스로 인해, 같은 프로세스 내에서 같은 inode에 대해 다른 파일 디스크립터를 열고 닫으면 기존 잠금이 모두 해제된다. `Run.swift` 440~450행의 주석에서 이 문제를 명시적으로 경고하고 있으며, VM 설정 파일을 읽은 후에야 PIDLock을 획득하는 우회 패턴을 사용한다.

그러나 코드베이스가 성장하면서 `config.json`을 읽는 새로운 코드 경로가 추가될 때, 이 순서 제약을 위반하면 실행 중인 VM의 PIDLock이 조용히 해제되어 동일 VM의 중복 실행이나 실행 중 삭제가 가능해지는 심각한 버그가 발생할 수 있다.

**관련 코드 경로:**
```
Run.run() → VMConfig(fromURL: configURL) [config.json 읽기/닫기]
→ vmDir.lock() [PIDLock 획득] → 이 순서가 반드시 지켜져야 함
잘못된 순서: lock() → VMConfig() → close(fd) → 잠금 소실
```

### 2.6 LocalLayerCache의 최적 베이스 이미지 선택 알고리즘 비효율

**현상:** `VMStorageOCI.chooseLocalLayerCache()`(`Sources/tart/VMStorageOCI.swift`)에서 Pull 대상 이미지와 가장 많은 레이어를 공유하는 기존 캐시 이미지를 찾기 위해, 캐시된 모든 OCI 이미지의 매니페스트를 로드하고 Set 교집합 연산을 수행한다. 각 캐시 이미지마다 `Data(contentsOf: vmDir.manifestURL)` + `OCIManifest(fromJSON:)` 파싱이 필요하다.

캐시에 50개의 이미지가 있고 각 이미지에 100개의 디스크 레이어(50GB / 512MB)가 있으면, 50번의 파일 읽기 + JSON 파싱 + 5000개 레이어의 Set 교집합 연산이 Pull 시작 전에 수행된다.

**관련 코드 경로:**
```
VMStorageOCI.pull() → chooseLocalLayerCache()
→ list() [전체 OCI 캐시 순회]
→ for (name, vmDir, isSymlink) in list [심볼릭 링크 필터링]
→ Data(contentsOf: vmDir.manifestURL) [각 이미지 매니페스트 로드]
→ Set(manifest.layers).intersection() [레이어 교집합]
```

---

## 3. 개선 제안

### 3.1 OCI Pull 잠금 세분화: 호스트 단위에서 이미지 단위로 전환

**문제 상세:**
동일 레지스트리 호스트의 모든 Pull이 직렬화되어, CI/CD 환경에서 여러 워커가 서로 다른 이미지를 동시에 Pull할 수 없다. `ghcr.io`에서 `macos-ventura`와 `macos-sonoma`를 동시에 Pull하려 해도 하나가 끝나야 다음이 시작된다.

**근본 원인:**
`FileLock(lockURL: hostDirectoryURL)`에서 `hostDirectoryURL`이 `~/.tart/cache/OCIs/ghcr.io/` 수준이다. 잠금 범위가 레지스트리 호스트 전체를 포괄한다.

**제안 솔루션:**
잠금 대상을 다이제스트 디렉토리 단위로 세분화한다. 구체적으로:

1. `FileLock`의 대상을 `hostDirectoryURL`에서 `digestDirectoryURL`(예: `~/.tart/cache/OCIs/ghcr.io/org/repo/sha256:abc123/`)로 변경한다.
2. 동일 다이제스트에 대한 중복 Pull만 직렬화하고, 서로 다른 이미지는 완전 병렬로 수행한다.
3. 매니페스트 Pull 단계(`registry.pullManifest`)까지는 잠금 없이 수행하고, 다이제스트 계산 후 해당 다이제스트에 대한 잠금을 획득한다.

```
개선 전: hostDir 잠금 → manifest pull → 전체 pull → 해제
개선 후: manifest pull (잠금 없음) → digest 계산 → digestDir 잠금 → exists 확인 → pull → 해제
```

**예상 효과:**
- 동일 호스트의 서로 다른 이미지 Pull이 완전 병렬화
- CI/CD 클러스터에서 N개 워커의 동시 Pull 처리량이 N배 향상 (네트워크 대역폭 한도 내에서)
- 동일 이미지에 대한 중복 다운로드 방지는 유지

**구현 난이도:** 중 (잠금 대상 URL 변경과 매니페스트 Pull 순서 조정 필요, 테스트 필요)

---

### 3.2 DiskV2 Push의 스트리밍 압축 파이프라인 전환

**문제 상세:**
512MB 청크를 통째로 `NSData.compressed(using: .lz4)`에 전달하므로, 압축 중 입력 데이터 + 출력 데이터가 동시에 메모리에 존재한다. `concurrency` 값만큼 병렬 실행되어 메모리 사용량이 곱해진다.

**근본 원인:**
Pull 경로에서는 `OutputFilter(.decompress, using: .lz4, bufferCapacity: 4MB)`를 사용하여 4MB 단위 스트리밍 해제를 구현했지만, Push 경로에서는 동일한 스트리밍 패턴을 적용하지 않고 `NSData.compressed()`를 사용한다.

**제안 솔루션:**

1. Push 경로에서도 `InputFilter(.compress, using: .lz4, bufferCapacity: 4MB)`를 사용하여 스트리밍 압축을 구현한다.
2. 512MB mmap 청크를 4MB 단위로 `InputFilter`에 공급하고, 압축 출력을 임시 파일 또는 `pushBlob`의 청크 업로드로 직접 전달한다.
3. 압축된 데이터의 SHA256 다이제스트를 스트리밍으로 계산한다 (`Digest` 클래스의 incremental `update()` 패턴 활용).

```
개선 전: mmap 512MB → NSData.compressed() [~512MB 메모리] → pushBlob(fromData: 압축 결과)
개선 후: mmap 512MB → InputFilter(4MB 단위) → Digest.update() → tmpFile.write() → pushBlob(fromFile)
```

**예상 효과:**
- 청크당 피크 메모리 사용량이 512MB에서 8~16MB로 감소 (입력 4MB + 출력 버퍼)
- concurrency=4 기준, 피크 메모리가 ~2GB에서 ~64MB로 약 30배 절감
- 8GB RAM Mac mini에서도 안정적인 Push 수행 가능

**구현 난이도:** 중~고 (스트리밍 압축 + 스트리밍 다이제스트 + 임시 파일 관리 또는 청크 업로드 통합 필요)

---

### 3.3 AuthenticationKeeper의 스코프별 토큰 캐시 도입

**문제 상세:**
`AuthenticationKeeper` actor가 단일 `Authentication?` 프로퍼티만 관리하여, 다른 스코프의 토큰 요청 시 기존 토큰이 덮어쓰기된다.

**근본 원인:**
초기 설계에서 하나의 Registry 인스턴스가 하나의 namespace만 담당하도록 되어 있어, 단일 토큰으로 충분했다. 그러나 이 가정이 향후 기능 확장(multi-arch index, cross-repo mount 등)에서 깨질 수 있다.

**제안 솔루션:**

1. `AuthenticationKeeper`의 내부 저장소를 `[String: Authentication]` 딕셔너리로 변경한다. 키는 `scope` 문자열이다.
2. `set()` 메서드에 scope 파라미터를 추가한다.
3. `header(for scope:)` 메서드가 해당 스코프의 유효한 토큰을 반환하되, 없으면 nil을 반환한다.
4. Basic 인증은 스코프와 무관하므로 별도 경로로 처리한다.

```swift
// 개선 후 AuthenticationKeeper
actor AuthenticationKeeper {
    var basic: BasicAuthentication? = nil
    var tokens: [String: TokenResponse] = [:]

    func set(_ auth: BasicAuthentication) { basic = auth }
    func set(_ token: TokenResponse, scope: String) { tokens[scope] = token }

    func header(scope: String?) -> (String, String)? {
        if let basic = basic { return basic.header() }
        if let scope = scope, let token = tokens[scope], token.isValid() {
            return token.header()
        }
        return nil
    }
}
```

**예상 효과:**
- 멀티 스코프 작업 시 불필요한 토큰 재발급(HTTP 401 왕복) 제거
- manifest list / cross-repository blob mount 등 향후 기능 확장에 대한 기반 마련
- 기존 단일 namespace 사용 패턴에는 영향 없음

**구현 난이도:** 하~중 (actor 내부 구조 변경, `auth()` 메서드에 scope 전달 경로 추가)

---

### 3.4 프루닝 메타데이터 인덱스 캐시 도입

**문제 상세:**
매번 프루닝이 필요할 때 전체 OCI 캐시 디렉토리를 순회하고, 각 VM 디렉토리의 3개 파일에 대해 `stat(2)` 시스템 콜로 크기와 접근 시간을 조회한다. 자동 프루닝(`reclaimIfNeeded`)은 Clone/Pull의 크리티컬 패스에서 호출된다.

**근본 원인:**
프루닝 시스템에 인덱스나 캐시 레이어가 없어, 매번 파일시스템을 O(N) 순회한다. `allocatedSizeBytes()`가 각 VM에 대해 3번의 `stat()` 호출을 수행하므로 실제로는 O(3N)이다.

**제안 솔루션:**

1. `~/.tart/cache/prune-index.json` 파일에 각 캐시 항목의 메타데이터(경로, allocatedSizeBytes, accessDate, digest)를 기록한다.
2. Pull 완료 시(`VMStorageOCI.move()`), Clone 완료 시 인덱스를 업데이트한다.
3. `reclaimIfNeeded()` 호출 시 인덱스 파일을 먼저 로드하고, 인덱스와 실제 파일시스템 간 불일치는 lazy하게 보정한다.
4. 인덱스 파일이 없거나 손상된 경우 기존 전체 순회 방식으로 폴백한다.
5. 인덱스 파일 접근도 `FileLock`으로 보호하여 동시성 안전성을 확보한다.

```
개선 전: reclaimIfNeeded → list() [디렉토리 순회] → stat() x 3N → sort → delete
개선 후: reclaimIfNeeded → 인덱스 로드 (1회 파일 읽기) → sort → delete → 인덱스 업데이트
```

**예상 효과:**
- 자동 프루닝의 오버헤드가 O(3N) stat 호출에서 O(1) 파일 읽기로 감소
- 100개 캐시 이미지 기준, 프루닝 판단 시간이 수백ms에서 수ms로 단축
- Clone/Pull의 전체 소요 시간에서 프루닝 오버헤드 사실상 제거

**구현 난이도:** 중 (인덱스 파일 스키마 설계, 일관성 보장 로직, 폴백 경로 구현)

---

### 3.5 PIDLock 래퍼를 통한 fcntl 시맨틱스 안전장치 강화

**문제 상세:**
fcntl(2) 기반 PIDLock은 같은 inode에 대한 다른 fd의 close()가 잠금을 해제하는 위험한 시맨틱스를 가진다. 현재 `Run.swift`의 주석으로만 이 제약이 문서화되어 있어, 새로운 개발자가 실수할 위험이 높다.

**근본 원인:**
POSIX fcntl(2) 잠금의 프로세스 단위 시맨틱스(동일 프로세스 내에서 같은 inode의 어떤 fd라도 close하면 모든 잠금 해제)가 Swift의 파일 I/O 패턴과 충돌한다.

**제안 솔루션:**

1. `VMDirectory`에 `withLockedConfig<T>(_ body: (VMConfig, PIDLock) throws -> T) throws -> T` 메서드를 추가한다.
2. 이 메서드 내부에서 config.json을 읽고, 그 후 PIDLock을 획득한 다음, 클로저를 실행한다.
3. PIDLock의 전체 유효 기간 동안 config.json에 대한 다른 파일 디스크립터가 열리지 않도록 보장한다.
4. 기존 `Run.swift`, `Push.swift`, `Delete.swift`의 잠금 패턴을 이 메서드로 통합한다.
5. `#if DEBUG` 조건부 컴파일로, config.json에 대한 중복 open을 감지하는 assert를 추가한다.

```swift
// 제안하는 안전 래퍼
extension VMDirectory {
    func withLockedConfig<T>(_ body: (VMConfig, PIDLock) throws -> T) throws -> T {
        // 1. config 먼저 읽기 (fd 열고 닫기)
        let config = try VMConfig(fromURL: configURL)
        // 2. 이제 PIDLock 획득 (새 fd)
        let lock = try self.lock()
        guard try lock.trylock() else {
            throw RuntimeError.VMAlreadyRunning(name)
        }
        // 3. 잠금 하에서 작업 수행
        return try body(config, lock)
    }
}
```

**예상 효과:**
- fcntl 시맨틱스로 인한 잠금 소실 버그 구조적 방지
- 새로운 개발자의 실수 가능성 제거
- 기존 잠금 패턴의 코드 중복 감소

**구현 난이도:** 하 (API 설계 + 기존 코드의 점진적 마이그레이션)

---

### 3.6 LocalLayerCache 선택의 사전 계산 인덱스 도입

**문제 상세:**
Pull 시작 전 `chooseLocalLayerCache()`에서 모든 캐시 이미지의 매니페스트를 로드하고 레이어 교집합을 계산한다. 캐시 이미지 수에 비례하여 Pull 시작이 지연된다.

**근본 원인:**
OCI 이미지 간 레이어 공유 정보가 사전에 인덱싱되어 있지 않아, 매번 전체 탐색이 필요하다.

**제안 솔루션:**

1. `~/.tart/cache/layer-index.json`에 `{compressed_digest: [image_digest_list]}` 형태의 역 인덱스를 유지한다.
2. Pull 완료 후 해당 이미지의 레이어 다이제스트를 인덱스에 추가한다.
3. `chooseLocalLayerCache()` 호출 시, Pull 대상 매니페스트의 레이어 다이제스트를 역 인덱스에서 조회하여 가장 많은 레이어를 공유하는 이미지를 O(L) (L = 레이어 수)에 찾는다.
4. 인덱스와 실제 캐시 상태의 불일치는 폴백 로직으로 처리한다(인덱스가 가리키는 이미지가 삭제된 경우 건너뛰기).

```
개선 전: list() [N개 이미지] → 각 manifest 로드 [N회 파일 I/O] → Set 교집합 [N x L]
개선 후: layer-index 로드 [1회] → digest 조회 [L회 해시맵 lookup] → 최적 이미지 선정
```

**예상 효과:**
- 베이스 이미지 선택 시간이 O(N * L)에서 O(L)로 감소 (N = 캐시 이미지 수, L = 레이어 수)
- 50개 캐시 이미지 기준, 수백ms에서 수ms로 단축
- Pull 시작 지연 사실상 제거

**구현 난이도:** 중 (역 인덱스 스키마 설계, 인덱스 업데이트/삭제 일관성, 폴백 로직)

---

## 4. 우선순위 및 로드맵

### Phase 1: 즉시 개선 (1~2주) - 안정성 및 안전성

| 순서 | 개선 항목 | 난이도 | 기대 효과 | 근거 |
|------|----------|--------|----------|------|
| 1 | 3.5 PIDLock 안전 래퍼 | 하 | 잠금 관련 잠재 버그 구조적 방지 | 코드 변경 최소, 즉각적 안전성 향상 |
| 2 | 3.3 스코프별 토큰 캐시 | 하~중 | 향후 기능 확장 기반, 불필요한 401 제거 | AuthenticationKeeper 내부만 변경 |

### Phase 2: 성능 최적화 (3~4주) - Pull/Push 핵심 경로

| 순서 | 개선 항목 | 난이도 | 기대 효과 | 근거 |
|------|----------|--------|----------|------|
| 3 | 3.1 OCI Pull 잠금 세분화 | 중 | CI/CD 클러스터 Pull 처리량 N배 향상 | 가장 큰 CI/CD 병목 해소 |
| 4 | 3.2 스트리밍 압축 파이프라인 | 중~고 | Push 피크 메모리 30배 절감 | 저사양 환경 안정성 확보 |

### Phase 3: 캐시 인텔리전스 (5~6주) - 캐시 효율성

| 순서 | 개선 항목 | 난이도 | 기대 효과 | 근거 |
|------|----------|--------|----------|------|
| 5 | 3.4 프루닝 메타데이터 인덱스 | 중 | 자동 프루닝 오버헤드 수백배 감소 | Clone/Pull 크리티컬 패스 최적화 |
| 6 | 3.6 레이어 역 인덱스 | 중 | 베이스 이미지 선택 N배 가속 | Pull 시작 지연 제거 |

---

## 5. 기대 성과

### 정량적 기대 효과

| 지표 | 현재 상태 | 개선 후 예상 | 관련 개선 항목 |
|------|----------|-------------|-------------|
| 동일 호스트 동시 Pull 처리량 | 1x (직렬) | Nx (병렬, N=워커 수) | 3.1 |
| Push 피크 메모리 (50GB, conc=4) | ~2~3GB | ~64MB | 3.2 |
| 토큰 재발급 빈도 (멀티 스코프) | 매 스코프 전환마다 | 스코프당 1회 (TTL 내) | 3.3 |
| 자동 프루닝 오버헤드 (100개 캐시) | ~500ms (300+ stat 호출) | ~5ms (1회 파일 읽기) | 3.4 |
| PIDLock 관련 버그 발생 가능성 | 코드 리뷰 의존 | 구조적 방지 | 3.5 |
| 베이스 이미지 선택 시간 (50개 캐시) | ~500ms (50회 manifest 파싱) | ~5ms (해시맵 조회) | 3.6 |

### 정성적 기대 효과

- **CI/CD 처리량 향상**: Phase 2 완료 후, 대규모 macOS CI/CD 팜에서 VM 프로비저닝 시간이 전반적으로 단축되어 빌드 파이프라인의 대기 시간 감소
- **저사양 환경 안정성**: 스트리밍 압축 전환으로 8GB RAM Mac mini에서도 Push 작업이 안정적으로 수행 가능
- **코드 유지보수성**: PIDLock 안전 래퍼와 인덱스 캐시 도입으로 새로운 기능 추가 시 버그 발생 위험 감소
- **확장성 기반 마련**: 스코프별 토큰 캐시와 레이어 인덱스가 향후 multi-arch 이미지 지원, incremental push/pull 등 고급 기능의 기반이 됨
