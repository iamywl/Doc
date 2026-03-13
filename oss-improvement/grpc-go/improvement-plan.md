# gRPC-Go 구조적 개선 제안서

## 1. 프로젝트 개요

gRPC-Go는 Google이 설계한 고성능 RPC 프레임워크의 Go 구현체로, HTTP/2 기반 스트림 멀티플렉싱과 Protocol Buffers 직렬화를 핵심으로 한다. 플러그인 아키텍처를 통해 Resolver, Balancer, Interceptor, Codec, Compressor, Credentials 등 거의 모든 컴포넌트가 인터페이스로 추상화되어 교체 가능하다.

**핵심 구성:**
- 서버 진입점: `server.go` (Server 구조체, L126) / 클라이언트 진입점: `clientconn.go` (ClientConn, L667)
- Transport 계층: `internal/transport/` (http2Server, http2Client, loopyWriter, controlBuffer)
- 로드 밸런싱: `balancer/` (Balancer, Picker, SubConn) + `picker_wrapper.go` (pickerWrapper)
- 인코딩: `encoding/` (Codec, Compressor) + `rpc_util.go` (프레이밍, 직렬화/역직렬화)
- 재시도: `stream.go` (shouldRetry, withRetry, replayBuffer) + `service_config.go`

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 controlBuffer의 단일 연결 리스트 병목

**위치:** `internal/transport/controlbuf.go`

controlBuffer는 loopyWriter로 프레임 전송 요청을 전달하는 FIFO 큐로, 모든 고루틴에서 동시에 `put()`을 호출하여 HEADERS, DATA, SETTINGS, WINDOW_UPDATE, PING, GOAWAY 등의 제어 메시지를 큐잉한다. 현재 구현은 `sync.Mutex`로 보호되는 단일 연결 리스트(`list`)와 채널 기반 시그널링(`ch chan struct{}`)을 사용한다.

**구체적 문제:**
- `put()` 호출 시 mutex 획득 -> 리스트 append -> 채널 시그널의 3단계를 거치며, 고빈도 RPC 환경(수십만 QPS)에서 `put()` 호출이 loopyWriter의 `get()` 호출과 동일한 mutex를 경합한다.
- loopyWriter가 `get()`으로 아이템을 꺼내는 동안 다른 고루틴의 `put()`이 블로킹되어, 특히 서버 측 http2Server에서 동시 스트림 수가 많을 때 쓰기 지연이 전파된다.
- 제어 프레임(SETTINGS, PING)과 데이터 프레임(DATA, HEADERS)이 동일한 큐에 들어가므로, 데이터 폭주 시 제어 프레임 전송이 지연될 수 있다.

### 2.2 서버 워커 풀의 고정 크기 및 폴백 비효율

**위치:** `server.go` L150 (`serverWorkerChannel`), L1054-1073 (`serveStreams`)

서버 워커 풀은 `initServerWorkers()`에서 고정 크기(`numServerWorkers`)의 고루틴을 생성하고, `serverWorkerChannel chan func()`를 통해 작업을 분배한다. 채널이 가득 차면 `select { case s.serverWorkerChannel <- f: return; default: }` 패턴으로 폴백하여 새 고루틴을 생성한다.

**구체적 문제:**
- 워커 풀 크기가 정적이어서 부하 변동에 적응하지 못한다. 피크 시간에는 폴백으로 새 고루틴이 대량 생성되어 워커 풀의 이점이 사라지고, 유휴 시간에는 불필요한 고루틴이 `serverWorkerChannel`에서 블로킹 대기한다.
- `serverWorkerChannel`의 버퍼 크기가 워커 수와 동일하게 설정되므로, 워커가 모두 바쁘면 버퍼도 즉시 가득 차서 폴백이 빈번하게 발생한다.
- `streamQuota` (`handlerQuota`)와 워커 풀이 독립적으로 동시성을 제어하여, `maxConcurrentStreams` 설정과 워커 수 설정 간의 최적 비율을 사용자가 직접 튜닝해야 한다.
- 워커가 처리 중인 RPC의 지연 시간(latency) 분포를 고려하지 않아, 느린 RPC 하나가 워커를 점유하면 다른 빠른 RPC도 폴백으로 밀려난다.

### 2.3 pickerWrapper의 블로킹 대기 및 Picker 갱신 비효율

**위치:** `picker_wrapper.go` L48 (`pickerWrapper`), L105 (`pick()`)

`pickerWrapper`는 `atomic.Pointer[pickerGeneration]`을 사용하여 lock-free 읽기를 구현하지만, Picker가 아직 설정되지 않았거나 `ErrNoSubConnAvailable`이 반환될 때 `blockingCh chan struct{}`에서 블로킹 대기한다.

**구체적 문제:**
- `pick()` 루프에서 Picker가 SubConn을 반환하더라도 `getReadyTransport()`가 nil을 반환하면(SubConn이 아직 READY가 아닌 경우) 다시 루프를 돌며 새로운 `blockingCh`를 대기한다. 이때 Picker 세대가 변경되지 않으면 busy-wait에 가까운 상황이 발생한다.
- Balancer 상태 업데이트 시 `updatePicker()`가 호출되면 기존 `blockingCh`를 닫고 새로 생성하는데, 이 시점에 대기 중인 모든 RPC 고루틴이 동시에 깨어나 동일한 Picker에 `Pick()`을 호출하는 thundering herd 현상이 발생한다.
- `pick()` 내부에서 context 취소/만료 확인이 루프 시작 부분에서만 이루어져, Picker.Pick() 호출 자체가 오래 걸리는 커스텀 Picker에서는 context 만료 후에도 불필요한 작업이 계속될 수 있다.

### 2.4 인코딩/압축 파이프라인의 메모리 할당 비효율

**위치:** `rpc_util.go` (encode, compress, decompress, recvAndDecompress), `encoding/proto/proto.go`, `encoding/gzip/gzip.go`

현재 직렬화-압축-프레이밍 파이프라인은 다음 단계를 거친다:
1. `codec.Marshal(v)` -> `[]byte` 또는 `mem.BufferSlice` 할당
2. `compress(data)` -> 새로운 `[]byte` 할당 (gzip의 경우 `sync.Pool`에서 Writer를 가져오지만 출력 버퍼는 새로 할당)
3. 5바이트 프레이밍 헤더 생성 + payload 결합

**구체적 문제:**
- Codec V1 (`encoding.Codec`)의 `Marshal(v any) ([]byte, error)` 인터페이스가 `[]byte`를 반환하므로, 매 RPC마다 새 바이트 슬라이스를 할당한다. Codec V2 (`encoding.CodecV2`)가 `mem.BufferSlice` 기반으로 개선되었지만, V1 -> V2 브리지 패턴(`codec.go`의 `codecV0Bridge`, `codecV1Bridge`)에서 변환 시 추가 복사가 발생한다.
- gzip 압축기의 `Compress()` 구현(`encoding/gzip/gzip.go`)은 `sync.Pool`로 `gzip.Writer`를 재사용하지만, 압축 결과를 담는 `bytes.Buffer`는 매번 새로 생성한다. 대용량 메시지의 경우 이 버퍼가 수 MB에 달할 수 있다.
- `recvAndDecompress()`에서 수신 메시지의 5바이트 헤더를 읽은 후 payload 전체를 메모리에 로드하고, 압축 해제 시 추가 버퍼를 할당하여, 압축된 메시지의 경우 최소 2배의 메모리를 일시적으로 소비한다.
- `prepareMsg()` 함수에서 메시지를 사전 직렬화할 때, 이미 직렬화된 데이터를 다시 `codec.Marshal()`로 인코딩하는 경로가 있어 불필요한 이중 직렬화가 발생할 수 있다.

### 2.5 DNS Resolver의 폴링 기반 갱신과 TTL 미반영

**위치:** `internal/resolver/dns/dns_resolver.go`

DNS Resolver는 주기적으로(기본 30분) DNS 조회를 반복하는 폴링 방식을 사용한다. `ResolveNow()`가 호출되면 즉시 재조회를 트리거하지만, DNS 레코드의 TTL을 고려하지 않는다.

**구체적 문제:**
- 30분 폴링 간격은 Kubernetes 환경에서 Pod 스케일링 이벤트(수초 단위)에 비해 너무 느리다. 새로운 Pod이 추가되어도 최대 30분간 트래픽을 받지 못한다.
- DNS TTL 값을 무시하므로, TTL이 60초인 레코드도 30분간 캐싱되어 오래된 주소로 연결을 시도할 수 있다.
- `ResolveNow()`가 여러 SubConn에서 동시에 호출되면 중복 DNS 조회가 발생하는데, 디바운싱 메커니즘이 단순한 타이머 기반이어서 불필요한 조회가 발생할 수 있다.
- SRV 레코드와 A 레코드를 별도로 조회한 뒤 병합하는 과정에서, 두 조회 사이에 DNS 레코드가 변경되면 불일치가 발생할 수 있다.

---

## 3. 개선 제안

### 3.1 controlBuffer를 lock-free 링 버퍼로 교체

**문제 상세:**
controlBuffer의 `put()`/`get()` 경합이 고부하 환경에서 loopyWriter의 프레임 전송 처리량을 제한한다. 특히 서버 측에서 수천 개의 동시 스트림이 각각 DATA 프레임과 WINDOW_UPDATE를 동시에 큐잉할 때, 단일 mutex가 직렬화 지점(serialization point)이 된다.

**근본 원인:**
단일 mutex + 연결 리스트 구조에서 생산자(다수 고루틴)와 소비자(loopyWriter 단일 고루틴)가 동일한 lock을 공유한다. Go의 `sync.Mutex`는 내부적으로 세마포어 기반이며, 경합 시 고루틴 파킹/언파킹 비용이 발생한다.

**제안 솔루션:**
1. **MPSC(Multi-Producer Single-Consumer) lock-free 큐 도입**: loopyWriter가 단일 소비자이므로, CAS(Compare-And-Swap) 기반 MPSC 큐가 적합하다. 생산자는 `atomic.CompareAndSwapPointer`로 새 노드를 연결하고, 소비자는 lock 없이 순차적으로 꺼낸다.
2. **우선순위 분리 큐**: 제어 프레임용 고우선순위 큐와 데이터 프레임용 일반 큐를 분리한다. loopyWriter의 `run()` 루프에서 고우선순위 큐를 먼저 드레인한 후 일반 큐를 처리한다.
3. **배치 get() 지원**: loopyWriter가 한 번의 get()으로 여러 아이템을 가져와 배치 처리하여 lock/CAS 횟수를 줄인다.

```
현재:  put() [mutex lock] -> append -> [unlock] -> signal
제안:  put() [CAS] -> append (lock-free) -> signal (atomic flag)
       get() [no lock] -> batch drain -> process all
```

**예상 효과:**
- put()/get() 경합 제거로 고부하 시 프레임 전송 처리량 20-40% 향상 예상
- 제어 프레임 우선순위 분리로 PING/SETTINGS 응답 지연 감소 (keepalive 안정성 향상)
- 배치 처리로 syscall(conn.Write) 횟수 절감

**구현 난이도:** 상 (High)
- lock-free 자료구조의 정확성 검증이 까다롭고, Go의 GC와의 상호작용을 고려해야 한다.
- 기존 controlBuffer를 사용하는 모든 코드 경로(http2Client, http2Server 양쪽)에 영향을 준다.
- 성능 회귀 테스트를 위한 벤치마크 인프라 구축이 필요하다.

---

### 3.2 적응형 서버 워커 풀 (Adaptive Worker Pool)

**문제 상세:**
고정 크기 워커 풀이 부하 변동에 적응하지 못하여, 피크 시 폴백 고루틴이 대량 생성되고 유휴 시 리소스가 낭비된다. `serverWorkerChannel`의 select-default 폴백 패턴은 워커 풀이 포화될 때마다 고루틴 생성 비용을 그대로 노출한다.

**근본 원인:**
워커 풀 크기가 `NewServer()` 시점에 고정되며, 런타임 부하를 모니터링하는 메커니즘이 없다. `maxConcurrentStreams`(HTTP/2 레벨)와 `streamQuota`(gRPC 서버 레벨)와 워커 풀이 독립적으로 동시성을 제어하여 최적 설정을 찾기 어렵다.

**제안 솔루션:**
1. **동적 워커 스케일링**: 워커 풀에 활성 워커 수(busy count)를 추적하는 `atomic.Int64` 카운터를 추가한다. busy ratio가 상한(예: 80%)을 초과하면 워커를 추가 생성하고, 하한(예: 20%) 미만이면 유휴 워커를 종료한다.
2. **Work-stealing 큐 도입**: 단일 `serverWorkerChannel` 대신 워커별 로컬 큐를 두고, 자신의 큐가 비면 다른 워커의 큐에서 작업을 steal한다. 이렇게 하면 채널 경합이 워커 수에 비례하여 분산된다.
3. **RPC 지연 시간 기반 워커 분류**: Fast path(< 1ms 예상)와 slow path(> 10ms 예상)를 분리하여, 느린 RPC가 빠른 RPC의 워커를 점유하지 않도록 한다. 메서드별 p99 지연 시간을 EMA(지수이동평균)로 추적하여 분류 기준으로 사용한다.

```go
// 제안하는 적응형 워커 풀 구조
type adaptiveWorkerPool struct {
    minWorkers   uint32
    maxWorkers   uint32
    activeCount  atomic.Int64  // 현재 작업 중인 워커 수
    totalWorkers atomic.Int64  // 전체 워커 수
    taskCh       chan func()
    scaleTicker  *time.Ticker  // 스케일링 판단 주기 (예: 1초)
}
```

**예상 효과:**
- 폴백 고루틴 생성 횟수 80-90% 감소 (피크 시 사전 스케일업)
- 유휴 시 메모리 절약 (불필요한 워커 자동 종료)
- RPC 지연 시간 분포 개선 (느린 RPC 격리)

**구현 난이도:** 중 (Medium)
- 기존 `serverWorkerChannel` 인터페이스를 유지하면서 내부 구현만 교체 가능하다.
- 스케일링 정책의 파라미터 튜닝이 필요하지만, 보수적인 기본값으로 시작할 수 있다.
- `streamQuota`와의 통합은 별도 작업으로 분리 가능하다.

---

### 3.3 pickerWrapper의 세밀한 깨움 메커니즘 (Fine-grained Wake-up)

**문제 상세:**
Picker가 갱신될 때 `blockingCh`를 닫아 모든 대기 중인 RPC를 동시에 깨우면, 수천 개의 고루틴이 동시에 `Picker.Pick()`을 호출하여 thundering herd가 발생한다. 또한 `getReadyTransport()` nil 반환 시의 재시도 루프가 비효율적이다.

**근본 원인:**
`blockingCh`가 이진 상태(열림/닫힘)만 표현하여, "깨울 대상을 선별"하는 것이 불가능하다. 모든 대기 고루틴이 동일한 채널에서 대기하므로 all-or-nothing 깨움만 가능하다.

**제안 솔루션:**
1. **계층적 깨움 (Tiered Wake-up)**: 대기 중인 고루틴을 버킷(예: 8개)으로 분할하고, Picker 갱신 시 버킷을 순차적으로 깨운다. 첫 번째 버킷의 RPC가 SubConn을 소비하면, 남은 SubConn 용량에 따라 다음 버킷을 깨울지 결정한다.
2. **SubConn 준비 알림 채널**: `getReadyTransport()` nil 반환 시, 해당 SubConn의 상태 변경 채널에서 직접 대기한다. 현재는 Picker 전체 갱신을 기다리지만, SubConn 개별 READY 전환을 직접 관찰하면 불필요한 루프를 제거할 수 있다.
3. **적응적 백오프**: `Pick()` 실패 후 즉시 재시도하는 대신, 짧은 백오프(1ms -> 2ms -> 4ms, 최대 16ms)를 적용하여 CPU 소비를 줄인다. 단, `WaitForReady` RPC에만 적용하고, fail-fast RPC는 즉시 실패를 반환한다.

```
현재:
  [RPC 1] --wait--> [blockingCh close] --Pick()--> [경합!]
  [RPC 2] --wait--> [blockingCh close] --Pick()--> [경합!]
  [RPC 3] --wait--> [blockingCh close] --Pick()--> [경합!]
  ...N개 동시 깨어남

제안:
  [Bucket 0: RPC 1,2] --wake--> Pick() --> 성공 -> 다음 버킷 깨움
  [Bucket 1: RPC 3,4] --wake--> Pick() --> ...
  [Bucket 2: RPC 5,6] --대기 유지 (SubConn 소진 시)
```

**예상 효과:**
- Picker 갱신 시 순간 CPU 스파이크 70-80% 감소
- p99 RPC 지연 시간 개선 (thundering herd 해소)
- `getReadyTransport()` nil 재시도 루프의 CPU 소비 감소

**구현 난이도:** 중 (Medium)
- `pickerWrapper` 내부 구현 변경이지만, `balancer.Picker` 인터페이스 자체는 변경하지 않는다.
- 버킷 수와 깨움 전략의 튜닝이 필요하다.
- 기존 테스트에서 타이밍 의존적인 부분의 수정이 필요할 수 있다.

---

### 3.4 인코딩 파이프라인의 제로카피 최적화

**문제 상세:**
직렬화 -> 압축 -> 프레이밍 파이프라인에서 각 단계마다 새로운 바이트 슬라이스가 할당되어 GC 압박이 증가한다. 특히 Codec V1 -> V2 브리지 경로와 gzip 압축 버퍼에서 불필요한 메모리 복사가 발생한다.

**근본 원인:**
1. Codec V1 인터페이스(`Marshal(v any) ([]byte, error)`)가 `[]byte`를 반환하도록 설계되어, 호출자가 버퍼를 제공할 수 없다.
2. gzip 압축기가 `bytes.Buffer`를 매번 새로 생성하여 `sync.Pool`의 이점을 활용하지 못한다.
3. 프레이밍 헤더(5바이트)와 payload를 별도로 관리하여 `net.Conn.Write()`에 2번의 시스템 콜이 필요할 수 있다.

**제안 솔루션:**
1. **Codec V2 전면 전환 가속**: V1 코덱 사용자에게 V2 마이그레이션 가이드를 제공하고, V1 -> V2 브리지에서의 `mem.BufferSlice` -> `[]byte` -> `mem.BufferSlice` 이중 변환을 제거하기 위해 브리지 경로에 `mem.BufferPool`을 직접 통합한다.
2. **gzip 압축 버퍼 풀링**: `encoding/gzip/gzip.go`의 `Compress()` 구현에서 `bytes.Buffer`도 `sync.Pool`로 관리한다. 버퍼 크기별 풀(small: < 4KB, medium: < 64KB, large: < 1MB)을 두어 크기 불일치로 인한 메모리 낭비를 줄인다.
3. **프레이밍 헤더 인라인**: 5바이트 프레이밍 헤더를 payload 슬라이스의 앞부분에 직접 기록하여, 별도의 `hdr []byte` 할당을 제거하고 단일 `Write()` 호출로 전송한다. `mem.BufferSlice`의 `Prepend()` 메서드를 추가하여 구현한다.
4. **직렬화 결과 캐싱**: 동일한 protobuf 메시지가 반복 전송되는 패턴(서버 스트리밍에서 유사한 응답)에서, 직렬화 결과의 fingerprint를 비교하여 재직렬화를 스킵하는 선택적 캐싱 레이어를 도입한다.

```
현재 경로 (V1 Codec + gzip):
  Marshal() -> [alloc 1: []byte]
  -> compress() -> [alloc 2: bytes.Buffer] -> [alloc 3: compressed []byte]
  -> framing -> [alloc 4: hdr []byte]
  -> Write(hdr) + Write(data): 2 syscalls

제안 경로 (V2 Codec + pooled gzip):
  MarshalAppend(pooledBuf) -> [pool에서 가져온 BufferSlice에 직접 기록]
  -> compress(pooledBuf) -> [pool에서 가져온 Buffer에 압축]
  -> Prepend(5-byte header) -> [추가 할당 없음]
  -> Write(combined): 1 syscall
```

**예상 효과:**
- RPC당 메모리 할당 횟수 50-70% 감소
- GC 중단 시간(pause time) 감소로 p99 지연 시간 개선
- 대용량 메시지(> 1MB) 처리 시 처리량 30-50% 향상 예상

**구현 난이도:** 중-상 (Medium-High)
- V2 Codec 전환은 하위 호환성을 유지하면서 점진적으로 수행 가능하다.
- gzip 버퍼 풀링은 독립적으로 구현 가능한 저위험 개선이다.
- 프레이밍 헤더 인라인은 `transport.Write()` 인터페이스 변경이 필요하여 영향 범위가 넓다.

---

### 3.5 DNS Resolver의 TTL 인식 및 이벤트 기반 갱신

**문제 상세:**
고정 30분 폴링 간격이 Kubernetes 등 동적 환경에서 서비스 디스커버리 지연을 초래한다. DNS TTL을 무시하여 오래된 주소로의 연결 시도가 발생하고, SRV/A 레코드 조회 간 경쟁 상태(race condition)가 있다.

**근본 원인:**
Go 표준 라이브러리의 `net.Resolver`가 DNS 응답의 TTL 값을 노출하지 않아, gRPC DNS Resolver가 자체적으로 TTL을 추적할 수 없다. 또한 폴링 기반 설계가 이벤트 기반 갱신보다 구현이 단순하여 채택되었다.

**제안 솔루션:**
1. **TTL 인식 DNS 클라이언트**: `miekg/dns` 라이브러리를 사용하여 DNS 응답의 TTL 값을 직접 추출하고, 다음 폴링 시점을 `min(TTL * 0.75, maxPollingInterval)`로 동적 조정한다. 기존 `net.Resolver`는 폴백 경로로 유지한다.
2. **적응적 폴링 간격**: DNS 레코드 변경 빈도를 추적하여, 최근 변경이 잦으면 폴링 간격을 줄이고(최소 5초), 안정적이면 늘린다(최대 5분). 지수이동평균(EMA)으로 변경 빈도를 추정한다.
3. **SRV+A 원자적 조회**: SRV 레코드와 A 레코드를 동시에 조회하고, 두 결과의 타임스탬프를 비교하여 일관성을 검증한다. 불일치 시 즉시 재조회한다.
4. **Kubernetes 전용 Resolver 옵션**: Kubernetes headless service의 경우, `endpoints` API를 watch하여 이벤트 기반으로 즉시 갱신하는 별도 Resolver Builder를 제공한다. DNS 폴링 대비 수초 -> 수백 밀리초로 갱신 지연을 단축한다.

```
현재:
  t=0      DNS 조회 -> [A, B, C]
  t=30min  DNS 조회 -> [A, B, D]  (C 제거, D 추가: 30분간 미반영)

제안 (TTL 인식):
  t=0      DNS 조회 -> [A, B, C] (TTL=60s)
  t=45s    DNS 조회 -> [A, B, D]  (TTL*0.75=45s에 재조회)

제안 (K8s watch):
  t=0      Watch 시작 -> [A, B, C]
  t=3s     이벤트 수신 -> [A, B, D]  (즉시 반영)
```

**예상 효과:**
- 서비스 디스커버리 지연 30분 -> 수초(TTL 인식) 또는 수백 밀리초(K8s watch)로 단축
- 오래된 주소로의 불필요한 연결 시도 제거
- DNS 조회 횟수 최적화 (TTL이 긴 레코드의 경우 불필요한 조회 감소)

**구현 난이도:** 중 (Medium)
- TTL 인식 DNS 클라이언트는 외부 라이브러리 의존성 추가가 필요하다.
- 기존 `resolver.Builder` 인터페이스를 변경하지 않고 새 Builder로 제공 가능하다.
- K8s watch Resolver는 `k8s.io/client-go` 의존성으로 별도 모듈로 분리해야 한다.

---

## 4. 우선순위 및 로드맵

### Phase 1: 즉시 적용 가능한 저위험 개선 (1-2개월)

| 순위 | 개선 항목 | 이유 |
|------|-----------|------|
| 1 | gzip 압축 버퍼 풀링 (3.4의 일부) | 독립적, 저위험, 즉각적인 메모리 효율 개선 |
| 2 | DNS Resolver 적응적 폴링 간격 (3.5의 일부) | 기존 인터페이스 변경 없음, K8s 환경에서 즉시 효과 |
| 3 | pickerWrapper 적응적 백오프 (3.3의 일부) | 단순 구현, CPU 소비 감소 |

### Phase 2: 중기 구조 개선 (3-4개월)

| 순위 | 개선 항목 | 이유 |
|------|-----------|------|
| 4 | 적응형 서버 워커 풀 (3.2 전체) | 서버 처리량 직접 향상, 기존 인터페이스 호환 |
| 5 | pickerWrapper 계층적 깨움 (3.3 전체) | thundering herd 해소, p99 지연 개선 |
| 6 | DNS TTL 인식 + K8s watch Resolver (3.5 전체) | 동적 환경 지원 강화 |

### Phase 3: 장기 아키텍처 개선 (5-8개월)

| 순위 | 개선 항목 | 이유 |
|------|-----------|------|
| 7 | Codec V2 전면 전환 + 프레이밍 인라인 (3.4 전체) | 하위 호환성 관리 필요, 넓은 영향 범위 |
| 8 | controlBuffer lock-free 교체 (3.1 전체) | 높은 구현 난이도, 철저한 검증 필요 |

### 주요 마일스톤

```
Month 1-2:  Phase 1 구현 + 벤치마크 인프라 구축
Month 3:    Phase 1 성능 검증 + Phase 2 설계
Month 4-6:  Phase 2 구현 + 통합 테스트
Month 7:    Phase 2 성능 검증 + Phase 3 설계
Month 8-10: Phase 3 구현 + 회귀 테스트 + 커뮤니티 RFC
```

---

## 5. 기대 성과

### 정량적 기대 효과

| 지표 | 현재 수준 (추정) | 개선 후 목표 | 관련 제안 |
|------|------------------|-------------|-----------|
| RPC당 메모리 할당 | 5-8회 | 2-3회 | 3.4 인코딩 최적화 |
| 서버 처리량 (QPS) | 기준선 | +20-30% | 3.1 controlBuffer + 3.2 워커 풀 |
| p99 지연 시간 | 기준선 | -30-40% | 3.3 pickerWrapper + 3.2 워커 풀 |
| GC pause 빈도 | 기준선 | -40-50% | 3.4 메모리 풀링 |
| 서비스 디스커버리 지연 | 최대 30분 | < 5초 (TTL 인식) / < 500ms (K8s watch) | 3.5 DNS 개선 |
| Picker 갱신 시 CPU 스파이크 | 기준선 | -70-80% | 3.3 계층적 깨움 |

### 정성적 기대 효과

1. **운영 안정성 향상**: 적응형 워커 풀과 TTL 인식 DNS로 부하 변동과 인프라 변경에 자동 적응하여 수동 튜닝 필요성이 감소한다.
2. **Kubernetes 네이티브 경험**: K8s watch 기반 Resolver로 Pod 스케일링/롤링 업데이트 시 서비스 중단 없는 연결 전환이 가능하다.
3. **대규모 배포 환경 최적화**: lock-free controlBuffer와 계층적 Picker 깨움으로, 단일 서버에서 수만 개의 동시 연결을 처리하는 엣지 프록시/게이트웨이 시나리오에서 성능이 크게 향상된다.
4. **오픈소스 커뮤니티 기여**: gRPC-Go 메인 프로젝트에 upstream 기여로 이어질 수 있으며, 현대 에코시스템에서 gRPC-Go의 경쟁력을 강화한다.
5. **기술 역량 축적**: HTTP/2 프로토콜, lock-free 자료구조, 적응형 동시성 제어 등 고급 시스템 프로그래밍 기법에 대한 조직 내 기술력이 축적된다.
