# 경쟁/관련 분산 DB 논문 요약 + 링크 (39편)

---

## 1. Google Bigtable

### 1-1. Bigtable: A Distributed Storage System for Structured Data
| 항목 | 내용 |
|------|------|
| **저자** | F. Chang, J. Dean, S. Ghemawat, W. C. Hsieh, D. A. Wallach, M. Burrows, T. Chandra, A. Fikes, R. E. Gruber |
| **학회/연도** | OSDI 2006 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi-06/bigtable-distributed-storage-system-structured-data) · [PDF (Google)](https://research.google.com/archive/bigtable-osdi06.pdf) |

**요약**: GFS 위에 구축된 Wide-column 분산 스토리지. 단일 마스터가 태블릿 할당 관리. Strong consistency (CP). HBase, Cassandra의 데이터 모델에 직접적 영향. Dynamo와 대비되는 Master-slave 아키텍처.

### 1-2. Bigtable Merge Compaction
| 항목 | 내용 |
|------|------|
| **저자** | C. Mathieu et al. |
| **학회/연도** | arXiv 2014 |
| **링크** | [arXiv](https://arxiv.org/abs/1407.3008) |

**요약**: Bigtable의 LSM-tree compaction 전략 분석 및 최적화.

---

## 2. Yahoo! PNUTS

### 2-1. PNUTS: Yahoo!'s Hosted Data Serving Platform
| 항목 | 내용 |
|------|------|
| **저자** | B. F. Cooper, R. Ramakrishnan, U. Srivastava, A. Silberstein, P. Bohannon, H.-A. Jacobsen, N. Puz, D. Weaver, R. Yerneni |
| **학회/연도** | VLDB 2008 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/1454159.1454167) · [ACM DL](https://dl.acm.org/doi/10.14778/1454159.1454167) |

**요약**: 레코드별 타임라인 일관성. 각 레코드의 마스터 리전이 쓰기를 순서화. Dynamo의 AP 접근과 달리 레코드별 마스터로 충돌을 원천 차단.

### 2-2. PNUTS to Sherpa: Lessons from Yahoo!'s Cloud Database
| 항목 | 내용 |
|------|------|
| **저자** | B. F. Cooper et al. |
| **학회/연도** | VLDB 2019 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3352063.3352146) |

**요약**: PNUTS에서 Sherpa로의 10년간 진화 회고. 운영 경험에서 얻은 교훈 정리.

---

## 3. Apache Cassandra

### 3-1. Cassandra: A Decentralized Structured Storage System
| 항목 | 내용 |
|------|------|
| **저자** | A. Lakshman, P. Malik |
| **학회/연도** | ACM SIGOPS OSR, 2010 |
| **링크** | [DOI](https://doi.org/10.1145/1773912.1773922) · [ACM DL](https://dl.acm.org/doi/10.1145/1773912.1773922) |

**요약**: Dynamo 파티셔닝/복제 + Bigtable 데이터 모델 결합. Dynamo 공저자 Lakshman이 Facebook에서 개발. 사실상 Dynamo의 오픈소스 후속작.

---

## 4. Google Megastore

### 4-1. Megastore: Providing Scalable, Highly Available Storage for Interactive Services
| 항목 | 내용 |
|------|------|
| **저자** | J. Baker, C. Bond, J. C. Corbett, J. J. Furman, A. Khorlin, J. Larson, J.-M. Leon, Y. Li, A. Lloyd, V. Yushprakh |
| **학회/연도** | CIDR 2011 |
| **BK21** | **미등재** |
| **링크** | [PDF (CIDR)](https://www.cidrdb.org/cidr2011/Papers/CIDR11_Paper32.pdf) |

**요약**: Entity Group 단위 Paxos 기반 동기식 복제. Bigtable 위에 ACID 시맨틱 제공. 높은 쓰기 지연이 문제로 지적되어 Spanner로 대체.

---

## 5. Google Spanner

### 5-1. Spanner: Google's Globally-Distributed Database
| 항목 | 내용 |
|------|------|
| **저자** | J. C. Corbett, J. Dean, M. Epstein, A. Fikes, C. Frost, J. J. Furman, S. Ghemawat, ... (26명) |
| **학회/연도** | OSDI 2012 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi12/technical-sessions/presentation/corbett) · [PDF (Google)](https://research.google.com/archive/spanner-osdi2012.pdf) |

**요약**: GPS+원자시계 기반 TrueTime API로 전역 외부 일관성(external consistency) 달성. Dynamo와 정반대 철학: "Always consistent" vs "Always writable". 분산 DB 역사상 가장 야심적인 논문 중 하나.

### 5-2. Spanner: Becoming a SQL System
| 항목 | 내용 |
|------|------|
| **저자** | D. F. Bacon et al. |
| **학회/연도** | SIGMOD 2017 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3035918.3056103) · [ACM DL](https://dl.acm.org/doi/10.1145/3035918.3056103) |

**요약**: Spanner가 NoSQL에서 완전한 SQL 시스템으로 진화한 과정. 분산 쿼리 처리, 범위 추출, 쿼리 재시작 등 추가.

### 5-3. Spanner, TrueTime and the CAP Theorem
| 항목 | 내용 |
|------|------|
| **저자** | E. Brewer |
| **학회/연도** | Google Research, 2017 |
| **링크** | [PDF (Google)](https://research.google.com/pubs/archive/45855.pdf) |

**요약**: Brewer가 Spanner와 CAP 정리의 관계를 해설. Spanner는 기술적으로 CP이지만 실질적으로 CA처럼 동작한다고 주장 (파티션이 극히 드물어서).

---

## 6. F1

### 6-1. F1: A Distributed SQL Database That Scales
| 항목 | 내용 |
|------|------|
| **저자** | J. Shute et al. |
| **학회/연도** | VLDB 2013 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/2536222.2536232) |

**요약**: Google AdWords를 위한 Spanner 위의 SQL 레이어. 수십 PB 데이터에 대해 완전한 SQL + ACID 트랜잭션 제공.

### 6-2. F1 Query: Declarative Querying at Scale
| 항목 | 내용 |
|------|------|
| **저자** | B. Samwel et al. |
| **학회/연도** | VLDB 2018 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3231751.3231757) |

**요약**: F1의 선언적 쿼리 처리 시스템. Spanner, Bigtable, CSV 등 이종 데이터 소스에 대한 통합 쿼리.

---

## 7. Calvin

### 7-1. Calvin: Fast Distributed Transactions for Partitioned Database Systems
| 항목 | 내용 |
|------|------|
| **저자** | A. Thomson, T. Diamond, S.-C. Weng, K. Ren, P. Shao, D. J. Abadi |
| **학회/연도** | SIGMOD 2012 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/2213836.2213838) · [ACM DL](https://dl.acm.org/doi/10.1145/2213836.2213838) |

**요약**: 결정론적 트랜잭션 순서화로 분산 잠금/2PC 없이 트랜잭션 처리. 복제본 간 합의가 실행 전에 이루어짐. FoundationDB에 영향.

### 7-2. The Case for Determinism in Database Systems
| 항목 | 내용 |
|------|------|
| **저자** | A. Thomson, D. J. Abadi |
| **학회/연도** | VLDB 2010 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/1920841.1920855) |

**요약**: 결정론적 DB 시스템의 이론적 논거. Calvin의 사전 연구.

### 7-3. SLOG: Serializable, Low-latency, Geo-replicated Transactions
| 항목 | 내용 |
|------|------|
| **저자** | K. Ren, D. Li, D. J. Abadi |
| **학회/연도** | VLDB 2019 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3342263.3342647) |

**요약**: Calvin을 지리적 분산 환경에 최적화. 데이터 지역성(locality)을 활용하여 cross-region 트랜잭션을 최소화.

### 7-4. Aria: A Fast and Practical Deterministic OLTP Database
| 항목 | 내용 |
|------|------|
| **저자** | Y. Lu, X. Yu, L. Cao, S. Madden |
| **학회/연도** | VLDB 2020 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3407790.3407808) |

**요약**: Calvin의 실용적 개선. 결정론적 실행에서 read-set 사전 선언 없이도 동작하는 프로토콜.

### 7-5. An Overview of Deterministic Database Systems
| 항목 | 내용 |
|------|------|
| **저자** | D. J. Abadi |
| **학회/연도** | CACM, 2018 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1145/3181853) |

**요약**: 결정론적 데이터베이스 시스템의 포괄적 서베이 및 미래 전망.

---

## 8. Facebook TAO

### 8-1. TAO: Facebook's Distributed Data Store for the Social Graph
| 항목 | 내용 |
|------|------|
| **저자** | N. Bronson, Z. Amsden, G. Cabrera, P. Chakka, P. Dimov, H. Ding, ... (15명) |
| **학회/연도** | USENIX ATC 2013 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/atc13/technical-sessions/presentation/bronson) · [PDF](https://www.usenix.org/system/files/conference/atc13/atc13-bronson.pdf) |

**요약**: Facebook 소셜 그래프 전용 분산 캐시. MySQL 위에 그래프 추상화 제공. 초당 10억 건 이상 읽기 처리.

### 8-2. RAMP-TAO: Layering Atomic Transactions on Facebook's Online TAO Data Store
| 항목 | 내용 |
|------|------|
| **저자** | A. Cheng et al. |
| **학회/연도** | VLDB 2021 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3476311.3476379) |

**요약**: TAO에 원자적 트랜잭션(Read Atomic Multi-Partition)을 추가. 기존 캐시 시스템의 성능을 유지하면서 트랜잭션 보장 강화.

---

## 9. CockroachDB

### 9-1. CockroachDB: The Resilient Geo-Distributed SQL Database
| 항목 | 내용 |
|------|------|
| **저자** | R. Taft, I. Sharif, A. Matei, N. VanBenschoten, J. Lewis, T. Grieger, ... (17명) |
| **학회/연도** | SIGMOD 2020 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3318464.3386134) · [ACM DL](https://dl.acm.org/doi/10.1145/3318464.3386134) |

**요약**: 오픈소스 Spanner 구현. TrueTime 대신 Hybrid Logical Clock 사용으로 특수 하드웨어 불필요. Raft 기반 복제. Serializable Snapshot Isolation.

---

## 10. TiDB

### 10-1. TiDB: A Raft-based HTAP Database
| 항목 | 내용 |
|------|------|
| **저자** | D. Huang, Q. Liu, Q. Cui, Z. Fang, X. Ma, F. Xu, L. Shen, ... (21명) |
| **학회/연도** | VLDB 2020 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3415478.3415535) |

**요약**: Raft + Percolator 기반 분산 HTAP DB. MySQL 호환. TiKV(KV 스토어) + TiFlash(분석용 열 저장소) 아키텍처.

---

## 11. FoundationDB

### 11-1. FoundationDB: A Distributed Unbundled Transactional Key Value Store
| 항목 | 내용 |
|------|------|
| **저자** | J. Zhou, M. Xu, A. Shraer, B. Namasivayam, A. Miller, E. Tschannen, ... (17명) |
| **학회/연도** | SIGMOD 2021 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3448016.3457559) · [ACM DL](https://dl.acm.org/doi/10.1145/3448016.3457559) |

**요약**: 핵심 KV 스토어 위에 다양한 데이터 모델을 "레이어"로 구현하는 unbundled 아키텍처. 결정론적 시뮬레이션 테스트(deterministic simulation testing)로 정확성 검증. Apple이 인수하여 iCloud 백엔드로 사용.

### 11-2. FoundationDB Record Layer: A Multi-Tenant Structured Datastore
| 항목 | 내용 |
|------|------|
| **저자** | C. Chrysafis et al. |
| **학회/연도** | SIGMOD 2019 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3299869.3314039) |

**요약**: FoundationDB 위의 구조화된 데이터 레이어. 멀티테넌트 지원, 스키마 관리, 인덱싱 제공.

---

## 12. Google Percolator

### 12-1. Large-scale Incremental Processing Using Distributed Transactions and Notifications
| 항목 | 내용 |
|------|------|
| **저자** | D. Peng, F. Dabek |
| **학회/연도** | OSDI 2010 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/osdi10/large-scale-incremental-processing-using-distributed-transactions-and) · [PDF (Google)](https://research.google.com/pubs/archive/36726.pdf) |

**요약**: Bigtable 위에 Snapshot Isolation 기반 분산 트랜잭션 구현. 2PC + timestamp oracle. TiDB/TiKV가 이 모델을 직접 채택.

---

## 13. Azure Cosmos DB

### 13-1. Schema-Agnostic Indexing with Azure DocumentDB
| 항목 | 내용 |
|------|------|
| **저자** | D. Shukla et al. |
| **학회/연도** | VLDB 2015 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/2824032.2824075) |

**요약**: Cosmos DB의 전신인 DocumentDB의 스키마 무관(schema-agnostic) 인덱싱. Bw-Tree 기반. 모든 문서 필드를 자동 인덱싱.

---

## 14. Amazon Aurora

### 14-1. Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases
| 항목 | 내용 |
|------|------|
| **저자** | A. Verbitski, A. Gupta, D. Slich, T. Guo, H. Howard, R. Brandenberger, J. Bao, ... |
| **학회/연도** | SIGMOD 2017 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3035918.3056101) · [ACM DL](https://dl.acm.org/doi/10.1145/3035918.3056101) |

**요약**: "로그가 곧 데이터베이스"라는 혁신적 설계. MySQL/PostgreSQL 호환 관계형 DB를 클라우드 네이티브로 재설계. 6개 복제본, 4/6 쓰기 쿼럼, 3/6 읽기 쿼럼.

### 14-2. Amazon Aurora: On Avoiding Distributed Consensus for I/Os, Commits, and Membership Changes
| 항목 | 내용 |
|------|------|
| **저자** | A. Verbitski et al. |
| **학회/연도** | SIGMOD 2018 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/3183713.3196937) · [ACM DL](https://dl.acm.org/doi/10.1145/3183713.3196937) |

**요약**: Aurora가 I/O, 커밋, 멤버십 변경에서 분산 합의를 회피하는 방법. Paxos 없이 쿼럼 기반 접근으로 성능 최적화.

---

## 15. Voldemort (LinkedIn)

### 15-1. Serving Large-scale Batch Computed Data with Project Voldemort
| 항목 | 내용 |
|------|------|
| **저자** | R. Sumbaly et al. |
| **학회/연도** | USENIX FAST 2012 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/fast12/serving-large-scale-batch-computed-data-project-voldemort) |

**요약**: LinkedIn의 Dynamo 클론. 대규모 배치 데이터를 읽기 전용 스토어로 서빙. Consistent hashing + 쿼럼 기반 복제.

---

## 16. Anna

### 16-1. Anna: A KVS for Any Scale
| 항목 | 내용 |
|------|------|
| **저자** | C. Wu, J. M. Faleiro, Y. Lin, J. M. Hellerstein |
| **학회/연도** | ICDE 2018 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1109/ICDE.2018.00044) · [IEEE](https://ieeexplore.ieee.org/document/8509395) |

**요약**: 격자(lattice) 기반 조율 없는(coordination-free) KV 스토어. 단일 노드부터 클라우드까지 스케일링. CRDT 원리를 활용한 충돌 해결.

### 16-2. Autoscaling Tiered Cloud Storage in Anna
| 항목 | 내용 |
|------|------|
| **저자** | C. Wu et al. |
| **학회/연도** | VLDB 2019 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3311880.3311881) |

**요약**: Anna에 자동 스케일링과 계층형 스토리지(메모리/SSD/S3) 추가.

---

## 17. Epoxy

### 17-1. Epoxy: ACID Transactions Across Diverse Data Stores
| 항목 | 내용 |
|------|------|
| **저자** | P. Kraft et al. |
| **학회/연도** | VLDB 2023 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3611479.3611484) |

**요약**: 이종 데이터 스토어(RDBMS, KV store, 메시지 큐 등) 간 ACID 트랜잭션 제공. 각 스토어의 기존 트랜잭션 메커니즘을 활용하여 교차 스토어 원자성 보장.

---

## 18. 스토리지 자료구조

### 18-1. The Log-Structured Merge-Tree (LSM-Tree)
| 항목 | 내용 |
|------|------|
| **저자** | P. O'Neil, E. Cheng, D. Gawlick, E. O'Neil |
| **학회/연도** | Acta Informatica, 1996 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/s002360050048) |

**요약**: 쓰기 최적화 자료구조. 거의 모든 현대 분산 DB의 스토리지 엔진 기반 (Bigtable, Cassandra, RocksDB, DynamoDB).

### 18-2. LSM-based Storage Techniques: A Survey
| 항목 | 내용 |
|------|------|
| **저자** | C. Luo, M. Carey |
| **학회/연도** | VLDB Journal, 2020 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1007/s00778-019-00555-y) |

**요약**: LSM-tree 기반 스토리지 기법의 포괄적 서베이. Compaction 전략, 인덱싱, 병합 최적화 등.

### 18-3. The Bw-Tree: A B-tree for New Hardware Platforms
| 항목 | 내용 |
|------|------|
| **저자** | J. Levandoski, D. Lomet, S. Sengupta |
| **학회/연도** | ICDE 2013 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [DOI](https://doi.org/10.1109/ICDE.2013.6544834) |

**요약**: 락-프리 B-tree 변형. 멀티코어 + 플래시 스토리지에 최적화. Cosmos DB의 인덱싱 기반.

### 18-4. Cache-Oblivious Streaming B-trees (B-epsilon Tree)
| 항목 | 내용 |
|------|------|
| **저자** | M. A. Bender et al. |
| **학회/연도** | SPAA 2007 |
| **링크** | [DOI](https://doi.org/10.1145/1248377.1248393) |

**요약**: B-tree와 LSM-tree 사이의 트레이드오프를 제공하는 write-optimized 자료구조. TokuDB에서 사용.

---

## 19. 서베이 논문

### 19-1. Cloud Databases: New Techniques, Challenges, and Opportunities
| 항목 | 내용 |
|------|------|
| **저자** | G. Li et al. |
| **학회/연도** | VLDB 2022 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.14778/3554821.3554830) |

**요약**: 클라우드 데이터베이스의 기술, 도전, 기회를 다룬 종합 서베이.

### 19-2. Cloud-Native Databases: A Survey
| 항목 | 내용 |
|------|------|
| **저자** | H. Dong et al. |
| **학회/연도** | IEEE TKDE, 2024 |
| **SCIE** |
| **링크** | [DOI](https://doi.org/10.1109/TKDE.2024.3397508) |

**요약**: 클라우드 네이티브 DB의 포괄적 서베이. 스토리지-컴퓨팅 분리, 서버리스, 멀티테넌시 등.

### 19-3. HTAP Databases: What is New and What is Next
| 항목 | 내용 |
|------|------|
| **저자** | G. Li, C. Zhang |
| **학회/연도** | SIGMOD 2022 |
| **BK21** | IF **4** (Top-tier 최우수) |

**요약**: HTAP 데이터베이스의 현황과 미래 방향 서베이.

---

## 20. Amazon DynamoDB (최신)

### 20-1. Amazon DynamoDB (ATC 2022)
→ [01_dynamo_core.md#2](01_dynamo_core.md) 참조

### 20-2. DynamoDB Distributed Transactions (ATC 2023)
→ [01_dynamo_core.md#3](01_dynamo_core.md) 참조
