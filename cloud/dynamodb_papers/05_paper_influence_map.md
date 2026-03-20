# DynamoDB 논문 영향 관계도 및 전체 인덱스

## 1. 논문 영향 관계도 (Influence Map)

```
                    ┌─────────────────────────────────────────────┐
                    │         이론적 기반 (1978~1998)                │
                    │                                             │
                    │  Lamport Clocks ──→ Vector Clocks           │
                    │  (CACM 1978)       (Fidge 1988)             │
                    │                                             │
                    │  2PC (Gray 1978)                            │
                    │                                             │
                    │  Quorum Voting ──→ Sloppy Quorum            │
                    │  (SOSP 1979)       (Dynamo 2007)            │
                    │                                             │
                    │  FLP Impossibility (JACM 1985)              │
                    │                                             │
                    │  Gossip Protocols (PODC 1987)               │
                    │                                             │
                    │  Consistent Hashing (STOC 1997)             │
                    │                                             │
                    │  Paxos (TOCS 1998)                          │
                    │                                             │
                    │  CAP Theorem (PODC 2000)                    │
                    └───────────┬─────────────────────────────────┘
                                │
                                ▼
    ┌──────────────────────────────────────────────────────────────┐
    │              1세대 분산 스토리지 (2003~2006)                    │
    │                                                              │
    │  GFS (SOSP 2003) ──→ Bigtable (OSDI 2006)                   │
    │  MapReduce (OSDI 2004)      │                                │
    │  Chord/CAN/Pastry (2001)    │                                │
    └─────────────────────────────┼────────────────────────────────┘
                                  │
                                  ▼
    ┌──────────────────────────────────────────────────────────────┐
    │         ★ Amazon Dynamo (SOSP 2007) ★                        │
    │                                                              │
    │  Consistent Hashing + Vector Clocks + Sloppy Quorum         │
    │  + Gossip Protocol + Merkle Trees + Hinted Handoff           │
    └───┬──────────┬──────────┬───────────────────────────────────┘
        │          │          │
        ▼          ▼          ▼
    ┌────────┐ ┌────────┐ ┌────────────────────────────────────┐
    │Cassandra│ │ PNUTS  │ │ Google 계열                         │
    │(2010)  │ │(VLDB   │ │                                    │
    │Dynamo +│ │ 2008)  │ │ Megastore (CIDR 2011)              │
    │Bigtable│ │        │ │     │                              │
    │모델 결합│ │        │ │     ▼                              │
    └────────┘ └────────┘ │ Spanner (OSDI 2012)               │
                          │     │                              │
                          │     ▼                              │
                          │ CockroachDB (SIGMOD 2020)          │
                          └────────────────────────────────────┘
        │
        ▼
    ┌──────────────────────────────────────────────────────────────┐
    │              최신 세대 (2012~2022)                             │
    │                                                              │
    │  Calvin (SIGMOD 2012) ──→ FoundationDB (SIGMOD 2021)         │
    │  Raft (ATC 2014) ──→ TiDB (VLDB 2020)                       │
    │  TAO (ATC 2013)                                              │
    │                                                              │
    │  ★ Amazon DynamoDB (ATC 2022) ★                              │
    │  ★ DynamoDB Transactions (ATC 2023) ★                        │
    └──────────────────────────────────────────────────────────────┘
```

---

## 2. 전체 논문 인덱스 (BK21 점수 포함)

### BK21 인정 IF 4 (최우수 Top-tier)

| # | 논문 | 학회 | 연도 | 문서 |
|---|------|------|------|------|
| 1 | Consistent Hashing and Random Trees | STOC | 1997 | 02_foundational |
| 2 | Chord: A Scalable Peer-to-Peer Lookup Service | SIGCOMM | 2001 | 02_foundational |
| 3 | CAN: A Scalable Content-Addressable Network | SIGCOMM | 2001 | 02_foundational |
| 4 | The Google File System | SOSP | 2003 | 03_foundations |
| 5 | MapReduce: Simplified Data Processing on Large Clusters | OSDI | 2004 | 03_foundations |
| 6 | Bigtable: A Distributed Storage System for Structured Data | OSDI | 2006 | 04_competing |
| 7 | **Dynamo: Amazon's Highly Available Key-value Store** | **SOSP** | **2007** | **01_origin** |
| 8 | PNUTS: Yahoo!'s Hosted Data Serving Platform | VLDB | 2008 | 04_competing |
| 9 | Spanner: Google's Globally-Distributed Database | OSDI | 2012 | 04_competing |
| 10 | Calvin: Fast Distributed Transactions | SIGMOD | 2012 | 04_competing |
| 11 | CockroachDB: The Resilient Geo-Distributed SQL Database | SIGMOD | 2020 | 04_competing |
| 12 | TiDB: A Raft-based HTAP Database | VLDB | 2020 | 04_competing |
| 13 | FoundationDB: A Distributed Unbundled Transactional KV Store | SIGMOD | 2021 | 04_competing |
| 14 | Weighted Voting for Replicated Data | SOSP | 1979 | 02_foundational |
| 15 | Practical Byzantine Fault Tolerance | OSDI | 1999 | 03_foundations |

### BK21 인정 IF 3 (우수 Top-tier)

| # | 논문 | 학회 | 연도 | 문서 |
|---|------|------|------|------|
| 16 | Epidemic Algorithms for Replicated Database Maintenance | PODC | 1987 | 02_foundational |
| 17 | CAP Theorem Keynote (Brewer) | PODC | 2000 | 02_foundational |
| 18 | **Amazon DynamoDB (updated)** | **ATC** | **2022** | **01_origin** |
| 19 | **DynamoDB Transactions** | **ATC** | **2023** | **01_origin** |
| 20 | TAO: Facebook's Distributed Data Store | ATC | 2013 | 04_competing |
| 21 | Raft: In Search of an Understandable Consensus Algorithm | ATC | 2014 | 03_foundations |
| 22 | ICDE 관련 논문들 | ICDE | - | - |

### SCIE 저널 (JCR IF 적용)

| # | 논문 | 저널 | 연도 | JCR IF (대략) |
|---|------|------|------|-------------|
| 23 | Time, Clocks, and the Ordering of Events | CACM | 1978 | ~11.1 |
| 24 | Paxos (The Part-Time Parliament) | ACM TOCS | 1998 | ~2.1 |
| 25 | FLP Impossibility | JACM | 1985 | SCIE |
| 26 | Eventually Consistent | CACM | 2009 | ~11.1 |
| 27 | Brewer's Conjecture (CAP 형식 증명) | ACM SIGACT News | 2002 | - |

### BK21 미등재 / 기타

| # | 논문 | 학회/출처 | 연도 | 비고 |
|---|------|----------|------|------|
| 28 | Megastore | CIDR | 2011 | CIDR는 BK21 미등재 |
| 29 | Cassandra | SIGOPS Review | 2010 | 뉴스레터/워크숍 성격 |
| 30 | Vector Clocks (Fidge) | Australian CS Conf. | 1988 | 지역 학회 |
| 31 | Vector Clocks (Mattern) | Workshop | 1989 | 워크숍 |
| 32 | Pastry | Middleware | 2001 | BK21 등급 확인 필요 |
| 33 | Merkle Trees | CRYPTO | 1979/1989 | 암호학 학회 |
| 34 | 2PC (Jim Gray) | Springer LNCS | 1978 | 교과서/튜토리얼 |
| 35 | Paxos Made Simple | SIGACT News | 2001 | 해설 논문 |

---

## 3. 파일 구조

```
cloud/dynamodb_papers/              ← 상세 분석 (논문별 3~5 문단)
├── 00_BK21_tier_system.md          ← BK21 등급 체계 및 점수 설명
├── 01_dynamo_origin.md             ← Dynamo 원본 + DynamoDB 진화 논문
├── 02_foundational_papers.md       ← Dynamo가 참조한 기반 논문 (51편)
├── 03_distributed_systems_foundations.md ← 분산 시스템 이론 (44편)
├── 04_competing_systems.md         ← 동시대/경쟁 분산 DB 논문 비교 (39편)
├── 05_paper_influence_map.md       ← 영향 관계도 및 전체 인덱스 (이 문서)
└── README.md                       ← 디렉토리 개요

cloud/dynamodb_paper_summaries/     ← 논문별 링크 + 1줄 요약 (빠른 참조)
├── 01_dynamo_core.md               ← Dynamo/DynamoDB 핵심 3편
├── 02_foundational.md              ← 기반 논문 요약 + 링크 (51편)
├── 03_distributed_theory.md        ← 분산 이론 요약 + 링크 (44편)
├── 04_competing_systems.md         ← 경쟁 시스템 요약 + 링크 (39편)
├── 05_master_index.md              ← 전체 마스터 인덱스 (BK21 등급별, ~120편)
└── README.md                       ← 디렉토리 개요
```
