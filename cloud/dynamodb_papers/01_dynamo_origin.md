# DynamoDB의 기원: Amazon Dynamo 논문

## 핵심 논문

### Dynamo: Amazon's Highly Available Key-value Store
- **저자**: Giuseppe DeCandia, Deniz Hastorun, Madan Jampani, Gunavardhan Kakulaparam, Avinash Lakshman, Alex Pilchin, Swaminathan Sivasubramanian, Peter Vosshall, Werner Vogels
- **학회**: **SOSP 2007** (ACM Symposium on Operating Systems Principles)
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)
- **DOI**: 10.1145/1294261.1294281

#### 배경과 문제 정의

Amazon은 2004~2006년경 급격히 성장하는 이커머스 플랫폼에서 다음과 같은 문제에 직면했다:

1. **가용성(Availability)**: 장바구니 서비스는 항상 쓰기가 가능해야 한다 ("always writable")
2. **확장성(Scalability)**: 수백만 동시 사용자를 처리해야 한다
3. **지연시간(Latency)**: 99.9 percentile에서도 300ms 이내 응답을 보장해야 한다
4. **기존 RDBMS의 한계**: 조인, 트랜잭션 등 불필요한 기능이 오버헤드를 유발

#### 6가지 핵심 설계 결정

| 문제 | 기술 | 근거 논문 |
|------|------|----------|
| 데이터 파티셔닝 | Consistent Hashing | Karger et al. (STOC 1997) |
| 고가용 쓰기 | Vector Clocks + 읽기 시 충돌 해결 | Fidge (1988), Mattern (1989) |
| 일시적 장애 처리 | Sloppy Quorum + Hinted Handoff | Gifford (SOSP 1979) 변형 |
| 영구적 장애 복구 | Anti-entropy with Merkle Trees | Merkle (1979) |
| 멤버십/장애 감지 | Gossip-based Protocol | Demers et al. (PODC 1987) |
| 복제 동기화 | Read-repair + Anti-entropy | 자체 설계 |

#### CAP 정리에서의 위치

Dynamo는 **AP 시스템**이다:
- **Availability + Partition Tolerance**를 선택
- **Consistency를 희생**: Eventually Consistent 모델
- 이는 Brewer의 CAP 정리(PODC 2000)에 따른 의도적 선택

#### 영향

- Apache Cassandra의 직접적 모태 (Lakshman이 Facebook으로 이직 후 개발)
- Riak, Voldemort (LinkedIn) 등 오픈소스 구현에 영향
- NoSQL 운동의 학술적 기반 제공

---

## DynamoDB 진화 논문

### Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service
- **저자**: Mostafa Elhemali, Niall Gallagher, Nick Gordon, Joseph Idziorek, Richard Krog, Colin Lazier, Erben Mo, Akhilesh Mritunjai, Somu Perianayagam, Tim Rath, Swami Sivasubramanian, James Christopher Sorenson III, Sroaj Sosothikul, Doug Terry, Akshat Vig
- **학회**: **USENIX ATC 2022**
- **BK21 등급**: 인정 IF **3** (Top-tier 우수)

#### Dynamo(2007)에서 DynamoDB(2022)로의 진화

| 측면 | Dynamo (2007) | DynamoDB (2022) |
|------|--------------|-----------------|
| 일관성 | Eventually Consistent only | Strong + Eventually Consistent 선택 가능 |
| 충돌 해결 | 클라이언트 측 (Vector Clocks) | 서버 측 (Last Writer Wins) |
| 파티셔닝 | Consistent Hashing (가상 노드) | Consistent Hashing + 자동 분할 |
| 관리 | 개발팀이 직접 운영 | 완전 관리형 서비스 |
| 트랜잭션 | 미지원 | ACID 트랜잭션 지원 |
| 스토리지 | 로컬 디스크 | 분리된 스토리지 계층 |

---

### Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service (Transactions)
- **저자**: Joseph Idziorek et al.
- **학회**: **USENIX ATC 2023**
- **BK21 등급**: 인정 IF **3** (Top-tier 우수)

DynamoDB의 트랜잭션 메커니즘을 상세히 기술. Timestamp Ordering 기반의 낙관적 동시성 제어를 사용하며, 기존 single-item 작업의 성능을 저하시키지 않으면서 multi-item ACID 트랜잭션을 구현.

## 참고문헌

1. G. DeCandia et al., "Dynamo: Amazon's Highly Available Key-value Store," *Proc. SOSP*, 2007. [SOSP, BK21 IF=4]
2. M. Elhemali et al., "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service," *Proc. USENIX ATC*, 2022. [ATC, BK21 IF=3]
3. J. Idziorek et al., "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service," *Proc. USENIX ATC*, 2023. [ATC, BK21 IF=3]
