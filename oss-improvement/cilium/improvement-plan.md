# Cilium 오픈소스 개선 제안서

## 1. 프로젝트 개요

Cilium은 eBPF 기반의 Kubernetes 네트워킹, 보안, 옵저버빌리티 플랫폼이다. 4개의 핵심 바이너리(Agent, Operator, CNI Plugin, Hubble Relay)가 Hive 셀 아키텍처 기반으로 협력하며, Linux 커널 내부의 eBPF 프로그램으로 패킷을 처리하여 iptables 대비 O(1) 룩업 성능을 달성한다.

주요 서브시스템: eBPF 데이터패스(~30,000줄 C), 정책 엔진(SelectorCache/PolicyRepository), 로드밸런싱(Maglev/Random), FQDN 정책(DNS Proxy), Hubble 옵저버빌리티, IPAM(8가지 모드), ClusterMesh 멀티클러스터.

---

## 2. 현재 구조적 한계점 및 성능 병목

### 2.1 FQDN 정책의 DNS 프록시 병목 및 정규식 매칭 비용

**현상**: FQDN 정책이 활성화되면 모든 DNS 트래픽이 `pkg/fqdn/dnsproxy/proxy.go`의 `DNSProxy.ServeDNS()`를 경유한다. 이 과정에서 `perEPAllow` 맵의 계층적 조회(Endpoint ID -> Port/Protocol -> CachedSelector -> Regexp Match)가 수행되며, 특히 `*.example.com` 같은 와일드카드 패턴은 `regexp.Regexp` 매칭을 매 DNS 요청마다 실행한다.

- **정규식 캐시(`regexCache`)는 컴파일만 캐시**하고, 매칭 결과는 캐시하지 않는다. 동일한 도메인에 대한 반복 DNS 쿼리에서도 매번 정규식 매칭이 수행됨.
- `ConcurrencyLimit *semaphore.Weighted`로 동시 처리량을 제한하지만, 세마포어 대기 시 DNS 응답 지연이 Pod 전체로 전파됨.
- `NameManager`의 `nameLocks []*lock.Mutex`(256 버킷)는 FNV 해시 충돌 시 서로 다른 도메인 간에도 잠금 경합 발생.

**영향**: DNS 응답 지연 100-500us 추가, 대규모 클러스터(1000+ Pod)에서 DNS 프록시가 병목 지점이 됨.

### 2.2 정책 엔진의 전체 재계산 비용과 SelectorCache 잠금 경합

**현상**: `pkg/policy/repository.go`의 `ReplaceByResource()` 호출 시, 영향받는 모든 엔드포인트에 대해 `GetSelectorPolicy()` -> `resolvePolicyLocked()` -> `computePolicyEnforcementAndRules()` 경로로 전체 정책 재계산이 발생한다.

- `Repository.mutex lock.RWMutex`가 전체 Repository를 보호하므로, 정책 변경(Write Lock) 중에는 모든 정책 조회(Read Lock)가 차단됨.
- `SelectorCache.UpdateIdentities()`에서 `sc.mutex lock.RWMutex`를 잡고 전체 셀렉터를 순회하며, 이 동안 `DistillPolicy()`의 `WithRLock()` 호출이 대기함.
- `policyCache`(`pkg/policy/distillery.go`)는 아이덴티티별로 `cachedSelectorPolicy`를 캐시하지만, 캐시 키가 `identity.NumericIdentity`뿐이라 같은 아이덴티티라도 다른 네임스페이스의 정책 변경에 영향을 받아 불필요한 캐시 무효화가 발생함.

**영향**: CNP 100개 이상 환경에서 정책 변경 시 수백 ms의 엔드포인트 재생성 지연. `MapChanges` 큐에 변경이 누적되어 BPF 맵 업데이트 지연.

### 2.3 ConnTrack LRU 맵의 대규모 환경 메모리 압박과 Eviction 비효율

**현상**: `cilium_ct4_global`/`cilium_ct_any4_global` LRU Hash 맵은 고정 크기로 운용된다. `bpf/lib/conntrack.h`의 `ct_entry` 구조체는 64바이트 이상이며, TCP와 비-TCP 맵이 별도로 존재하여 기본 설정에서 노드당 ~512MB의 커널 메모리를 사용한다.

- LRU eviction은 **per-CPU 캐시 기반**이므로 CPU 코어 간 불균형 발생. 특정 코어에서 집중적으로 새 연결이 생성되면 해당 코어의 LRU 캐시가 빠르게 가득 차서 유효한 연결이 조기 evict됨.
- `__ct_update_timeout()`에서 `bpf_mono_now()`를 매 패킷마다 호출하고 `WRITE_ONCE(entry->lifetime, ...)`로 타임아웃을 갱신하는데, 이 write 연산이 LRU 맵의 내부 LRU 리스트 업데이트를 트리거하여 캐시 라인 바운싱 발생.
- `__sync_fetch_and_add(&entry->packets, 1)` / `__sync_fetch_and_add(&entry->bytes, ...)` atomic 연산이 모든 패킷에서 수행되어 멀티코어 환경에서 cache contention 증가.

**영향**: 높은 연결 생성률(10K+ conn/sec)에서 CT miss로 인한 정책 재검증 오버헤드, 유효 연결의 조기 eviction으로 서비스 중단 가능.

### 2.4 Hubble 이벤트 파이프라인의 단일 Ring Buffer 병목

**현상**: Hubble의 이벤트 파이프라인은 `BPF_MAP_TYPE_PERF_EVENT_ARRAY`(`cilium_events`)에서 이벤트를 수신한 후, Monitor Agent -> Monitor Consumer -> Hubble Observer -> Ring Buffer -> gRPC Server 순으로 전달된다.

- Ring Buffer(`pkg/hubble/container/ring.go`)는 단일 인스턴스로, `atomic.Pointer` 기반 MVCC를 사용하지만, **write 경로가 단일 고루틴**(Monitor Consumer)에 집중됨.
- `send_trace_notify()`와 `send_drop_notify()`가 모든 관찰 지점(14개)에서 호출되므로, 고트래픽 환경에서 perf event 맵의 CPU별 버퍼 오버플로우가 발생하여 이벤트 손실.
- Hubble Relay가 각 노드의 Agent에 gRPC 연결을 유지하면서 우선순위 큐로 정렬하는데, 노드 수 증가 시 Relay의 메모리 사용량이 선형 증가(노드당 Ring Buffer 크기 * 보유 시간).

**영향**: 고트래픽(100K+ pps) 환경에서 Hubble 이벤트 손실률 5-20%, `cilium-dbg hubble observe` 결과 불완전.

---

## 3. 개선 제안

### 3.1 FQDN DNS 프록시 성능 최적화

**문제 상세**: `pkg/fqdn/dnsproxy/proxy.go`의 `ServeDNS()` 경로에서 매 DNS 요청마다 `perEPAllow` 맵의 4단계 계층 조회와 정규식 매칭이 수행됨. `regexCache`는 `regexp.Regexp` 컴파일만 캐시하고 매칭 결과를 캐시하지 않음.

**근본 원인**: DNS 프록시 설계가 "모든 요청을 개별 검증"하는 방식이며, 동일 엔드포인트의 동일 도메인 반복 쿼리에 대한 빠른 경로(fast path)가 없음. `NameManager`의 256 버킷 잠금은 대규모 클러스터에서 해시 충돌 확률이 높아짐.

**제안 솔루션**:

1. **DNS 매칭 결과 LRU 캐시 도입**: `(endpoint_id, dns_name)` -> `(allowed bool, matched_selector, expiry)` LRU 캐시를 추가. TTL 기반 무효화로 정규식 매칭을 캐시 히트 시 완전히 건너뜀.
   ```go
   type dnsMatchCache struct {
       cache *lru.Cache[dnsMatchKey, dnsMatchResult]  // 엔드포인트별 10K 엔트리
       mu    sync.RWMutex
   }
   ```

2. **`nameLocks` 버킷 수 동적 확장**: 현재 고정 256 버킷을 등록된 FQDN 셀렉터 수에 비례하여 자동 확장(최소 1024, 최대 16384). `runtime.NumCPU() * 256` 등의 휴리스틱 적용.

3. **DNS 프록시 워커 풀 병렬화**: 현재 단일 세마포어 기반 동시성 제어를 CPU 코어 수 기반 워커 풀로 교체. 각 워커가 독립적인 upstream DNS 연결 풀을 유지.

**예상 효과**: DNS 프록시 레이턴시 50-70% 감소(반복 쿼리 시), `nameLocks` 경합 80% 이상 감소.

**구현 난이도**: 중 (LRU 캐시: 2-3주, nameLocks 확장: 1주, 워커 풀: 2주)

---

### 3.2 정책 엔진 잠금 세분화 및 증분 업데이트 확장

**문제 상세**: `Repository.mutex`가 전체 규칙 저장소를 보호하여, `ReplaceByResource()` 실행 중 모든 `GetSelectorPolicy()` 호출이 차단됨. `SelectorCache`의 `UpdateIdentities()` -> `commit()` -> `readTxn.Store()` 경로에서 전체 셀렉터 스냅샷을 교체하므로, 진행 중인 `DistillPolicy()` 호출이 일시적으로 stale 스냅샷을 참조할 수 있음.

**근본 원인**: Repository의 잠금 단위가 너무 크고(전체 규칙 맵), `policyCache`의 무효화 조건이 리비전 비교에만 의존하여 관련 없는 정책 변경에도 캐시가 무효화됨.

**제안 솔루션**:

1. **네임스페이스별 잠금 분리**: `Repository.mutex`를 네임스페이스별 `sync.RWMutex` 맵으로 분리. `rulesByNamespace` 인덱스를 활용하여 특정 네임스페이스 정책 변경 시 해당 네임스페이스 잠금만 획득.
   ```go
   type Repository struct {
       nsMutexes map[string]*sync.RWMutex  // 네임스페이스별 잠금
       globalMu  sync.RWMutex              // 클러스터와이드 규칙용
       // ...
   }
   ```

2. **`policyCache` 키에 영향 범위 추가**: 현재 `identity.NumericIdentity`만 키로 사용하는 것을 `(identity, affectedNamespaces set)` 쌍으로 확장하여, 무관한 네임스페이스 정책 변경 시 캐시 재사용.

3. **Subject 셀렉터 변경의 증분 처리**: 현재 Subject 변경 시 전체 재계산하는 것을 `subjectSelectorCache`에 변경 알림 메커니즘을 추가하여, 영향받는 아이덴티티만 선별적으로 재계산.

**예상 효과**: 멀티 네임스페이스 환경에서 정책 변경 시 잠금 대기 시간 70-90% 감소. 10개 네임스페이스 환경에서 한 네임스페이스 정책 변경이 다른 네임스페이스에 미치는 영향 제거.

**구현 난이도**: 상 (잠금 분리: 4-6주, 캐시 키 확장: 2-3주, Subject 증분 처리: 4주)

---

### 3.3 ConnTrack 맵 메모리 효율 및 Eviction 전략 개선

**문제 상세**: `bpf/lib/conntrack.h`의 `ct_entry`(64+ 바이트)에서 `packets`/`bytes` 카운터가 `__sync_fetch_and_add` atomic으로 매 패킷 갱신됨. LRU Hash 맵의 per-CPU 캐시가 특정 CPU에 집중되면 유효 연결이 조기 evict됨. 또한 `ct_update_timeout()`의 `WRITE_ONCE` 연산이 LRU 리스트 재정렬을 트리거.

**근본 원인**: CT 엔트리의 통계 카운터(packets/bytes)와 연결 상태(lifetime, flags)가 같은 구조체에 있어, 통계 업데이트가 연결 상태 갱신과 동일한 캐시 라인을 오염시킴. LRU 맵의 eviction 정책이 "마지막 접근" 기반이라, 장기 idle 연결과 새 연결의 가치를 구분하지 못함.

**제안 솔루션**:

1. **CT 엔트리 캐시 라인 분리**: `ct_entry` 구조체를 hot path 필드(lifetime, flags, rev_nat_index)와 cold path 필드(packets, bytes, src_sec_id)로 분리. 별도의 per-CPU 배열 맵에 통계 카운터를 이동하여 atomic 연산의 cache contention 감소.
   ```c
   // Hot path (32 bytes, 하나의 캐시 라인)
   struct ct_entry_hot {
       __u32 lifetime;
       __u16 flags;
       __u16 rev_nat_index;
       __u64 backend_id;
       // ...
   };
   // Cold path (별도 per-CPU 맵)
   struct ct_stats {
       __u64 packets;
       __u64 bytes;
   };
   ```

2. **적응형 CT 맵 크기 조정**: Agent에서 CT 맵 사용률을 주기적으로 모니터링(`metricsmap` 활용)하고, 사용률이 80%를 초과하면 경고 + 자동 확장 권고를 제공. BPF 맵 핀닝과 원자적 교체를 활용한 온라인 리사이징 메커니즘 도입.

3. **CT 타임아웃 갱신 빈도 감소**: 매 패킷이 아닌 일정 간격(예: CT_REPORT_INTERVAL 기반)으로만 타임아웃을 갱신. 이미 최근에 갱신된 엔트리는 `bpf_mono_now() - entry->lifetime < threshold` 조건으로 스킵.

**예상 효과**: 멀티코어(16+ 코어) 환경에서 CT 관련 cache contention 40-60% 감소. 메모리 효율 20-30% 개선.

**구현 난이도**: 상 (캐시 라인 분리: 6-8주, 적응형 크기: 3-4주, 갱신 빈도 최적화: 2주)

---

### 3.4 Hubble 이벤트 파이프라인 확장성 개선

**문제 상세**: `cilium_events` perf event 맵의 CPU별 버퍼가 고트래픽에서 오버플로우됨. Monitor Consumer가 단일 고루틴으로 Ring Buffer에 쓰기하여 병목 발생. Hubble Relay의 노드별 gRPC 연결이 선형 스케일링 문제를 야기.

**근본 원인**: BPF perf event array -> userspace 전달 경로가 CPU당 하나의 링 버퍼를 공유하며, userspace 소비 속도가 BPF 생성 속도를 따라가지 못함. Ring Buffer의 단일 writer 설계가 병목.

**제안 솔루션**:

1. **BPF Ring Buffer(`BPF_MAP_TYPE_RINGBUF`) 마이그레이션**: Linux 5.8+에서 사용 가능한 `BPF_MAP_TYPE_RINGBUF`로 전환. Perf event array 대비 메모리 공유가 효율적이고, 단일 링 버퍼를 모든 CPU가 공유하여 버퍼 활용률 향상.
   ```c
   struct {
       __uint(type, BPF_MAP_TYPE_RINGBUF);
       __uint(max_entries, 16 * 1024 * 1024);  // 16MB
   } cilium_events __section_maps_btf;
   ```

2. **이벤트 샘플링 및 집계**: 고트래픽 환경에서 `send_trace_notify()`의 호출 빈도를 동적으로 조절. CT_REPORT_INTERVAL처럼 동일 flow에 대해 일정 시간 내 중복 이벤트를 커널에서 집계.
   ```c
   // 동일 flow에 대해 1초 내 이벤트 중복 제거
   if (now - entry->last_trace_report < TRACE_DEDUP_INTERVAL)
       return;  // 이벤트 생성 스킵
   ```

3. **Hubble Relay 계층적 집계**: 대규모 클러스터(100+ 노드)를 위해 노드 그룹별 중간 집계 레이어 도입. Zone별 Relay 인스턴스가 해당 zone의 노드를 집계하고, 최상위 Relay가 zone 결과를 병합.

**예상 효과**: 이벤트 손실률 5-20% -> 1% 미만. Relay 메모리 사용량 대규모 클러스터에서 50% 감소.

**구현 난이도**: 중-상 (Ring Buffer 마이그레이션: 4-5주, 이벤트 샘플링: 3주, 계층적 Relay: 6-8주)

---

## 4. 우선순위 및 로드맵

### Phase 1 (1-2개월): 즉시 효과, 낮은 위험

| 순위 | 개선 항목 | 예상 소요 | 근거 |
|------|----------|----------|------|
| 1 | FQDN DNS 매칭 결과 LRU 캐시 | 2-3주 | 가장 낮은 구현 비용으로 즉각적인 DNS 레이턴시 개선 |
| 2 | CT 타임아웃 갱신 빈도 최적화 | 2주 | BPF 코드 수정 최소화, 데이터패스 성능 즉시 개선 |
| 3 | Hubble 이벤트 샘플링/집계 | 3주 | 고트래픽 환경 이벤트 손실 문제 해결 |

### Phase 2 (2-4개월): 중간 효과, 중간 위험

| 순위 | 개선 항목 | 예상 소요 | 근거 |
|------|----------|----------|------|
| 4 | `nameLocks` 동적 확장 + DNS 워커 풀 | 3주 | Phase 1의 DNS 최적화를 보완 |
| 5 | BPF Ring Buffer 마이그레이션 | 4-5주 | Hubble 이벤트 안정성의 근본적 개선 |
| 6 | 적응형 CT 맵 크기 모니터링 | 3-4주 | 운영 가시성 향상, 장애 예방 |

### Phase 3 (4-8개월): 높은 효과, 높은 위험

| 순위 | 개선 항목 | 예상 소요 | 근거 |
|------|----------|----------|------|
| 7 | 정책 엔진 네임스페이스별 잠금 분리 | 4-6주 | 대규모 클러스터 정책 성능의 근본적 개선 |
| 8 | CT 엔트리 캐시 라인 분리 | 6-8주 | BPF 데이터 구조 변경으로 높은 위험, 높은 효과 |
| 9 | policyCache 키 확장 + Subject 증분 처리 | 6주 | 정책 엔진 내부 구조 변경 |
| 10 | Hubble Relay 계층적 집계 | 6-8주 | 100+ 노드 클러스터에서만 필요 |

```
Timeline:
Month 1-2:  [====== Phase 1 ======]
Month 2-4:        [========= Phase 2 =========]
Month 4-8:               [============== Phase 3 ==============]
```

---

## 5. 기대 성과

### 정량적 기대 효과

| 지표 | 현재 상태 | Phase 1 후 | Phase 3 완료 후 |
|------|----------|-----------|----------------|
| FQDN DNS 프록시 레이턴시 (반복 쿼리) | 200-500us | 50-100us | 30-80us |
| 정책 변경 시 엔드포인트 재생성 지연 (CNP 100개) | 200-800ms | 200-800ms | 50-200ms |
| CT 관련 cache contention (16코어) | 높음 | 중간 | 낮음 |
| Hubble 이벤트 손실률 (100K pps) | 5-20% | 2-5% | <1% |
| 노드당 CT 맵 메모리 사용량 | ~512MB | ~512MB | ~350MB |

### 정성적 기대 효과

1. **운영 안정성 향상**: CT eviction 개선으로 고부하 환경에서의 예기치 않은 연결 중단 감소. Hubble 이벤트 완정성 향상으로 장애 분석 정확도 증가.

2. **대규모 클러스터 지원 강화**: 정책 엔진 잠금 세분화로 1000+ 엔드포인트, 100+ CNP 환경에서도 안정적인 정책 전파. Relay 계층화로 100+ 노드 클러스터 옵저버빌리티 지원.

3. **커뮤니티 기여 가치**: 특히 Phase 1의 DNS 캐시와 CT 갱신 최적화는 Cilium 전체 사용자에게 즉각적인 성능 개선을 제공하므로 upstream 병합 가능성이 높음. BPF Ring Buffer 마이그레이션은 Cilium의 최소 커널 버전 요구사항과 연계하여 장기 로드맵에 부합.

4. **엔터프라이즈 활용도**: 현대자동차 그룹의 대규모 Kubernetes 환경에서 Cilium 도입 시 예상되는 성능 병목을 사전에 해소하여, 프로덕션 환경에서의 안정성 확보.

---

*작성일: 2026-03-13*
*분석 대상: Cilium 소스코드 교육 자료 (01-architecture, 07-ebpf-datapath, 09-loadbalancing, 10-policy-engine, 13-observability, 14-ipam, 22-fqdn-policy)*
