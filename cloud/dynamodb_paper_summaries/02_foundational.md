# Dynamo 기반 논문 요약 + 링크 (51편)

Dynamo가 직접 참조했거나 설계에 영향을 준 기반 논문 전체 목록.

---

## 1. Consistent Hashing

### 1-1. Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web
| 항목 | 내용 |
|------|------|
| **저자** | D. Karger, E. Lehman, F. T. Leighton, R. Panigrahy, M. Levine, D. Lewin |
| **학회/연도** | STOC 1997 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/258533.258660) · [ACM DL](https://dl.acm.org/doi/10.1145/258533.258660) |

**요약**: 해시 링 기반으로 노드 추가/제거 시 최소 키만 재배치되는 해싱 기법. Dynamo 데이터 파티셔닝의 직접적 기반. Akamai CDN의 기술적 토대이기도 함.

### 1-2. Novel Architectures for P2P Applications: the Continuous-Discrete Approach
| 항목 | 내용 |
|------|------|
| **저자** | M. Naor, U. Wieder |
| **학회/연도** | SPAA 2003 |
| **링크** | [DOI](https://doi.org/10.1145/777412.777417) |

**요약**: Consistent hashing의 부하 균형 문제를 개선하는 연속-이산 접근법 제안. Dynamo의 가상 노드(virtual node) 전략에 이론적 기반 제공.

### 1-3. Simple Efficient Load Balancing Algorithms for Peer-to-Peer Systems
| 항목 | 내용 |
|------|------|
| **저자** | J. Byers, J. Considine, M. Mitzenmacher |
| **학회/연도** | SPAA 2003 |
| **링크** | [DOI](https://doi.org/10.1145/777412.777422) |

**요약**: "Power of two choices" 원리를 P2P 시스템에 적용한 부하 균형 알고리즘. Consistent hashing에서 핫스팟 문제를 해결하는 실용적 기법.

---

## 2. Vector Clocks / Version Tracking

### 2-1. Time, Clocks, and the Ordering of Events in a Distributed System
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport |
| **학회/연도** | CACM 1978 |
| **SCIE** | JCR IF ~11.1 |
| **링크** | [DOI](https://doi.org/10.1145/359545.359563) · [ACM DL](https://dl.acm.org/doi/10.1145/359545.359563) · [Lamport's page](https://lamport.azurewebsites.net/pubs/time-clocks.pdf) |

**요약**: "Happened-before" 관계와 논리적 시계 개념 정의. 분산 시스템 이론의 가장 기본적이고 가장 많이 인용된 논문. Vector Clock의 직접적 선행 연구.

### 2-2. Timestamps in Message-Passing Systems That Preserve the Partial Ordering
| 항목 | 내용 |
|------|------|
| **저자** | C. J. Fidge |
| **학회/연도** | 11th Australian Computer Science Conference, 1988 |
| **링크** | [PDF](https://fileadmin.cs.lth.se/cs/Personal/Amr_Ergawy/dist-algos-papers/4.pdf) |

**요약**: Lamport 시계를 확장하여 동시 이벤트(concurrent events)를 식별할 수 있는 벡터 시계 제안. Mattern과 독립적으로 동시 발명.

### 2-3. Virtual Time and Global States of Distributed Systems
| 항목 | 내용 |
|------|------|
| **저자** | F. Mattern |
| **학회/연도** | Parallel and Distributed Algorithms Workshop, 1989 |
| **링크** | [PDF](https://www.vs.inf.ethz.ch/publ/papers/VirtTimeGlobworlds.pdf) |

**요약**: Fidge와 독립적으로 벡터 시계를 발명. 가상 시간(virtual time) 개념과 분산 시스템의 글로벌 상태 탐지에 벡터 시계를 적용.

### 2-4. Detection of Mutual Inconsistency in Distributed Systems
| 항목 | 내용 |
|------|------|
| **저자** | D. S. Parker, G. J. Popek, G. Rudisin, A. Stoughton, B. J. Walker, E. Walton, J. M. Chow, D. Edwards, S. Kiser, C. Kline |
| **학회/연도** | IEEE Trans. Software Engineering, 1983 |
| **링크** | [DOI](https://doi.org/10.1109/TSE.1983.236733) |

**요약**: Version Vector 개념의 최초 제안. 분산 복제 시스템에서 상호 불일치(mutual inconsistency)를 감지하는 방법. Dynamo의 충돌 감지 메커니즘에 직접적 영향.

### 2-5. Dotted Version Vectors: Logical Clocks for Optimistic Replication
| 항목 | 내용 |
|------|------|
| **저자** | N. Preguiça, C. Baquero, P. S. Almeida, V. Fonte, R. Gonçalves |
| **학회/연도** | arXiv 2010 |
| **링크** | [arXiv](https://arxiv.org/abs/1011.5808) |

**요약**: Dynamo 스타일 시스템에서 기존 vector clock의 문제점(형제 폭발, sibling explosion)을 해결하는 개선된 논리적 시계. Riak이 이 기법을 채택.

### 2-6. Interval Tree Clocks: A Logical Clock for Dynamic Systems
| 항목 | 내용 |
|------|------|
| **저자** | P. S. Almeida, C. Baquero, V. Fonte |
| **학회/연도** | OPODIS 2008 |
| **링크** | [DOI](https://doi.org/10.1007/978-3-540-92221-6_18) |

**요약**: 참여자가 동적으로 변하는 시스템을 위한 논리적 시계. Vector clock은 참여자 수에 비례하여 크기가 증가하지만, ITC는 ID 분할/병합으로 이 문제를 해결.

---

## 3. Gossip Protocols / Failure Detection

### 3-1. Epidemic Algorithms for Replicated Database Maintenance
| 항목 | 내용 |
|------|------|
| **저자** | A. Demers, D. Greene, C. Hauser, W. Irish, J. Larson, S. Shenker, H. Sturgis, D. Swinehart, D. Terry |
| **학회/연도** | PODC 1987 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1145/41840.41841) · [ACM DL](https://dl.acm.org/doi/10.1145/41840.41841) |

**요약**: 전염병 확산 모델을 분산 데이터베이스 복제에 적용. Push, pull, push-pull 세 가지 전파 방식 분석. Dynamo의 멤버십 관리와 anti-entropy 프로세스의 직접적 기반.

### 3-2. SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol
| 항목 | 내용 |
|------|------|
| **저자** | A. Das, I. Gupta, A. Motivala |
| **학회/연도** | DSN 2002 |
| **링크** | [DOI](https://doi.org/10.1109/DSN.2002.1028914) · [PDF (Cornell)](https://www.cs.cornell.edu/projects/Quicksilver/public_pdfs/SWIM.pdf) |

**요약**: O(n) 메시지로 완전한 멤버십 정보를 유지하는 프로토콜. 하트비트 대신 무작위 ping/ping-req 기반 장애 감지. Consul, Serf 등에서 사용. Dynamo의 가십 기반 장애 감지를 개선하는 후속 연구.

### 3-3. The Phi (Φ) Accrual Failure Detector
| 항목 | 내용 |
|------|------|
| **저자** | N. Hayashibara, X. Défago, R. Yared, T. Katayama |
| **학회/연도** | SRDS 2004 |
| **링크** | [DOI](https://doi.org/10.1109/RELDIS.2004.1353004) |

**요약**: 이진(alive/dead) 대신 연속적 의심 수준(phi value)을 출력하는 장애 감지기. 네트워크 지연 변동에 적응적. Cassandra와 Akka에서 채택.

### 3-4. Unreliable Failure Detectors for Reliable Distributed Systems
| 항목 | 내용 |
|------|------|
| **저자** | T. D. Chandra, S. Toueg |
| **학회/연도** | JACM 1996 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/226643.226647) · [ACM DL](https://dl.acm.org/doi/10.1145/226643.226647) |

**요약**: 불완전한 장애 감지기로도 합의 문제를 해결할 수 있음을 증명. FLP 불가능성 결과를 우회하는 이론적 돌파구. 장애 감지기를 완전성(completeness)과 정확성(accuracy)으로 분류.

---

## 4. Distributed Hash Tables

### 4-1. Chord: A Scalable Peer-to-Peer Lookup Service for Internet Applications
| 항목 | 내용 |
|------|------|
| **저자** | I. Stoica, R. Morris, D. Karger, M. F. Kaashoek, H. Balakrishnan |
| **학회/연도** | SIGCOMM 2001 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/383059.383071) · [ACM DL](https://dl.acm.org/doi/10.1145/383059.383071) · [PDF (MIT)](https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf) |

**요약**: Consistent hashing 기반 DHT에서 O(log N) 홉으로 키를 찾는 구조적 오버레이. 핑거 테이블(finger table)로 라우팅을 가속. Dynamo의 키 공간 분할 전략에 직접적 영향.

### 4-2. Chord: A Scalable Peer-to-Peer Lookup Protocol (확장 저널 버전)
| 항목 | 내용 |
|------|------|
| **저자** | I. Stoica et al. |
| **학회/연도** | IEEE/ACM Trans. Networking, 2003 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1109/TNET.2002.808407) |

**요약**: SIGCOMM 2001 버전의 확장. 안정화 프로토콜, 장애 처리, 성능 분석 추가.

### 4-3. Pastry: Scalable, Decentralized Object Location and Routing for Large-Scale Peer-to-Peer Systems
| 항목 | 내용 |
|------|------|
| **저자** | A. Rowstron, P. Druschel |
| **학회/연도** | Middleware 2001 |
| **링크** | [DOI](https://doi.org/10.1007/3-540-45518-3_18) |

**요약**: 접두사 기반(prefix-based) 라우팅 DHT. 네트워크 지역성(network locality)을 고려한 라우팅으로 Chord 대비 낮은 지연.

### 4-4. A Scalable Content-Addressable Network (CAN)
| 항목 | 내용 |
|------|------|
| **저자** | S. Ratnasamy, P. Francis, M. Handley, R. Karp, S. Shenker |
| **학회/연도** | SIGCOMM 2001 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/383059.383072) · [ACM DL](https://dl.acm.org/doi/10.1145/383059.383072) |

**요약**: d차원 토러스 공간에 키를 매핑하는 DHT. O(d·N^(1/d)) 홉 라우팅. 다차원 공간 분할이라는 Chord와 다른 접근법.

### 4-5. Kademlia: A Peer-to-Peer Information System Based on the XOR Metric
| 항목 | 내용 |
|------|------|
| **저자** | P. Maymounkov, D. Mazières |
| **학회/연도** | IPTPS 2002 |
| **링크** | [DOI](https://doi.org/10.1007/3-540-45748-8_5) · [PDF](https://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf) |

**요약**: XOR 거리 메트릭 기반 DHT. 대칭적 거리 함수로 라우팅 테이블 유지가 효율적. BitTorrent, Ethereum에서 사용. O(log N) 룩업.

### 4-6. Tapestry: An Infrastructure for Fault-tolerant Wide-area Location and Routing
| 항목 | 내용 |
|------|------|
| **저자** | B. Y. Zhao, J. Kubiatowicz, A. D. Joseph |
| **학회/연도** | UC Berkeley TR UCB/CSD-01-1141, 2001 |
| **링크** | [PDF (Berkeley)](https://people.eecs.berkeley.edu/~istoica/classes/cs294/04/papers/tapestry_jsac.pdf) |

**요약**: Plaxton 라우팅을 기반으로 한 DHT. 네트워크 지역성을 활용하여 효율적 라우팅. OceanStore 분산 스토리지의 기반.

### 4-7. Accessing Nearby Copies of Replicated Objects in a Distributed Environment
| 항목 | 내용 |
|------|------|
| **저자** | C. G. Plaxton, R. Rajaraman, A. W. Richa |
| **학회/연도** | SPAA 1997 |
| **링크** | [DOI](https://doi.org/10.1145/258492.258523) |

**요약**: 분산 환경에서 가장 가까운 복제 객체를 찾는 이론적 프레임워크. Pastry와 Tapestry의 라우팅 알고리즘의 이론적 기반.

---

## 5. CAP 정리

### 5-1. Harvest, Yield, and Scalable Tolerant Systems
| 항목 | 내용 |
|------|------|
| **저자** | A. Fox, E. A. Brewer |
| **학회/연도** | HotOS-VII, 1999 |
| **링크** | [DOI](https://doi.org/10.1109/HOTOS.1999.798396) · [IEEE](https://ieeexplore.ieee.org/document/798396) |

**요약**: Harvest(데이터 완전성)와 Yield(요청 성공률) 개념으로 가용성-일관성 트레이드오프를 정량화. CAP 정리의 전조.

### 5-2. Towards Robust Distributed Systems (CAP Keynote)
| 항목 | 내용 |
|------|------|
| **저자** | E. A. Brewer |
| **학회/연도** | PODC 2000 Keynote |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [Slides (Berkeley)](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) |

**요약**: Consistency, Availability, Partition Tolerance 세 가지를 동시에 만족할 수 없다는 CAP 추측(conjecture) 최초 제시. Dynamo의 AP 선택의 이론적 근거.

### 5-3. Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services
| 항목 | 내용 |
|------|------|
| **저자** | S. Gilbert, N. Lynch |
| **학회/연도** | ACM SIGACT News, 2002 |
| **링크** | [DOI](https://doi.org/10.1145/564585.564601) · [ACM DL](https://dl.acm.org/doi/10.1145/564585.564601) |

**요약**: Brewer의 CAP 추측을 비동기 네트워크 모델에서 형식적으로 증명. 추측을 정리(theorem)로 격상.

### 5-4. CAP Twelve Years Later: How the "Rules" Have Changed
| 항목 | 내용 |
|------|------|
| **저자** | E. A. Brewer |
| **학회/연도** | IEEE Computer, 2012 |
| **링크** | [DOI](https://doi.org/10.1109/MC.2012.37) · [IEEE](https://ieeexplore.ieee.org/document/6133253) |

**요약**: CAP 정리 12년 후 회고. "2 out of 3" 해석은 오해이며, 파티션은 드물게 발생하고, 파티션 발생 시에만 C/A 트레이드오프가 필요하다고 수정. 보상(compensation)과 복구 전략 강조.

### 5-5. Consistency Tradeoffs in Modern Distributed Database System Design (PACELC)
| 항목 | 내용 |
|------|------|
| **저자** | D. J. Abadi |
| **학회/연도** | IEEE Computer, 2012 |
| **링크** | [DOI](https://doi.org/10.1109/MC.2012.33) · [IEEE](https://ieeexplore.ieee.org/document/6127847) |

**요약**: CAP을 확장한 **PACELC** 모델 제안. Partition 시 A/C 선택 + Else(정상) 시 Latency/Consistency 선택. Dynamo는 PA/EL(파티션 시 가용성, 정상 시 저지연), Spanner는 PC/EC(항상 일관성).

### 5-6. Perspectives on the CAP Theorem
| 항목 | 내용 |
|------|------|
| **저자** | S. Gilbert, N. Lynch |
| **학회/연도** | IEEE Computer, 2012 |
| **링크** | [DOI](https://doi.org/10.1109/MC.2012.55) |

**요약**: CAP 정리의 형식적 의미와 실무적 함의를 재정리. 일관성과 가용성 사이의 연속적 스펙트럼을 강조.

---

## 6. Quorum Systems

### 6-1. Weighted Voting for Replicated Data
| 항목 | 내용 |
|------|------|
| **저자** | D. K. Gifford |
| **학회/연도** | SOSP 1979 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/800215.806583) · [ACM DL](https://dl.acm.org/doi/10.1145/800215.806583) |

**요약**: 복제 데이터에 대한 가중 투표(weighted voting) 프로토콜. W + R > N이면 강한 일관성 보장. Dynamo의 N/R/W 쿼럼 시스템의 직접적 기원.

### 6-2. A Majority Consensus Approach to Concurrency Control for Multiple Copy Databases
| 항목 | 내용 |
|------|------|
| **저자** | R. H. Thomas |
| **학회/연도** | ACM TODS, 1979 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/320071.320076) · [ACM DL](https://dl.acm.org/doi/10.1145/320071.320076) |

**요약**: 다수결 기반 동시성 제어. Thomas Write Rule: 새 쓰기가 기존 쓰기보다 타임스탬프가 크면 직접 덮어쓰기 허용.

### 6-3. A Quorum-Consensus Replication Method for Abstract Data Types
| 항목 | 내용 |
|------|------|
| **저자** | M. P. Herlihy |
| **학회/연도** | ACM TOCS, 1986 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/6306.6308) |

**요약**: 쿼럼 합의를 추상 데이터 타입으로 일반화. 읽기/쓰기 외의 임의 연산에도 적용 가능한 쿼럼 프레임워크.

### 6-4. Probabilistic Quorum Systems
| 항목 | 내용 |
|------|------|
| **저자** | D. Malkhi, M. Reiter, R. Wright |
| **학회/연도** | PODC 1997 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1145/259380.259458) |

**요약**: 확률적 보장으로 쿼럼 크기를 줄이는 기법. O(√N) 크기의 쿼럼으로 높은 확률의 일관성 보장. Dynamo의 sloppy quorum에 이론적 영감.

### 6-5. Byzantine Quorum Systems
| 항목 | 내용 |
|------|------|
| **저자** | D. Malkhi, M. Reiter |
| **학회/연도** | Distributed Computing, 1998 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/s004460050050) |

**요약**: 비잔틴 장애 환경에서의 쿼럼 시스템. 악의적 노드가 있어도 정확한 읽기/쓰기를 보장하는 쿼럼 교차 조건 정의.

---

## 7. Merkle Trees

### 7-1. A Digital Signature Based on a Conventional Encryption Function
| 항목 | 내용 |
|------|------|
| **저자** | R. C. Merkle |
| **학회/연도** | CRYPTO 1987 |
| **링크** | [DOI](https://doi.org/10.1007/3-540-48184-2_32) |

**요약**: 해시 트리(Merkle tree)를 이용한 디지털 서명. 트리의 루트 해시만 비교하면 전체 데이터 일치 여부를 판단 가능. Dynamo의 anti-entropy에서 복제본 간 데이터 불일치를 효율적으로 감지하는 데 사용.

---

## 8. Replication Strategies

### 8-1. The Dangers of Replication and a Solution
| 항목 | 내용 |
|------|------|
| **저자** | J. Gray, P. Helland, P. O'Neil, D. Shasha |
| **학회/연도** | SIGMOD 1996 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/233269.233330) · [ACM DL](https://dl.acm.org/doi/10.1145/233269.233330) |

**요약**: 즉시 복제(eager replication)의 위험성 분석. 노드 수가 증가하면 교착/중단 확률이 급증. 지연 복제(lazy replication) + 마스터 기반 업데이트를 권장. Dynamo가 eager replication을 피하고 eventual consistency를 선택한 이론적 근거.

### 8-2. Chain Replication for Supporting High Throughput and Availability
| 항목 | 내용 |
|------|------|
| **저자** | R. van Renesse, F. B. Schneider |
| **학회/연도** | OSDI 2004 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi-04/chain-replication-supporting-high-throughput-and-availability) · [PDF](https://www.cs.cornell.edu/home/rvr/papers/OSDI04.pdf) |

**요약**: 복제본을 체인으로 연결하여 head에서 쓰기, tail에서 읽기 수행. Strong consistency + 높은 처리량. Dynamo의 쿼럼 기반과는 대조적인 접근법. CRAQ, Azure Storage에서 채택.

### 8-3. Optimistic Replication
| 항목 | 내용 |
|------|------|
| **저자** | Y. Saito, M. Shapiro |
| **학회/연도** | ACM Computing Surveys, 2005 |
| **SCIE** | JCR IF ~23.8 |
| **링크** | [DOI](https://doi.org/10.1145/1057977.1057980) · [ACM DL](https://dl.acm.org/doi/10.1145/1057977.1057980) |

**요약**: 낙관적 복제 기법의 포괄적 서베이. 업데이트를 즉시 전파하지 않고 비동기적으로 복제한 뒤 충돌을 사후 해결. Dynamo의 eventual consistency + 클라이언트 측 충돌 해결의 이론적 프레임워크.

### 8-4. Conflict-free Replicated Data Types (CRDTs)
| 항목 | 내용 |
|------|------|
| **저자** | M. Shapiro, N. Preguiça, C. Baquero, M. Zawirski |
| **학회/연도** | SSS 2011 |
| **링크** | [DOI](https://doi.org/10.1007/978-3-642-24550-3_29) · [PDF (INRIA)](https://hal.inria.fr/inria-00609399v1/document) |

**요약**: 수학적으로 충돌이 발생하지 않는 복제 데이터 타입. 격자(lattice) 기반 병합으로 어떤 순서로 업데이트가 도착해도 최종 상태 수렴 보장. Dynamo의 충돌 해결 문제를 근본적으로 해결하는 접근. Riak 2.0, Redis CRDT에서 채택.

---

## 9. Sloppy Quorum / Hinted Handoff

### 9-1. Sloppy Quorum의 기원
Sloppy quorum은 Dynamo(2007)에서 처음 명명된 개념. Gifford의 quorum voting(1979)을 변형하여, 엄격한 "홈 노드" 대신 선호 목록(preference list)의 상위 N개 건강한 노드에 쓰기를 수행.

### 9-2. Hinted Handoff의 선행 연구
Hinted handoff는 Dynamo 이전에 이메일 시스템(SMTP의 임시 수신 후 전달)과 Bayou(1995)의 세션 전달에서 유사 개념이 존재.

---

## 10. Eventually Consistent Models

### 10-1. Managing Update Conflicts in Bayou, a Weakly Connected Replicated Storage System
| 항목 | 내용 |
|------|------|
| **저자** | D. B. Terry, M. M. Theimer, K. Petersen, A. J. Demers, M. J. Spreitzer, C. H. Hauser |
| **학회/연도** | SOSP 1995 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/224056.224070) · [ACM DL](https://dl.acm.org/doi/10.1145/224056.224070) |

**요약**: 약한 연결 환경에서의 복제 스토리지. 애플리케이션별 충돌 해결 절차(merge procedure)와 의존성 검사(dependency check) 도입. Dynamo의 "클라이언트 측 충돌 해결" 아이디어의 직접적 선행 연구.

### 10-2. Session Guarantees for Weakly Consistent Replicated Data
| 항목 | 내용 |
|------|------|
| **저자** | D. B. Terry, A. J. Demers, K. Petersen, M. Spreitzer, M. Theimer, B. B. Welch |
| **학회/연도** | PDIS 1994 |
| **링크** | [DOI](https://doi.org/10.1109/PDIS.1994.331722) |

**요약**: Read-your-writes, monotonic reads, monotonic writes, writes-follow-reads 네 가지 세션 보장 정의. 약한 일관성 모델에서도 사용자 경험을 개선하는 실용적 보장. DynamoDB의 strongly consistent reads 옵션과 관련.

### 10-3. Eventually Consistent
| 항목 | 내용 |
|------|------|
| **저자** | W. Vogels |
| **학회/연도** | CACM, 2009 |
| **SCIE** | JCR IF ~11.1 |
| **링크** | [DOI](https://doi.org/10.1145/1435417.1435432) · [ACM DL](https://dl.acm.org/doi/10.1145/1435417.1435432) |

**요약**: Amazon CTO가 eventual consistency의 다양한 변형(strong, weak, eventual, causal, read-your-writes 등)을 체계적으로 정리한 해설 논문. Dynamo의 설계 철학을 학술적으로 정리.

### 10-4. Providing High Availability Using Lazy Replication
| 항목 | 내용 |
|------|------|
| **저자** | R. Ladin, B. Liskov, L. Shrira, S. Ghemawat |
| **학회/연도** | ACM TOCS, 1992 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/138873.138877) · [ACM DL](https://dl.acm.org/doi/10.1145/138873.138877) |

**요약**: 지연 복제(lazy replication)로 고가용성을 달성하는 방법. 가십 기반 업데이트 전파와 인과적 순서 보장. Dynamo의 비동기 복제 전략에 영향.

---

## 11. Virtual Synchrony / Process Groups

### 11-1. Exploiting Virtual Synchrony in Distributed Systems
| 항목 | 내용 |
|------|------|
| **저자** | K. P. Birman, T. A. Joseph |
| **학회/연도** | SOSP 1987 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/41457.37515) · [ACM DL](https://dl.acm.org/doi/10.1145/41457.37515) |

**요약**: 가상 동기(virtual synchrony) 개념으로 프로세스 그룹 내 일관된 뷰를 보장. ISIS 시스템의 기반. 그룹 멤버십 변경과 메시지 전달의 원자적 처리.

### 11-2. The Process Group Approach to Reliable Distributed Computing
| 항목 | 내용 |
|------|------|
| **저자** | K. P. Birman |
| **학회/연도** | CACM, 1993 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/163298.163303) · [ACM DL](https://dl.acm.org/doi/10.1145/163298.163303) |

**요약**: 프로세스 그룹 기반 신뢰성 있는 분산 컴퓨팅의 체계적 소개. ISIS/Horus 시스템의 경험을 바탕으로 가상 동기의 실용적 활용 정리.

### 11-3. Lightweight Causal and Atomic Group Multicast
| 항목 | 내용 |
|------|------|
| **저자** | K. Birman, A. Schiper, P. Stephenson |
| **학회/연도** | ACM TOCS, 1991 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/128738.128742) |

**요약**: 경량 인과적/원자적 그룹 멀티캐스트 프로토콜. 벡터 시계를 활용한 인과적 순서 보장과 그룹 멤버십 관리의 결합.

### 11-4. Understanding the Limitations of Causally and Totally Ordered Communication
| 항목 | 내용 |
|------|------|
| **저자** | D. R. Cheriton, D. Skeen |
| **학회/연도** | SOSP 1993 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/168619.168623) · [ACM DL](https://dl.acm.org/doi/10.1145/168619.168623) |

**요약**: Birman의 가상 동기/인과적 순서에 대한 비판 논문. 인과적/전체 순서 보장의 성능 비용과 실용성 한계를 지적. 분산 시스템 커뮤니티의 유명한 논쟁.

---

## 12. Anti-Entropy / Read Repair

### 12-1. Efficient Reconciliation and Flow Control for Anti-Entropy Protocols
| 항목 | 내용 |
|------|------|
| **저자** | R. van Renesse, D. Dumitriu, V. Grishchenko, A. S. Kemaev |
| **학회/연도** | 관련 기술 보고서 |
| **링크** | [PDF (Cornell)](https://www.cs.cornell.edu/home/rvr/papers/flowgossip.pdf) |

**요약**: Anti-entropy 프로토콜의 효율적 조정(reconciliation)과 흐름 제어. Dynamo의 read-repair와 anti-entropy 프로세스의 성능 최적화에 관련.

---

## 13. Amazon 내부 / 관련 시스템

### 13-1. Cluster-Based Scalable Network Services
| 항목 | 내용 |
|------|------|
| **저자** | A. Fox, S. D. Gribble, Y. Chawathe, E. A. Brewer, P. Gauthier |
| **학회/연도** | SOSP 1997 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/268998.266662) |

**요약**: 클러스터 기반 확장 가능한 네트워크 서비스 아키텍처. Brewer의 초기 연구로, CAP 정리 방향의 사고가 형성된 연구.

### 13-2. SEDA: An Architecture for Well-Conditioned, Scalable Internet Services
| 항목 | 내용 |
|------|------|
| **저자** | M. Welsh, D. Culler, E. Brewer |
| **학회/연도** | SOSP 2001 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/502034.502057) · [ACM DL](https://dl.acm.org/doi/10.1145/502034.502057) |

**요약**: Staged Event-Driven Architecture. 서비스를 이벤트 큐로 연결된 단계(stage)로 분해하여 과부하 시에도 안정적 성능 보장. Amazon의 서비스 아키텍처에 영향.

---

## 14. 기반 분산 시스템

### 14-1. The Google File System
| 항목 | 내용 |
|------|------|
| **저자** | S. Ghemawat, H. Gobioff, S.-T. Leung |
| **학회/연도** | SOSP 2003 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/945445.945450) · [PDF (Google)](https://research.google.com/archive/gfs-sosp2003.pdf) |

**요약**: Google 규모의 분산 파일 시스템. 단일 마스터 + 다수 청크 서버. Bigtable의 기반 저장소.

### 14-2. Bigtable: A Distributed Storage System for Structured Data
| 항목 | 내용 |
|------|------|
| **저자** | F. Chang, J. Dean, S. Ghemawat, W. C. Hsieh, D. A. Wallach, M. Burrows, T. Chandra, A. Fikes, R. E. Gruber |
| **학회/연도** | OSDI 2006 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi-06/bigtable-distributed-storage-system-structured-data) · [PDF (Google)](https://research.google.com/archive/bigtable-osdi06.pdf) |

**요약**: Google의 구조화된 데이터용 분산 스토리지. Wide-column 모델. HBase, Cassandra에 영향.

### 14-3. Impossibility of Distributed Consensus with One Faulty Process (FLP)
| 항목 | 내용 |
|------|------|
| **저자** | M. J. Fischer, N. A. Lynch, M. S. Paterson |
| **학회/연도** | JACM, 1985 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/3149.214121) · [ACM DL](https://dl.acm.org/doi/10.1145/3149.214121) |

**요약**: 비동기 분산 시스템에서 단 하나의 crash failure로도 결정론적 합의 불가능 증명. 분산 시스템 이론 최고의 불가능성 결과.

### 14-4. The Byzantine Generals Problem
| 항목 | 내용 |
|------|------|
| **저자** | L. Lamport, R. Shostak, M. Pease |
| **학회/연도** | ACM TOPLAS, 1982 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/357172.357176) · [ACM DL](https://dl.acm.org/doi/10.1145/357172.357176) · [PDF (Lamport)](https://lamport.azurewebsites.net/pubs/byz.pdf) |

**요약**: 비잔틴 장애 환경에서의 합의 문제 정의. N ≥ 3f+1 필요 조건 증명. PBFT, 블록체인의 이론적 기반.

---

## 15. Post-Dynamo 시스템

### 15-1. Cassandra: A Decentralized Structured Storage System
| 항목 | 내용 |
|------|------|
| **저자** | A. Lakshman, P. Malik |
| **학회/연도** | LADIS 2009 / ACM SIGOPS OSR 2010 |
| **링크** | [DOI](https://doi.org/10.1145/1773912.1773922) · [ACM DL](https://dl.acm.org/doi/10.1145/1773912.1773922) |

**요약**: Dynamo + Bigtable 모델 결합. Dynamo 공저자 Lakshman이 Facebook에서 개발. 사실상 Dynamo의 오픈소스 후속작.
