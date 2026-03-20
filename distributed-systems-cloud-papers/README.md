# 분산시스템 & 클라우드 컴퓨팅 논문 종합 아카이브

분산시스템과 클라우드 컴퓨팅 분야의 핵심 논문 **약 300편 이상**을 8개 카테고리로 체계적으로 정리한 종합 리서치 자료입니다.

---

## 문서 구성

| # | 파일 | 내용 | 논문 수 |
|---|---|---|---|
| 01 | [01_분산컴퓨팅_기초이론.md](01_분산컴퓨팅_기초이론.md) | 시간/순서, 일관성 모델, 불가능성 정리, 장애 허용, CRDT, DHT, 분산 처리 | ~46편 |
| 02 | [02_합의알고리즘_분산조정.md](02_합의알고리즘_분산조정.md) | Paxos, Raft, PBFT, BFT, SMR, ZooKeeper, 원자적 브로드캐스트 | ~40편 |
| 03 | [03_분산파일시스템_객체스토리지.md](03_분산파일시스템_객체스토리지.md) | GFS, HDFS, Ceph, S3, 이레이저 코딩, 데이터 레이크, 스트리밍 스토리지 | ~27편 |
| 04 | [04_가상화_컨테이너_서버리스.md](04_가상화_컨테이너_서버리스.md) | Xen, KVM, Firecracker, Borg, K8s, 유니커널, FaaS, 콜드 스타트 | ~35편 |
| 05 | [05_네트워킹_서비스메시_eBPF.md](05_네트워킹_서비스메시_eBPF.md) | SDN, 데이터센터 네트워킹, eBPF/XDP, 서비스 메시, SmartNIC, RDMA | ~33편 |
| 06 | [06_보안_옵저버빌리티_카오스엔지니어링.md](06_보안_옵저버빌리티_카오스엔지니어링.md) | TEE, 제로트러스트, 컨테이너 보안, 분산 트레이싱, AIOps, SRE, 카오스 | ~42편 |
| 07 | [07_분산ML_엣지_그린_양자.md](07_분산ML_엣지_그린_양자.md) | 분산 학습, 연합학습, MoE, 엣지/포그 컴퓨팅, 그린 클라우드, 양자 | ~45편 |
| 08 | [08_분산스토리지_분산DB.md](08_분산스토리지_분산DB.md) | Dynamo, Spanner, CockroachDB, TiDB, Aurora, 벡터DB, 캐시, HTAP | ~36편 |

---

## 카테고리별 세부 분류

### 01. 분산컴퓨팅 기초이론 (~46편)

| 하위 분류 | 대표 논문 |
|---|---|
| 시간, 순서, 논리적 시계 | Lamport Clocks (1978), Vector Clocks, Chandy-Lamport Snapshots |
| 일관성 모델 | Sequential Consistency, Linearizability (Herlihy & Wing), Eventual Consistency |
| 불가능성 정리 | FLP (1985), Two Generals, CAP Theorem, Harvest & Yield |
| 장애 허용 | Byzantine Generals (1982), PBFT, Failure Detectors, Wait-Free Sync |
| 원자적 커밋 & 분산 트랜잭션 | 2PC, 3PC, Sagas |
| 복제 & 쿼럼 | Weighted Voting, Bayou |
| 해싱, DHT, P2P | Consistent Hashing, Chord, Kademlia, Pastry, CAN |
| 가십 프로토콜 | Epidemic Algorithms |
| CRDT | Shapiro et al. (2011) |
| 분산 데이터 처리 | MapReduce, Spark RDD, Flink |
| 형식 검증 | TLA+ (Lamport) |
| CALM 정리 | Hellerstein CALM |

### 02. 합의 알고리즘 & 분산 조정 (~40편)

| 하위 분류 | 대표 논문 |
|---|---|
| 이론적 기초 | Lamport Clocks, Byzantine Generals, FLP, DLS, Failure Detectors |
| CFT 합의 | Paxos (원본/Simple/Live/Moderately Complex), VR, Raft, EPaxos, Flexible Paxos |
| BFT 합의 | PBFT, HotStuff, HotStuff-2, Tendermint, Nakamoto, Avalanche |
| 상태 기계 복제 | Schneider SMR Tutorial, Atlas |
| 분산 조정 서비스 | Chubby, ZooKeeper, Zab |
| 원자적 브로드캐스트 | Defago-Schiper-Urban Survey |
| 리더 선출 | Gallager-Humblet-Spira |
| 최신 연구 (2022-2025) | BFT 50년 서베이, Frosty, Fast-HotStuff |

### 03. 분산 파일시스템 & 객체 스토리지 (~27편)

| 하위 분류 | 대표 논문 |
|---|---|
| 분산 파일시스템 | GFS, HDFS, Ceph, RADOS, BlueStore, Tectonic, Lustre |
| 오브젝트/Blob 스토리지 | Azure Storage, Haystack, f4, Ambry |
| 이레이저 코딩 | Azure LRC, Erasure Coding Survey |
| 콘텐츠 주소 & 중복 제거 | Venti, IPFS, Dedup Survey |
| 데이터 레이크/레이크하우스 | Delta Lake, Lakehouse |
| 스트리밍 스토리지 | Kafka, Virtual Log-Structured Storage |
| LSM 트리 | O'Neil LSM-Tree, LSM Survey |
| 아카이브 스토리지 | Pelican |

### 04. 가상화, 컨테이너 & 서버리스 (~35편)

| 하위 분류 | 대표 논문 |
|---|---|
| 가상화 | Xen (2003), KVM (2007), GPU 가상화 |
| VM 마이그레이션 | Live Migration, Post-copy |
| 컨테이너 | Docker, CNTR, Slacker |
| 오케스트레이션 | Borg, Omega, Mesos, K8s 진화 |
| 컨테이너 보안/런타임 | gVisor, Kata, RunC 비교 |
| 마이크로VM | Firecracker, LightVM |
| 유니커널 | MirageOS, Unikraft (Best Paper), OSv |
| 서버리스 콜드 스타트 | SOCK, Catalyzer, FaaSLight |
| FaaS | Serverless in the Wild, SAND |
| Knative | 하이브리드 오토스케일링 |

### 05. 네트워킹, 서비스 메시 & eBPF (~33편)

| 하위 분류 | 대표 논문 |
|---|---|
| SDN | OpenFlow (2008), SDN Comprehensive Survey |
| 데이터센터 아키텍처 | Jupiter Rising (Google), VL2 (MS), Andromeda |
| 로드 밸런싱 | Maglev (Google), Ananta (MS) |
| TCP 혼잡 제어 | DCTCP, pFabric, HPCC |
| eBPF/XDP | XDP CoNEXT, eBPF Survey |
| 서비스 메시 | mTLS 벤치마크, 서버리스 사이드카 |
| P4/프로그래머블 스위치 | P4 (2014), P4 Exhaustive Survey |
| RDMA | RoCEv2 at Scale (MS), Hyperscale Issues |
| SmartNIC/DPU | Azure AccelNet, SmartNIC Survey, Off-path SmartNIC |
| 가상 스위칭 | Open vSwitch (Best Paper) |
| 마이크로초 네트워킹 | Demikernel, Shenango, Caladan |
| 제로 트러스트 네트워킹 | BeyondCorp (Google) |

### 06. 보안, 옵저버빌리티 & 카오스 엔지니어링 (~42편)

| 하위 분류 | 대표 논문 |
|---|---|
| 클라우드 보안 | 클라우드 보안 서베이, 컨테이너 보안 |
| 기밀 컴퓨팅/TEE | SGX Haven, SCONE, Intel TDX, AMD SEV |
| 사이드 채널 공격 | Spectre, Meltdown |
| 제로 트러스트 | NIST ZTA, BeyondCorp |
| 공급망 보안 | SBOM, in-toto, Sigstore, SLSA |
| 런타임 보안 | Falco, eBPF 기반 |
| 분산 트레이싱 | Dapper, Jaeger, OpenTelemetry |
| 로그 분석/이상 탐지 | DeepLog, LogAnomaly, LLM 기반 탐지 |
| 카오스 엔지니어링 | Chaos Monkey, 카오스 프레임워크 |
| SRE/인시던트 관리 | Google SRE, AIOps 서베이 |
| 클라우드 포렌식 | 포렌식 프레임워크 |
| 분산 디버깅/성능 | X-Trace, Pip |

### 07. 분산 ML, 엣지, 그린, 양자 (~45편)

| 하위 분류 | 대표 논문 |
|---|---|
| 분산 ML 서베이 | Distributed ML Survey, LLM Training Survey |
| Parameter Server | PS (Li et al. 2014), Communication-Efficient PS |
| 데이터 병렬화 | Horovod, PyTorch DDP |
| 모델/파이프라인 병렬화 | GPipe, PipeDream, Megatron-LM |
| 메모리 최적화 | ZeRO, DeepSpeed |
| 자동 병렬화 | Alpa, FlexFlow |
| MoE | GShard, Switch Transformer |
| 분산 추론 | Orca, vLLM, FlexGen |
| 연합학습 | FedAvg, FedProx, SCAFFOLD |
| 엣지 컴퓨팅 | Cloudlets, MEC 서베이, 엣지 AI |
| 포그 컴퓨팅 | Bonomi Fog Framework |
| 그린 컴퓨팅 | 탄소 인식 스케줄링, PUE 최적화 |
| 양자 클라우드 | QCaaS, 하이브리드 양자-클래식 |
| GPU 스케줄링 | Gandiva, Tiresias |
| MLOps | MLflow, TFX |
| 분산 강화학습 | IMPALA, Ape-X |

### 08. 분산 스토리지 & 분산 DB (~36편)

| 하위 분류 | 대표 논문 |
|---|---|
| 분산 키-값 저장소 | Dynamo, DynamoDB, FoundationDB |
| NoSQL | Bigtable, Cassandra |
| NewSQL/분산 SQL | Spanner, F1, Calvin, CockroachDB, Megastore |
| 클라우드 네이티브 DB | Aurora, Socrates, PolarFS, PolarDB |
| LSM-Tree & 엔진 | LSM-Tree, WiscKey, MyRocks |
| 분산 트랜잭션 | Percolator |
| HTAP | TiDB, F1 Lightning, PolarDB-IMCI |
| 분산 캐시 | Memcache@Facebook, TAO |
| 그래프 처리 | Pregel |
| 벡터 DB | HNSW, Milvus, FAISS |
| 시계열 DB | Gorilla |

---

## 역사적 타임라인

```
1978  Lamport Clocks - 분산 시스템의 논리적 시간 개념 정립
1980  Two Generals Problem
1982  Byzantine Generals Problem - 비잔틴 장애 허용의 시작
1983  Gallager-Humblet-Spira 리더 선출
1984  End-to-End Arguments (Saltzer)
1985  FLP Impossibility - 비동기 합의의 불가능성 증명
1986  2PC, 3PC 원자적 커밋
1988  Viewstamped Replication
1990  Schneider SMR Tutorial
1991  Wait-Free Synchronization (Herlihy)
1993  Epidemic Algorithms (가십 프로토콜)
1996  LSM-Tree (O'Neil), Failure Detectors (Chandra-Toueg)
1997  Consistent Hashing (Karger)
1998  Paxos (Lamport)
1999  PBFT, Sequential Consistency
2000  CAP Conjecture (Brewer)
2001  Paxos Made Simple, Chord DHT
2002  Kademlia, Pastry, CAN, VMware ESX Memory
2003  GFS, Xen, Bayou
2004  MapReduce
2005  Venti, Live Migration
2006  Bigtable, Chubby
2007  Dynamo, KVM, Paxos Made Live
2008  OpenFlow, Nakamoto Consensus (Bitcoin)
2009  VL2, Berkeley View on Cloud
2010  ZooKeeper, Dapper, DCTCP, Spark RDD
2011  Zab, Mesos, CRDT
2012  Spanner, Linearizability (재발견), Sagas (재조명)
2013  Omega, EPaxos, pFabric, Ananta
2014  Raft, P4, Docker, Parameter Server, BeyondCorp
2015  Borg, Jupiter Rising, Open vSwitch, HDFS
2016  Borg/Omega/K8s, Flexible Paxos, Maglev, CockroachDB
2017  Slacker, LightVM
2018  Firecracker, Azure AccelNet, XDP, Andromeda, CNTR, Tendermint
2019  HotStuff, HPCC, Shenango, DeepLog
2020  ZeRO, CockroachDB SIGMOD, TiDB, Caladan, SCAFFOLD, FedAvg, Avalanche
2021  Megatron-LM, Unikraft (Best Paper), Demikernel, Flink
2022  Alpa, OceanBase, Delta Lake, GShard
2023  FlexGen, vLLM, HotStuff-2, Off-path SmartNIC, Switch Transformer
2024  eBPF Survey, SmartNIC Survey, 기밀컴퓨팅 Survey, AIOps Survey, HTAP Survey
2025  Agentic AIOps, LLM Training Survey, 카오스엔지니어링 Fintech, 양자클라우드
```

---

## 필독 논문 Top 30 (입문자 추천 순서)

### Phase 1: 기초 이론 (먼저 읽기)
1. **Lamport Clocks** (1978) - 분산 시스템의 시간 개념
2. **Byzantine Generals Problem** (1982) - 장애 허용의 근본 문제
3. **FLP Impossibility** (1985) - 비동기 합의의 한계
4. **CAP Theorem** (2002) - 분산 시스템 3가지 트레이드오프
5. **End-to-End Arguments** (1984) - 시스템 설계 원칙
6. **Linearizability** (1990) - 일관성 모델의 기준

### Phase 2: 합의 & 복제
7. **Paxos Made Simple** (2001) - 합의 알고리즘의 기본
8. **Raft** (2014) - 이해하기 쉬운 합의 알고리즘
9. **PBFT** (1999) - 비잔틴 장애 허용
10. **Consistent Hashing** (1997) - 분산 해싱의 핵심

### Phase 3: 분산 스토리지 & DB
11. **GFS** (2003) - 대규모 분산 파일 시스템
12. **MapReduce** (2004) - 분산 데이터 처리
13. **Bigtable** (2006) - 분산 구조화 스토리지
14. **Dynamo** (2007) - 고가용성 키-값 저장소
15. **Spanner** (2012) - 글로벌 분산 데이터베이스
16. **Spark RDD** (2012) - 인메모리 분산 컴퓨팅
17. **Kafka** (2011) - 분산 스트리밍 플랫폼

### Phase 4: 클라우드 인프라
18. **Xen** (2003) - 가상화의 시작
19. **Borg** (2015) - 대규모 클러스터 관리
20. **Borg, Omega, and Kubernetes** (2016) - 컨테이너 오케스트레이션 진화
21. **Firecracker** (2020) - 경량 마이크로VM
22. **OpenFlow** (2008) - SDN의 시작
23. **Dapper** (2010) - 분산 추적 시스템

### Phase 5: 현대 시스템
24. **CockroachDB** (2020) - 클라우드 네이티브 분산 SQL
25. **ZeRO** (2020) - 대규모 모델 학습
26. **Megatron-LM** (2021) - 3D 병렬화
27. **vLLM** (2023) - 효율적 LLM 추론
28. **Unikraft** (2021) - 고성능 유니커널
29. **BeyondCorp** (2014) - 제로 트러스트 아키텍처
30. **CRDT** (2011) - 충돌 없는 복제 데이터 타입

---

## 주요 키워드 인덱스

| 키워드 | 관련 문서 |
|---|---|
| 합의 알고리즘 (Consensus) | 01, 02 |
| 분산 데이터베이스 | 03, 08 |
| 컨테이너/쿠버네티스 | 04 |
| 서버리스/FaaS | 04 |
| SDN/네트워킹 | 05 |
| eBPF/서비스 메시 | 05 |
| 보안/TEE | 06 |
| 옵저버빌리티/트레이싱 | 06 |
| 카오스 엔지니어링 | 06 |
| 분산 ML/LLM 학습 | 07 |
| 연합학습 | 07 |
| 엣지/포그 컴퓨팅 | 07 |
| 그린 컴퓨팅 | 07 |
| 양자 클라우드 | 07 |
| 장애 허용 | 01, 02 |
| 일관성 모델 | 01 |
| CRDT | 01 |
| DHT/P2P | 01 |
| 벡터 DB | 08 |
| HTAP | 08 |

---

## 추가 리소스

- [Distributed Systems Reading List (dancres)](https://dancres.github.io/Pages/)
- [Papers We Love - Distributed Systems](https://github.com/papers-we-love/papers-we-love/blob/main/distributed_systems/README.md)
- [Distributed Consensus Reading List (Heidi Howard)](https://github.com/heidihoward/distributed-consensus-reading-list)
- [Foundational Distributed Systems Papers (Murat Demirbas)](http://muratbuffalo.blogspot.com/2021/02/foundational-distributed-systems-papers.html)
- [The Paper Trail - Distributed Systems Theory](https://www.the-paper-trail.org/post/2014-08-09-distributed-systems-theory-for-the-distributed-systems-engineer/)
- [Leslie Lamport's Writings](https://lamport.azurewebsites.net/pubs/pubs.html)
- [eBPF Research Papers Collection](https://pchaigno.github.io/bpf/2025/01/07/research-papers-bpf.html)

---

> 마지막 업데이트: 2026-03-20
