# Dynamo / DynamoDB 핵심 논문 요약 + 링크

---

## 1. Dynamo: Amazon's Highly Available Key-value Store

| 항목 | 내용 |
|------|------|
| **저자** | G. DeCandia, D. Hastorun, M. Jampani, G. Kakulaparam, A. Lakshman, A. Pilchin, S. Sivasubramanian, P. Vosshall, W. Vogels |
| **학회/연도** | SOSP 2007 |
| **BK21** | IF **4** (Top-tier 최우수) |
| **링크** | [DOI](https://doi.org/10.1145/1294261.1294281) · [PDF (Amazon)](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) · [PDF (Cornell)](https://www.cs.cornell.edu/courses/cs5414/2017fa/papers/dynamo.pdf) |

**요약**: Amazon의 장바구니 서비스를 위한 고가용성 키-값 저장소. Consistent Hashing, Vector Clocks, Sloppy Quorum, Gossip Protocol, Merkle Tree를 결합하여 AP(Availability + Partition Tolerance) 시스템 구현. "always writable" 설계 철학으로, 가용성을 일관성보다 우선시하는 최초의 대규모 산업 시스템 논문. NoSQL 운동의 학술적 기반을 제공하고 Cassandra, Riak, Voldemort 등 후속 시스템에 직접적 영향을 미침.

---

## 2. Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service

| 항목 | 내용 |
|------|------|
| **저자** | M. Elhemali, N. Gallagher, N. Gordon, J. Idziorek, R. Krog, C. Lazier, E. Mo, A. Mritunjai, S. Perianayagam, T. Rath, S. Sivasubramanian, J. C. Sorenson III, S. Sosothikul, D. Terry, A. Vig |
| **학회/연도** | USENIX ATC 2022 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/atc22/presentation/elhemali) · [PDF](https://www.usenix.org/system/files/atc22-elhemali.pdf) |

**요약**: 2012년 서비스 출시 이후 10년간의 DynamoDB 진화를 기술. 원본 Dynamo에서 크게 변화: (1) Strong consistency 옵션 추가, (2) Vector Clock 대신 Last-Writer-Wins, (3) 자동 파티셔닝/리밸런싱, (4) 스토리지와 컴퓨팅 분리, (5) 글로벌 테이블(다중 리전 복제). 2021년 Prime Day에서 초당 8,920만 요청 피크 처리. Dynamo의 AP 설계에서 CP/AP 선택 가능한 하이브리드로 전환한 과정을 설명.

---

## 3. Distributed Transactions at Scale in Amazon DynamoDB

| 항목 | 내용 |
|------|------|
| **저자** | J. Idziorek, A. Keyes, C. Lazier, S. Perianayagam, P. Ramanathan, J. C. Sorenson III, D. Terry, A. Vig |
| **학회/연도** | USENIX ATC 2023 |
| **BK21** | IF **3** (Top-tier 우수) |
| **링크** | [USENIX](https://www.usenix.org/conference/atc23/presentation/idziorek) · [PDF](https://www.usenix.org/system/files/atc23-idziorek.pdf) |

**요약**: DynamoDB에 multi-item ACID 트랜잭션을 추가한 방법론. Timestamp ordering protocol 기반의 낙관적 동시성 제어를 사용하며, 기존 single-item 연산의 성능을 저하시키지 않으면서 TransactWriteItems/TransactGetItems API를 구현. 트랜잭션과 비트랜잭션 연산이 공존하는 하이브리드 시스템의 설계 과제를 다룸.
