# 분산 시스템 이론 논문 요약 + 링크 (44편)

---

## 1. 합의 프로토콜 (Consensus)

### 1-1. The Part-Time Parliament (Paxos)
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport |
| **학회/연도** | ACM TOCS, 1998 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/279227.279229) · [ACM DL](https://dl.acm.org/doi/10.1145/279227.279229) · [PDF (Lamport)](https://lamport.azurewebsites.net/pubs/lamport-paxos.pdf) |

**요약**: 분산 합의의 표준 알고리즘. 비동기 시스템에서 장애가 발생해도 안전성(safety) 보장. 사실상 모든 현대 분산 시스템의 합의 메커니즘의 원형.

### 1-2. Paxos Made Simple
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport |
| **학회/연도** | ACM SIGACT News, 2001 |
| **링크** | [PDF (Lamport)](https://lamport.azurewebsites.net/pubs/paxos-simple.pdf) |

**요약**: 원본 Paxos 논문이 난해하다는 비판에 대응하여 알고리즘을 평이하게 재설명. 2페이지로 핵심 알고리즘을 기술.

### 1-3. Paxos Made Live: An Engineering Perspective
| 항목 | 내용 |
|------|------|
| **저자** | T. D. Chandra, R. Griesemer, J. Redstone |
| **학회/연도** | PODC 2007 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1145/1281100.1281103) · [ACM DL](https://dl.acm.org/doi/10.1145/1281100.1281103) |

**요약**: Google Chubby 잠금 서비스에 Paxos를 실제 구현한 경험. 이론과 실무 사이의 격차(마스터 리스, 스냅샷, 멤버십 변경 등)를 상세히 기술.

### 1-4. Fast Paxos
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport |
| **학회/연도** | Distributed Computing, 2006 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/s00446-006-0005-x) |

**요약**: 클라이언트가 리더를 거치지 않고 직접 acceptor에게 제안하여 지연을 줄이는 Paxos 변형. 2 message delay로 합의 가능(충돌 없을 때).

### 1-5. Flexible Paxos: Quorum Intersection Revisited
| 항목 | 내용 |
|------|------|
| **저자** | H. Howard, D. Malkhi, A. Spiegelman |
| **학회/연도** | OPODIS 2016 |
| **링크** | [DOI](https://doi.org/10.4230/LIPIcs.OPODIS.2016.25) · [arXiv](https://arxiv.org/abs/1608.06696) |

**요약**: Paxos의 Phase 1과 Phase 2 쿼럼이 반드시 다수결일 필요 없음을 증명. Phase 1 쿼럼과 Phase 2 쿼럼의 교집합만 보장되면 됨. 쿼럼 크기를 유연하게 조절 가능.

### 1-6. Viewstamped Replication: A New Primary Copy Method
| 항목 | 내용 |
|------|------|
| **저자** | B. M. Oki, B. Liskov |
| **학회/연도** | PODC 1988 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1145/62546.62549) · [ACM DL](https://dl.acm.org/doi/10.1145/62546.62549) |

**요약**: 뷰 번호(view number) 기반 복제 프로토콜. Paxos와 독립적으로 개발된 합의 알고리즘으로, 리더 교체(view change) 메커니즘이 명확.

### 1-7. Viewstamped Replication Revisited
| 항목 | 내용 |
|------|------|
| **저자** | B. Liskov, J. Cowling |
| **학회/연도** | MIT TR-2012-021, 2012 |
| **링크** | [PDF (MIT)](https://pmg.csail.mit.edu/papers/vr-revisited.pdf) |

**요약**: 원본 VR 프로토콜을 현대적으로 재정리하고, 재구성(reconfiguration), 상태 전송(state transfer) 등을 추가.

### 1-8. In Search of an Understandable Consensus Algorithm (Raft)
| 항목 | 내용 |
|------|------|
| **저자** | D. Ongaro, J. Ousterhout |
| **학회/연도** | USENIX ATC 2014 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/atc14/technical-sessions/presentation/ongaro) · [PDF](https://raft.github.io/raft.pdf) · [시각화](https://raft.github.io/) |

**요약**: Paxos와 동등한 안전성을 보장하면서 이해가 쉬운 합의 알고리즘. Leader election, log replication, safety로 분해. etcd, CockroachDB, TiKV 등에서 채택.

### 1-9. Zab: High-performance broadcast for primary-backup systems
| 항목 | 내용 |
|------|------|
| **저자** | F. P. Junqueira, B. C. Reed, M. Serafini |
| **학회/연도** | DSN 2011 |
| **링크** | [DOI](https://doi.org/10.1109/DSN.2011.5958223) · [IEEE](https://ieeexplore.ieee.org/document/5958223) |

**요약**: Apache ZooKeeper의 합의 프로토콜. Primary-backup 모델에 최적화된 원자적 브로드캐스트. Paxos 대비 리더가 안정적일 때 더 효율적.

### 1-10. There Is More Consensus in Egalitarian Parliaments (EPaxos)
| 항목 | 내용 |
|------|------|
| **저자** | I. Moraru, D. G. Andersen, M. Kaminsky |
| **학회/연도** | SOSP 2013 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/2517349.2517350) · [ACM DL](https://dl.acm.org/doi/10.1145/2517349.2517350) |

**요약**: 리더 없는(leaderless) 합의 프로토콜. 충돌하지 않는 명령은 1 RTT로 커밋, 충돌 시에만 2 RTT. 지리적 분산 환경에서 Multi-Paxos 대비 낮은 지연.

### 1-11. Mencius: Building Efficient Replicated State Machines for WANs
| 항목 | 내용 |
|------|------|
| **저자** | Y. Mao, F. P. Junqueira, K. Marzullo |
| **학회/연도** | OSDI 2008 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi-08/mencius-building-efficient-replicated-state-machines-wans) |

**요약**: Multi-Paxos를 WAN에 최적화. 리더 역할을 라운드 로빈으로 분산하여 단일 리더 병목 해소. 모든 사이트가 균등하게 쓰기를 제안.

---

## 2. 시간과 순서 (Time & Ordering)

### 2-1. Time, Clocks, and the Ordering of Events in a Distributed System
→ [02_foundational.md#2-1](02_foundational.md) 참조

### 2-2. Vector Clocks (Fidge 1988 / Mattern 1989)
→ [02_foundational.md#2-2, #2-3](02_foundational.md) 참조

### 2-3. Logical Physical Clocks and Consistent Snapshots in Globally Distributed Databases (HLC)
| 항목 | 내용 |
|------|------|
| **저자** | S. S. Kulkarni, M. Demirbas, D. Madappa, B. Avva, M. Leone |
| **학회/연도** | OPODIS 2014 |
| **링크** | [DOI](https://doi.org/10.1007/978-3-319-14472-6_2) · [PDF](https://cse.buffalo.edu/tech-reports/2014-04.pdf) |

**요약**: 논리적 시계와 물리적 시계를 결합한 Hybrid Logical Clock. NTP 수준의 물리적 시계와 Lamport 시계의 장점 결합. CockroachDB에서 채택. Spanner의 TrueTime이 특수 하드웨어를 필요로 하는 문제를 소프트웨어로 해결.

### 2-4. Causal Memory: Definitions, Implementation, and Programming
| 항목 | 내용 |
|------|------|
| **저자** | M. Ahamad, G. Neiger, J. E. Burns, P. Kohli, P. W. Hutto |
| **학회/연도** | Distributed Computing, 1995 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/BF01784241) |

**요약**: 인과적 메모리(causal memory)의 형식적 정의와 구현. Sequential consistency보다 약하지만 eventual consistency보다 강한 일관성 모델.

---

## 3. 불가능성 결과 (Impossibility Results)

### 3-1. FLP Impossibility
→ [02_foundational.md#14-3](02_foundational.md) 참조

### 3-2. CAP Theorem
→ [02_foundational.md#5-1 ~ #5-6](02_foundational.md) 참조

### 3-3. PACELC
→ [02_foundational.md#5-5](02_foundational.md) 참조

### 3-4. Consistency, Availability, and Convergence
| 항목 | 내용 |
|------|------|
| **저자** | P. Mahajan, L. Alvisi, M. Dahlin |
| **학회/연도** | UT Austin TR-11-22, 2011 |
| **링크** | [PDF (UT Austin)](https://www.cs.utexas.edu/~dahlin/papers/cac-tr.pdf) |

**요약**: CAP을 확장하여 수렴(convergence)까지 고려. Highly Available Transactions(HAT)의 이론적 기초. 어떤 트랜잭션 보장이 가용성과 양립 가능한지 분석.

---

## 4. 일관성 모델 (Consistency Models)

### 4-1. Linearizability: A Correctness Condition for Concurrent Objects
| 항목 | 내용 |
|------|------|
| **저자** | M. P. Herlihy, J. M. Wing |
| **학회/연도** | ACM TOPLAS, 1990 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/78969.78972) · [ACM DL](https://dl.acm.org/doi/10.1145/78969.78972) |

**요약**: 동시성 객체의 정확성 조건으로 선형화 가능성(linearizability) 정의. 모든 연산이 호출과 응답 사이의 어느 한 시점에 원자적으로 발생한 것처럼 보이는 가장 강한 일관성 모델. Spanner의 external consistency는 linearizability를 분산 환경으로 확장.

### 4-2. How to Make a Multiprocessor Computer That Correctly Executes Multiprocess Programs (Sequential Consistency)
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport |
| **학회/연도** | IEEE Trans. Computers, 1979 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1109/TC.1979.1675439) · [PDF (Lamport)](https://lamport.azurewebsites.net/pubs/multi.pdf) |

**요약**: 순차적 일관성(sequential consistency) 정의. 모든 프로세서의 연산이 어떤 순차적 순서로 실행된 것처럼 보이고, 각 프로세서 내의 프로그램 순서가 유지. Linearizability보다 약한 모델.

### 4-3. Session Guarantees for Weakly Consistent Replicated Data
→ [02_foundational.md#10-2](02_foundational.md) 참조

### 4-4. Eventually Consistent
→ [02_foundational.md#10-3](02_foundational.md) 참조

### 4-5. Conflict-Free Replicated Data Types (CRDTs)
→ [02_foundational.md#8-4](02_foundational.md) 참조

---

## 5. 복제 (Replication)

### 5-1. The Primary-Backup Approach
| 항목 | 내용 |
|------|------|
| **저자** | N. Budhiraja, K. Marzullo, F. B. Schneider, S. Toueg |
| **학회/연도** | Distributed Systems (2nd Ed.), ACM Press, 1993 |
| **링크** | [PDF (Cornell)](https://www.cs.cornell.edu/fbs/publications/SMSurvey.pdf) |

**요약**: Primary-backup 복제의 체계적 정리. Primary가 모든 쓰기를 처리하고 backup에 전파. 가장 단순한 복제 전략이지만 primary 장애 시 복구가 핵심 과제.

### 5-2. Chain Replication
→ [02_foundational.md#8-2](02_foundational.md) 참조

### 5-3. Object Storage on CRAQ: High-Throughput Chain Replication for Read-Mostly Workloads
| 항목 | 내용 |
|------|------|
| **저자** | J. Terrace, M. J. Freedman |
| **학회/연도** | USENIX ATC 2009 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/usenix-09/object-storage-craq-high-throughput-chain-replication-read-mostly-workloads) |

**요약**: Chain replication에서 중간 노드도 읽기를 처리할 수 있게 확장. 읽기 처리량을 체인 길이에 비례하여 증가. 최신 버전은 tail에서 확인.

### 5-4. Optimistic Replication
→ [02_foundational.md#8-3](02_foundational.md) 참조

### 5-5. Lazy Replication
→ [02_foundational.md#10-4](02_foundational.md) 참조

---

## 6. 분산 스토리지 기반 (Storage Foundations)

### 6-1. The Log-Structured Merge-Tree (LSM-Tree)
| 항목 | 내용 |
|------|------|
| **저자** | P. O'Neil, E. Cheng, D. Gawlick, E. O'Neil |
| **학회/연도** | Acta Informatica, 1996 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/s002360050048) |

**요약**: 쓰기 최적화 자료구조. 메모리 내 버퍼(memtable)에 쓰기를 모은 후 디스크에 순차적으로 플러시. Bigtable, Cassandra, LevelDB, RocksDB, DynamoDB의 스토리지 엔진 기반.

### 6-2. The Design and Implementation of a Log-Structured File System
| 항목 | 내용 |
|------|------|
| **저자** | M. Rosenblum, J. K. Ousterhout |
| **학회/연도** | SOSP 1991 / ACM TOCS 1992 |
| **BK21** | IF **4** (SOSP) / **SCIE** (TOCS) |
| **링크** | [DOI](https://doi.org/10.1145/146941.146943) · [ACM DL](https://dl.acm.org/doi/10.1145/146941.146943) |

**요약**: 모든 쓰기를 로그에 순차적으로 기록하는 파일 시스템. 디스크 대역폭을 최대로 활용. LSM-Tree의 직접적 영감.

### 6-3. Organization and Maintenance of Large Ordered Indexes (B-Tree)
| 항목 | 내용 |
|------|------|
| **저자** | R. Bayer, E. M. McCreight |
| **학회/연도** | Acta Informatica, 1972 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/BF00288683) |

**요약**: B-트리 자료구조 최초 제안. 디스크 기반 검색에 최적화된 균형 탐색 트리. LSM-Tree와 대비되는 읽기 최적화 자료구조.

### 6-4. The Ubiquitous B-Tree
| 항목 | 내용 |
|------|------|
| **저자** | D. Comer |
| **학회/연도** | ACM Computing Surveys, 1979 |
| **SCIE** | JCR IF ~23.8 |
| **링크** | [DOI](https://doi.org/10.1145/356770.356776) · [ACM DL](https://dl.acm.org/doi/10.1145/356770.356776) |

**요약**: B-트리 변형(B+, B*)의 포괄적 서베이. 데이터베이스 인덱싱의 표준 참조.

### 6-5. Space/Time Trade-offs in Hash Coding with Allowable Errors (Bloom Filter)
| 항목 | 내용 |
|------|------|
| **저자** | B. H. Bloom |
| **학회/연도** | CACM, 1970 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/362686.362692) · [ACM DL](https://dl.acm.org/doi/10.1145/362686.362692) |

**요약**: 확률적 멤버십 테스트 자료구조. 거짓 양성(false positive)은 허용하지만 거짓 음성(false negative)은 없음. LSM-Tree에서 불필요한 디스크 읽기를 줄이는 데 필수적으로 사용.

---

## 7. 장애 감지 (Failure Detection)

### 7-1. Unreliable Failure Detectors
→ [02_foundational.md#3-4](02_foundational.md) 참조

### 7-2. SWIM
→ [02_foundational.md#3-2](02_foundational.md) 참조

### 7-3. Phi Accrual Failure Detector
→ [02_foundational.md#3-3](02_foundational.md) 참조

---

## 8. 분산 트랜잭션 (Distributed Transactions)

### 8-1. Notes on Data Base Operating Systems (2PC)
| 항목 | 내용 |
|------|------|
| **저자** | J. Gray |
| **학회/연도** | Springer LNCS, 1978 |
| **링크** | [DOI](https://doi.org/10.1007/3-540-08755-9_9) |

**요약**: 2-Phase Commit 프로토콜 제안. Coordinator가 prepare → commit/abort로 분산 트랜잭션의 원자성 보장. 블로킹 프로토콜이라는 한계.

### 8-2. Nonblocking Commit Protocols (3PC)
| 항목 | 내용 |
|------|------|
| **저자** | D. Skeen |
| **학회/연도** | SIGMOD 1981 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/582318.582339) · [ACM DL](https://dl.acm.org/doi/10.1145/582318.582339) |

**요약**: 2PC의 블로킹 문제를 해결하는 3-Phase Commit. Pre-commit 단계 추가로 코디네이터 장애 시에도 참여자가 독자적으로 결정 가능. 실무에서는 비용 대비 효과가 낮아 Paxos 기반 접근이 선호됨.

### 8-3. Sagas
| 항목 | 내용 |
|------|------|
| **저자** | H. Garcia-Molina, K. Salem |
| **학회/연도** | SIGMOD 1987 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/38713.38742) · [ACM DL](https://dl.acm.org/doi/10.1145/38713.38742) |

**요약**: 장기 실행 트랜잭션(long-lived transaction)을 하위 트랜잭션의 시퀀스로 분해하고, 실패 시 보상 트랜잭션(compensating transaction)으로 롤백. 마이크로서비스 아키텍처의 분산 트랜잭션 패턴으로 부활.

### 8-4. Large-scale Incremental Processing Using Distributed Transactions and Notifications (Percolator)
| 항목 | 내용 |
|------|------|
| **저자** | D. Peng, F. Dabek |
| **학회/연도** | OSDI 2010 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi10/large-scale-incremental-processing-using-distributed-transactions-and) · [PDF (Google)](https://research.google.com/pubs/archive/36726.pdf) |

**요약**: Google의 웹 인덱스 증분 처리 시스템. Bigtable 위에 Snapshot Isolation 기반 분산 트랜잭션 구현. 2PC + 타임스탬프 오라클(timestamp oracle). TiDB/TiKV가 이 트랜잭션 모델을 직접 채택.

### 8-5. A Critique of ANSI SQL Isolation Levels (Snapshot Isolation)
| 항목 | 내용 |
|------|------|
| **저자** | H. Berenson, P. Bernstein, J. Gray, J. Melton, E. O'Neil, P. O'Neil |
| **학회/연도** | SIGMOD 1995 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/223784.223785) · [ACM DL](https://dl.acm.org/doi/10.1145/223784.223785) |

**요약**: ANSI SQL 격리 수준의 모호성을 비판하고 Snapshot Isolation을 형식적으로 정의. SI는 MVCC 기반으로 읽기가 쓰기를 차단하지 않음. PostgreSQL, Oracle, CockroachDB, TiDB의 기본 격리 수준.
