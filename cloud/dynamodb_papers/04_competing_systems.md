# Dynamo와 동시대/경쟁/후속 분산 데이터베이스 시스템: 심층 학술 연구

> 이 문서는 Amazon Dynamo(SOSP 2007)와 경쟁하거나, 영향을 받거나, 함께 진화한 주요 분산 데이터베이스 시스템들에 대한 심층 학술 연구를 담고 있다. 각 시스템에 대해 논문의 전체 서지정보, 기술적 세부사항, 아키텍처, Dynamo와의 비교, 후속 논문을 포함한다.

---

## 목차

1. [Google Bigtable](#1-google-bigtable)
2. [PNUTS / Yahoo Sherpa](#2-pnuts--yahoo-sherpa)
3. [Apache Cassandra](#3-apache-cassandra)
4. [Google Megastore](#4-google-megastore)
5. [Google Spanner](#5-google-spanner)
6. [F1](#6-f1)
7. [Calvin](#7-calvin)
8. [TAO](#8-tao)
9. [CockroachDB](#9-cockroachdb)
10. [TiDB](#10-tidb)
11. [FoundationDB](#11-foundationdb)
12. [Google Percolator](#12-google-percolator)
13. [Azure Cosmos DB](#13-azure-cosmos-db)
14. [Amazon Aurora](#14-amazon-aurora)
15. [Riak](#15-riak)
16. [Project Voldemort](#16-project-voldemort-linkedin)
17. [Apache HBase](#17-apache-hbase)
18. [MongoDB](#18-mongodb)
19. [Redis Cluster](#19-redis-cluster)
20. [ScyllaDB](#20-scylladb)
21. [YugabyteDB](#21-yugabytedb)
22. [Anna KVS](#22-anna-kvs)
23. [SLOG](#23-slog)
24. [Epoxy](#24-epoxy)
25. [Polaris (Azure Synapse)](#25-polaris)
26. [기반 데이터 구조 논문](#26-기반-데이터-구조-논문)
27. [클라우드 네이티브 데이터베이스 서베이](#27-클라우드-네이티브-데이터베이스-서베이)
28. [Amazon DynamoDB 최신 논문](#28-amazon-dynamodb-최신-논문)
29. [비교 요약표](#29-비교-요약표)

---

## 1. Google Bigtable

### 1.1 원본 논문

**Bigtable: A Distributed Storage System for Structured Data**
- **저자**: Fay Chang, Jeffrey Dean, Sanjay Ghemawat, Wilson C. Hsieh, Deborah A. Wallach, Mike Burrows, Tushar Chandra, Andrew Fikes, Robert E. Gruber
- **학회/연도**: OSDI 2006 (7th USENIX Symposium on Operating Systems Design and Implementation)
- **페이지**: 205-218
- **DOI**: 10.1145/1365815.1365816 (ACM TOCS 확장판), 10.5555/1267308.1267323 (OSDI 프로시딩)
- **수상**: OSDI 2006 Best Paper Award
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

Bigtable은 Google이 설계한 대규모 구조화 데이터 분산 스토리지 시스템이다. 데이터 모델은 희소(sparse), 분산(distributed), 영속(persistent) 다차원 정렬 맵(multi-dimensional sorted map)으로, `(row:string, column:string, time:int64) -> string` 형태의 매핑을 제공한다. 행(row)은 사전순으로 정렬되며, 연속된 행 범위가 "태블릿(tablet)"이라는 분산 단위로 분할된다. 각 태블릿은 약 100-200MB 크기이며 하나의 태블릿 서버에 할당된다.

아키텍처적으로 Bigtable은 GFS(Google File System) 위에 구축되며, Chubby 분산 락 서비스를 메타데이터 관리와 마스터 선출에 활용한다. 단일 마스터 서버가 태블릿 서버에 태블릿을 할당하고, 부하 분산과 가비지 컬렉션을 수행한다. 쓰기 경로에서는 먼저 커밋 로그에 기록한 후 메모리의 memtable에 삽입하며, memtable이 임계치에 도달하면 GFS의 SSTable 파일로 플러시한다. 이것이 후에 LSM-tree 아키텍처의 산업계 대규모 적용 사례로 자리잡았다.

Bigtable은 Column Family라는 접근 제어의 기본 단위를 제공하며, 하나의 테이블에 수백 개의 column family를 가질 수 있고, 각 column family 내에 제한 없는 수의 컬럼을 생성할 수 있다. 이러한 "wide column" 모델은 이후 Cassandra, HBase 등에 직접적 영향을 미쳤다.

**Dynamo와 비교**:
| 측면 | Bigtable | Dynamo |
|------|----------|--------|
| 데이터 모델 | Column-family (다차원 정렬 맵) | Key-value (단순 해시맵) |
| 일관성 | Strong consistency (단일 태블릿 서버) | Eventually consistent |
| 아키텍처 | Master-slave (단일 마스터) | Masterless (P2P 링) |
| CAP 선택 | CP (일관성 + 분할내성) | AP (가용성 + 분할내성) |
| 스토리지 의존성 | GFS + Chubby | 자체 완결적 (로컬 디스크) |
| 파티셔닝 | Range-based (사전순 정렬) | Consistent hashing |
| 데이터 정렬 | 행 키 기준 사전순 | 정렬 없음 (해시 기반) |
| 주요 사용처 | 웹 인덱싱, 분석 | 쇼핑카트, 세션 관리 |

### 1.2 후속 논문: Bigtable Merge Compaction

**Bigtable Merge Compaction**
- **저자**: Claire Mathieu, Carl Staelin, Neal E. Young, Arman Yousefi
- **발표**: arXiv:1407.3008, 2014
- **링크**: https://arxiv.org/abs/1407.3008

이 논문은 Bigtable과 같은 NoSQL 데이터베이스의 SSTable 파일 컴팩션 정책을 이론적으로 분석한다. 컴팩션은 여러 SSTable 파일을 병합하여 단일 파일로 만드는 과정으로, 어떤 파일을 언제 합칠지에 따라 계산 비용이 수 자릿수까지 차이날 수 있다. 기존 문헌에 온라인 컴팩션 정책 설계/분석 도구가 부재했던 상황에서 경쟁비(competitive ratio) 분석 프레임워크를 제시했다.

### 1.3 확장 저널 버전

**Bigtable: A Distributed Storage System for Structured Data**
- **학회**: ACM Transactions on Computer Systems (TOCS), Vol. 26, No. 2, Article 4, June 2008
- **DOI**: 10.1145/1365815.1365816

OSDI 원본의 확장 저널 버전으로, 프로덕션 운영 경험과 성능 벤치마크가 추가되었다.

---

## 2. PNUTS / Yahoo Sherpa

### 2.1 원본 논문

**PNUTS: Yahoo!'s Hosted Data Serving Platform**
- **저자**: Brian F. Cooper, Raghu Ramakrishnan, Utkarsh Srivastava, Adam Silberstein, Philip Bohannon, Hans-Arno Jacobsen, Nick Puz, Daniel Weaver, Ramana Yerneni
- **학회/연도**: VLDB 2008 (Proceedings of the VLDB Endowment, Vol. 1, No. 2)
- **페이지**: 1277-1288
- **DOI**: 10.14778/1454159.1454167
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

PNUTS는 Yahoo!가 웹 애플리케이션을 위해 설계한 대규모 병렬, 지리적 분산 데이터베이스 시스템이다. 핵심 혁신은 "타임라인 일관성(timeline consistency)"이라는 레코드 단위의 일관성 모델로, 각 레코드에 대해 하나의 마스터 리전을 지정하고, 해당 마스터가 해당 레코드의 모든 쓰기를 순서화한다. 다른 리전은 비동기적으로 복제본을 수신하되, 항상 동일한 순서로 업데이트가 적용됨을 보장한다.

데이터 모델은 해시(hashed) 또는 순서(ordered) 테이블로 구성되며, 대규모 동시 요청(업데이트 및 쿼리 포함)에 대해 낮은 지연을 제공한다. PNUTS는 Yahoo! Message Broker(YMB)를 기반으로 비동기 복제를 수행하며, 마스터십은 레코드 단위로 동적으로 이동할 수 있어 데이터 지역성을 최적화한다.

아키텍처적으로 각 리전(region)에는 라우터(router), 태블릿 컨트롤러(tablet controller), 스토리지 유닛(storage unit)이 배치된다. 라우터는 해시/순서 파티셔닝 정보를 기반으로 요청을 적절한 스토리지 유닛으로 전달하고, 태블릿 컨트롤러는 태블릿의 분할/이동을 관리한다.

**Dynamo와 비교**: Dynamo는 모든 복제본이 동등하게 쓰기를 받아 충돌이 발생할 수 있는 반면, PNUTS는 레코드별 마스터를 지정하여 충돌을 원천 차단한다. PNUTS의 일관성은 Dynamo보다 강하지만(레코드별 인과적 순서 보장), 마스터 리전 장애 시 해당 레코드의 쓰기가 불가능해지는 가용성 제약이 존재한다. Dynamo는 "always writable"을 최우선으로 하는 AP 시스템인 반면, PNUTS는 일관성과 가용성의 중간 지점을 선택했다.

### 2.2 후속 논문: PNUTS to Sherpa

**PNUTS to Sherpa: Lessons from Yahoo!'s Cloud Database**
- **저자**: Brian F. Cooper, Utkarsh Srivastava, Adam Silberstein, Philip Bohannon, Hans-Arno Jacobsen, Nick Puz, Daniel Weaver, Ramana Yerneni, P.P.S. Narayan, Raghu Ramakrishnan
- **학회/연도**: VLDB 2019 (PVLDB, Vol. 12, No. 12)
- **페이지**: 2300-2313
- **DOI**: 10.14778/3352063.3352146

이 회고 논문은 PNUTS 연구 프로토타입에서 전 세계적으로 배포된 프로덕션 시스템 Sherpa로의 진화 과정에서 얻은 교훈을 공유한다. Sherpa는 1조 개 이상의 레코드와 수 페타바이트의 스토리지를 관리하며, 초당 300만 건 이상의 읽기 피크를 달성했다. 주요 교훈으로는: (1) 원래의 메시징 시스템을 Apache Pulsar로 교체한 점, (2) 일관성 모델의 프로덕션 환경에서의 실질적 문제들, (3) Cosmos DB, DynamoDB, Spanner와의 비교를 포함한다.

---

## 3. Apache Cassandra

### 3.1 원본 논문

**Cassandra: A Decentralized Structured Storage System**
- **저자**: Avinash Lakshman, Prashant Malik
- **학회/연도**: ACM SIGOPS Operating Systems Review, Vol. 44, No. 2, April 2010
- **페이지**: 35-40
- **DOI**: 10.1145/1773912.1773922
- **최초 발표**: LADIS 2009 (Workshop on Large Scale Distributed Systems and Middleware)

**기술적 세부사항**:

Cassandra는 Facebook에서 Inbox Search 기능을 위해 개발된 분산 스토리지 시스템이다. 핵심 설계 철학은 Dynamo의 파티셔닝/복제 아키텍처와 Bigtable의 데이터 모델을 결합하는 것이다. 개발자 Avinash Lakshman은 Amazon에서 Dynamo 논문의 공저자로 참여한 후 Facebook으로 이직하여 Cassandra를 설계했으므로, Cassandra는 Dynamo의 직접적 정신적 후속작이라 할 수 있다.

아키텍처적으로 Cassandra는 완전한 마스터리스(masterless) P2P 시스템이다. Consistent hashing으로 데이터를 파티셔닝하되, 가상 노드(virtual node)를 도입하여 부하 분산을 개선했다. 복제본은 링에서 시계방향으로 다음 N개 노드에 배치되며, 멀티 데이터센터 배치를 위한 네트워크 토폴로지 인식 전략을 지원한다. 장애 감지는 Gossip 프로토콜(phi accrual failure detector)로 수행되며, 해시드 메시지 인증 없이 노드 간 통신이 이루어진다.

쓰기 경로는 Bigtable과 유사한 LSM-tree 구조를 따른다: 커밋 로그 -> memtable -> SSTable 플러시 -> 컴팩션. 읽기 시에는 memtable과 디스크의 SSTable을 병합하여 결과를 반환하며, Bloom filter로 불필요한 디스크 읽기를 줄인다.

Cassandra 2.0(2013년)부터는 Paxos 기반의 경량 트랜잭션(Lightweight Transactions, LWT)을 도입했다. 이는 Compare-and-Set(CAS) 연산을 지원하여 선형화 가능성(linearizability)을 제공하지만, 일반 쓰기 대비 3-4배의 지연이 발생한다. Cassandra의 마스터리스 아키텍처와 Raft의 리더 기반 설계가 충돌하기 때문에, Paxos가 선택되었다.

**Dynamo와 비교**:
| 측면 | Dynamo | Cassandra |
|------|--------|-----------|
| 데이터 모델 | Key-value | Wide-column (Bigtable 스타일) |
| 쿼리 언어 | Get/Put API | CQL (SQL-like) |
| 충돌 해결 | Vector Clocks | Last-Write-Wins (LWW) |
| 멤버십 | Gossip | Gossip |
| 트랜잭션 | 없음 | LWT (Paxos, 2.0+) |
| 일관성 | Tunable (R+W>N) | Tunable (ONE/QUORUM/ALL) |
| 오픈소스 | No | Yes (Apache) |
| 컴팩션 | Merkle tree 기반 anti-entropy | Size-tiered / Leveled |

### 3.2 관련 논문: RAMP-TAO

**RAMP-TAO: Layering Atomic Transactions on Facebook's Online TAO Data Store**
- **저자**: Audrey Cheng, Xiao Shi, Lu Pan, Anthony Simpson, Neil Wheaton, Shilpa Lawande, Nathan Bronson, Peter Bailis, Natacha Crooks, Ion Stoica
- **학회/연도**: VLDB 2021 (PVLDB, Vol. 14, No. 12)
- **페이지**: 3014-3027
- **DOI**: 10.14778/3476311.3476379
- **수상**: VLDB 2021 Best Industry Paper Award

Facebook의 TAO 데이터 스토어 위에 RAMP(Read Atomic Multi-Partition) 프로토콜을 적용하여 원자적 트랜잭션을 제공한다. TAO의 최종 일관성 모델을 확장하면서도 높은 성능을 유지한 점이 핵심 기여이다.

---

## 4. Google Megastore

### 4.1 원본 논문

**Megastore: Providing Scalable, Highly Available Storage for Interactive Services**
- **저자**: Jason Baker, Chris Bond, James C. Corbett, JJ Furman, Andrey Khorlin, James Larson, Jean-Michel Leon, Yawei Li, Alexander Lloyd, Vadim Yushprakh
- **학회/연도**: CIDR 2011 (Conference on Innovative Data Systems Research)
- **페이지**: 223-234
- **BK21 등급**: 미등재 (CIDR는 비공식적이나 영향력 높음)

**기술적 세부사항**:

Megastore는 Google의 인터랙티브 온라인 서비스를 위한 스토리지 시스템으로, NoSQL 데이터스토어의 확장성과 전통적 RDBMS의 편의성을 독특한 방식으로 결합한다. 핵심 개념은 "Entity Group"으로, 관련된 엔티티들을 하나의 그룹으로 묶어 해당 그룹 내에서 ACID 트랜잭션과 강한 일관성을 보장한다.

Entity Group 내부의 트랜잭션은 Paxos 기반의 동기식 복제로 처리된다. 각 Entity Group은 독립적인 Paxos 인스턴스를 가지며, WAN(Wide Area Network)을 통한 동기식 복제로 다수의 데이터센터에 걸쳐 강한 일관성을 제공한다. Entity Group 간의 트랜잭션은 2PC(Two-Phase Commit)나 큐 기반의 비동기 메시지로 처리되며, 이 경우 일관성 보장이 약해진다.

아키텍처적으로 Megastore는 Bigtable 위에 구축된다. Bigtable이 저수준 스토리지를 담당하고, Megastore가 스키마, 인덱스, 트랜잭션, 복제 레이어를 추가한다. 이 계층화된 접근법은 이후 FoundationDB의 "unbundled" 아키텍처에 영향을 미쳤다.

**Dynamo와 비교**: Dynamo는 eventual consistency를 수용하여 "always writable"을 달성한 반면, Megastore는 entity group 내에서 Paxos를 통한 strong consistency를 추구했다. 그러나 Megastore의 쓰기 지연은 WAN Paxos 라운드트립으로 인해 높았으며(수백 밀리초), 이 한계가 이후 Spanner 개발의 동기가 되었다. Megastore는 Dynamo와 Spanner 사이의 과도기적 시스템으로, 강한 일관성의 실용성을 입증하면서도 성능 문제를 드러낸 사례이다.

---

## 5. Google Spanner

### 5.1 원본 논문

**Spanner: Google's Globally-Distributed Database**
- **저자**: James C. Corbett, Jeffrey Dean, Michael Epstein, Andrew Fikes, Christopher Frost, JJ Furman, Sanjay Ghemawat, Andrey Gubarev, Christopher Heiser, Peter Hochschild, Wilson Hsieh, Sebastian Kanthak, Eugene Kogan, Hongyi Li, Alexander Lloyd, Sergey Melnik, David Mwaura, David Nagle, Sean Quinlan, Rajesh Rao, Lindsay Rolig, Yasushi Saito, Michal Szymaniak, Christopher Taylor, Ruth Wang, Dale Woodford
- **학회/연도**: OSDI 2012 (10th USENIX Symposium on Operating Systems Design and Implementation)
- **페이지**: 261-264 (확장: ACM TOCS 2013, Vol. 31, No. 3, Article 8)
- **수상**: Jay Lepreau Best Paper Award; **ACM SIGMOD 2025 Systems Award**
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

Spanner는 Google이 설계한 전 세계적으로 분산된 데이터베이스로, Dynamo와 정반대의 설계 철학을 채택한다. Dynamo가 "always writable"을 위해 일관성을 희생한 반면, Spanner는 "always consistent"를 목표로 외부 일관성(external consistency, linearizability보다 강한 보장)을 달성한다.

핵심 기술 혁신은 **TrueTime API**이다. TrueTime은 GPS 수신기와 원자시계(atomic clock)의 조합으로 구현되며, 각 Google 데이터센터에 배치된다. GPS와 원자시계는 독립적인 장애 모드를 가져(GPS는 전파 간섭에, 원자시계는 기계적 고장에 취약), 하나가 실패해도 다른 것이 시간 정보를 제공한다. TrueTime은 현재 시간을 단일 값이 아닌 불확실성 구간 [earliest, latest]으로 반환하며, 실제 불확실성은 1-7ms 범위이다.

트랜잭션 커밋 시, Spanner는 TrueTime에서 도출된 커밋 타임스탬프를 할당한 후, "커밋 대기(commit wait)"를 수행하여 해당 타임스탬프가 확실히 과거가 될 때까지 기다린다. 이 대기 시간은 트랜잭션을 내구화(durable)하는 과정과 중첩되어 오버헤드를 최소화한다. 이를 통해 어떤 두 트랜잭션의 실행 순서가 커밋 타임스탬프의 순서와 일치함을 보장한다(external consistency).

데이터는 "directory"라는 단위로 파티셔닝되며, 각 디렉토리는 Paxos 기반의 스팬서버(spanserver) 그룹에 의해 복제된다. 각 Paxos 그룹의 리더는 장기 리더(long-lived leader)로, 리더 임기 동안 낙관적으로 트랜잭션을 처리한다. 읽기 전용 트랜잭션은 어떤 복제본에서도 잠금 없이 실행될 수 있어 높은 읽기 처리량을 제공한다.

**Dynamo와 비교 — 가장 극명한 대조**:
| 측면 | Dynamo | Spanner |
|------|--------|---------|
| 일관성 | Eventually consistent | Externally consistent (linearizable+) |
| CAP 선택 | AP | 기술적 CP, 실질적 CA에 가까움 |
| 시간 동기화 | Vector Clocks (논리적) | TrueTime (물리적, GPS+원자시계) |
| 트랜잭션 | 없음 | 전역 ACID (2PC over Paxos) |
| 스키마 | Schema-free | Schema-required (반정규화된 관계형) |
| 설계 철학 | "Always writable" | "Always consistent" |
| 파티셔닝 | Consistent hashing | Directory-based (범위/해시 혼합) |
| 하드웨어 의존 | 범용 서버 | GPS/원자시계 특수 하드웨어 |
| 읽기 모델 | 쿼럼 읽기 | 스냅샷 읽기 (잠금 불필요) |

### 5.2 후속 논문: Spanner: Becoming a SQL System

**Spanner: Becoming a SQL System**
- **저자**: David F. Bacon, Nathan Bales, Nico Bruno, Brian F. Cooper, Adam Dickinson, Andrew Fikes, Campbell Fraser, Andrey Gubarev, Milind Joshi, Eugene Kogan, Alexander Lloyd, Sergey Melnik, Rajesh Rao, David Shue, Christopher Taylor, Marcel van der Holst, Dale Woodford
- **학회/연도**: SIGMOD 2017
- **DOI**: 10.1145/3035918.3056103

이 논문은 Spanner가 NoSQL 키-값 스토어에서 완전한 SQL 시스템으로 진화한 과정을 설명한다. 주요 변화: (1) 분산 SQL 쿼리 엔진 추가, (2) 강한 타입의 스키마 시스템, (3) 분산 쿼리 실행의 최적화(쿼리 재시작, 범위 추출, 쿼리 재개), (4) Spanner 위에 구축된 수백 개의 미션 크리티컬 Google 서비스 경험.

### 5.3 후속 논문: Spanner, TrueTime and the CAP Theorem

**Spanner, TrueTime and the CAP Theorem**
- **저자**: Eric A. Brewer
- **발표**: Google Research Technical Report, February 2017
- **링크**: https://research.google.com/pubs/archive/45855.pdf

CAP 정리의 창시자인 Eric Brewer가 직접 Spanner의 CAP 분류를 논한다. 결론: Spanner는 기술적으로 CP 시스템이다(파티션 발생 시 일관성을 선택하고 가용성을 포기). 그러나 Google의 사설 네트워크에서 실제 파티션 발생 빈도가 극히 낮고, TrueTime의 불확실성이 매우 작아, 사용자 관점에서는 "효과적으로 CA"에 가깝게 운영된다.

---

## 6. F1

### 6.1 원본 논문

**F1: A Distributed SQL Database That Scales**
- **저자**: Jeff Shute, Radek Vingralek, Bart Samwel, Ben Handy, Chad Whipkey, Eric Rollins, Mircea Oancea, Kyle Littlefield, David Menestrina, Stephan Ellner, John Cieslewicz, Ian Rae, Traian Stancescu, Himani Apte
- **학회/연도**: VLDB 2013 (PVLDB, Vol. 6, No. 11)
- **페이지**: 1068-1079
- **DOI**: 10.14778/2536222.2536232
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

F1은 Google AdWords 비즈니스를 지원하기 위해 Spanner 위에 구축된 분산 관계형 데이터베이스이다. 기존 MySQL 기반 샤딩 시스템을 대체하기 위해 설계되었으며, NoSQL 시스템의 확장성과 전통적 SQL 데이터베이스의 일관성 및 편의성을 결합한다.

F1은 Spanner의 동기식 교차 데이터센터 복제와 강한 일관성을 기반으로, 완전한 분산 SQL 쿼리 엔진, 자동 변경 추적(change tracking) 및 게시(publishing) 기능을 제공한다. 계층적(hierarchical) 스키마를 지원하여 관련 테이블을 물리적으로 인접하게 배치함으로써 조인 성능을 최적화한다.

**Dynamo와 비교**: F1/Spanner 조합은 Dynamo의 "일관성을 희생한 가용성" 접근법의 정반대편에 위치한다. Google은 AdWords와 같은 금융 트랜잭션에서 강한 일관성이 절대적으로 필요하다고 판단하여, 지연 시간 증가를 감수하면서도 전역 ACID 트랜잭션을 선택했다.

### 6.2 후속 논문: F1 Query

**F1 Query: Declarative Querying at Scale**
- **학회/연도**: VLDB 2018 (PVLDB, Vol. 11, No. 12)
- **DOI**: 10.14778/3229863.3229871

F1의 쿼리 처리 엔진을 독립적 시스템으로 분리하여, Spanner뿐만 아니라 Bigtable, CSV, Capacitor 컬럼나 등 다양한 데이터 소스를 통합 쿼리할 수 있게 한 논문이다.

---

## 7. Calvin

### 7.1 원본 논문

**Calvin: Fast Distributed Transactions for Partitioned Database Systems**
- **저자**: Alexander Thomson, Thaddeus Diamond, Shu-Chun Weng, Kun Ren, Philip Shao, Daniel J. Abadi
- **학회/연도**: SIGMOD 2012
- **DOI**: 10.1145/2213836.2213838
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

Calvin은 분산 트랜잭션의 근본적 병목인 분산 합의 비용을 제거하기 위해 결정론적(deterministic) 실행을 도입한 시스템이다. 핵심 통찰은: 모든 복제본이 동일한 트랜잭션 입력을 동일한 순서로 받으면, 결정론적 잠금 프로토콜 하에서 동일한 결과를 산출한다는 것이다.

Calvin의 아키텍처는 세 계층으로 구성된다: (1) **Sequencing Layer** - 전체 시스템에 걸쳐 트랜잭션의 전역 순서를 결정하고 이를 모든 복제본에 복제, (2) **Scheduling Layer** - 결정된 순서에 따라 트랜잭션을 잠금 관리자에 제출하고 다중 파티션 트랜잭션을 조율, (3) **Storage Layer** - 실제 데이터를 저장하고 CRUD 연산을 수행.

중요한 제약은 트랜잭션의 읽기/쓰기 세트(read/write set)가 실행 전에 알려져야 한다는 것이다. 이를 통해 sequencing layer가 트랜잭션 간 의존성을 미리 분석하고, 독립적인 트랜잭션을 병렬로 실행할 수 있다. 읽기/쓰기 세트를 미리 알 수 없는 트랜잭션은 "정찰(reconnaissance)" 쿼리를 먼저 실행하여 세트를 결정한 후 본 트랜잭션을 제출한다.

**Dynamo와 비교**: Calvin은 strong consistency + 고성능 트랜잭션을 목표로 하며, Dynamo의 AP 접근과는 정반대이다. Dynamo는 충돌을 허용하고 사후에 해결하는 반면, Calvin은 결정론적 순서화로 충돌 자체를 원천 차단한다. Calvin의 아이디어는 FoundationDB에 영향을 미쳤다.

### 7.2 후속 논문: The Case for Determinism

**The Case for Determinism in Database Systems**
- **저자**: Alexander Thomson, Daniel J. Abadi
- **학회/연도**: VLDB 2010 (PVLDB, Vol. 3, No. 1)
- **페이지**: 70-80
- **DOI**: 10.14778/1920841.1920855

Calvin의 이론적 기반을 마련한 논문으로, 결정론적 데이터베이스 시스템이 복제를 단순화하고 분산 커밋 프로토콜을 제거할 수 있음을 주장한다.

### 7.3 후속 논문: Calvin 평가

**An Evaluation of the Advantages and Disadvantages of Deterministic Database Systems**
- **저자**: Kun Ren, Alexander Thomson, Daniel J. Abadi
- **학회/연도**: VLDB 2014 (PVLDB, Vol. 7, No. 10)
- **페이지**: 821-832
- **DOI**: 10.14778/2732951.2732955

결정론적 데이터베이스의 장단점을 실험적으로 심층 평가한다. 장점: 교착 상태 회피, 분산 커밋 프로토콜 감소, 경량 잠금. 단점: 사전에 읽기/쓰기 세트를 알 수 없는 트랜잭션에 대한 추가 오버헤드.

### 7.4 후속 논문: Aria

**Aria: A Fast and Practical Deterministic OLTP Database**
- **저자**: Yi Lu, Xiangyao Yu, Lei Cao, Samuel Madden
- **학회/연도**: VLDB 2020 (PVLDB, Vol. 13, No. 12)
- **페이지**: 2047-2060
- **DOI**: 10.14778/3407790.3407808

Calvin의 핵심 제약(트랜잭션 읽기/쓰기 세트를 사전에 알아야 함)을 해결한 후속 시스템이다. Aria는 배치(batch) 내의 트랜잭션을 동일한 데이터베이스 스냅샷에 대해 먼저 실행(execution phase)한 후, 직렬화 가능성을 보장하면서 결정론적으로 커밋할 트랜잭션을 선택(commit phase)한다. Calvin 대비 최대 1.7배 높은 처리량을 달성했다.

### 7.5 후속 논문: SLOG (아래 별도 섹션 참조)

### 7.6 후속 논문: An Overview of Deterministic Database Systems

**An Overview of Deterministic Database Systems**
- **저자**: Daniel J. Abadi
- **학회/연도**: Communications of the ACM, 2018
- **DOI**: 10.1145/3181853

결정론적 데이터베이스 시스템의 종합 개요로, Calvin, BOHM, FaunaDB 등의 시스템을 포괄적으로 다룬다.

---

## 8. TAO

### 8.1 원본 논문

**TAO: Facebook's Distributed Data Store for the Social Graph**
- **저자**: Nathan Bronson, Zach Amsden, George Cabrera, Prasad Chakka, Peter Dimov, Hui Ding, Jack Ferris, Anthony Giardullo, Sachin Kulkarni, Harry Li, Mark Marchukov, Dmitri Petrov, Lovro Puzar, Yee Jiun Song, Venkat Venkataramani
- **학회/연도**: USENIX ATC 2013 (USENIX Annual Technical Conference)
- **페이지**: 49-60
- **ISBN**: 978-1-931971-01-0
- **BK21 등급**: 인정 IF **3** (Top-tier 우수)

**기술적 세부사항**:

TAO(The Associations and Objects)는 Facebook의 소셜 그래프를 서비스하기 위해 설계된 분산 데이터 스토어이다. Facebook의 소셜 그래프는 수십억 개의 노드(사용자, 페이지, 사진 등)와 수조 개의 엣지(친구 관계, 좋아요, 댓글 등)로 구성되며, TAO는 이 그래프에 대한 읽기 최적화된 접근을 제공한다.

데이터 모델은 두 가지 기본 타입으로 구성된다: **Object**(노드, 타입이 지정된 key-value 쌍)와 **Association**(방향성 있는 타입 지정 엣지). 각 Association은 (id1, atype, id2) 트리플로 식별되며, 시간순 정렬된 연관 리스트를 효율적으로 쿼리할 수 있다.

아키텍처는 다층 캐시 구조이다: (1) **Leader cache** - 각 리전에 하나, MySQL 데이터베이스에서 읽어온 데이터를 캐시, (2) **Follower cache** - 리더 캐시 앞에 위치, 읽기 요청을 처리하고 캐시 미스 시 리더에 전달, (3) **MySQL 스토리지** - 최종 영속 스토리지. 쓰기는 리더 캐시를 통해 MySQL에 동기식으로 기록되며, 변경 사항은 follower와 다른 리전에 비동기적으로 전파된다.

TAO는 초당 10억 건 이상의 읽기 요청을 처리하며, 읽기:쓰기 비율은 약 500:1이다. 이러한 극단적인 읽기 편향 워크로드에 최적화된 설계이다.

**Dynamo와 비교**: TAO와 Dynamo는 근본적으로 다른 문제를 해결한다. Dynamo는 범용 key-value 스토어로 읽기/쓰기 균형 워크로드를 대상으로 하며, TAO는 그래프 특화 시스템으로 극단적 읽기 편향 워크로드에 최적화되어 있다. Dynamo는 마스터리스 P2P 아키텍처인 반면, TAO는 리더/폴로워 캐시 계층과 MySQL 백엔드를 가진 명시적 계층 구조이다. 일관성 측면에서 TAO는 eventual consistency를 제공하되, "read-after-write" 일관성을 리더 캐시 내에서 보장한다.

### 8.2 후속 논문: TAOBench

**TAOBench: An End-to-End Benchmark for Social Network Workloads**
- **저자**: Audrey Cheng et al.
- **학회/연도**: VLDB 2022 (PVLDB, Vol. 15, No. 9)
- **페이지**: 1965-1977
- **DOI**: 10.14778/3538598.3538616

TAO 스타일 소셜 네트워크 워크로드를 위한 종단간 벤치마크를 제안한다.

---

## 9. CockroachDB

### 9.1 원본 논문

**CockroachDB: The Resilient Geo-Distributed SQL Database**
- **저자**: Rebecca Taft, Irfan Sharif, Andrei Matei, Nathan VanBenschoten, Jordan Lewis, Tobias Grieger, Kai Niemi, Andy Woods, Anne Birzin, Raphael Poss, Paul Bardea, Amruta Ranade, Ben Darnell, Bram Gruneir, Justin Jaffray, Lucy Zhang, Peter Mattis
- **학회/연도**: SIGMOD 2020
- **DOI**: 10.1145/3318464.3386134
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

CockroachDB는 Google Spanner의 설계를 오픈소스로 구현한 지리적 분산 SQL 데이터베이스이다. Spanner와의 가장 큰 차이점은 TrueTime(GPS/원자시계) 대신 **하이브리드 논리 시계(Hybrid Logical Clock, HLC)**를 사용한다는 것이다. HLC는 물리적 시계와 논리적 카운터를 결합하여, NTP 기반의 시간 동기화만으로도 인과적 순서를 보장한다.

아키텍처는 세 계층으로 나뉜다: (1) **SQL 계층** - PostgreSQL 호환 와이어 프로토콜과 SQL 파서/옵티마이저/실행기, (2) **트랜잭션 계층** - 직렬화 가능한 스냅샷 격리(Serializable Snapshot Isolation, SSI)를 구현, (3) **복제/분산 계층** - Raft 합의 프로토콜로 데이터 복제.

데이터는 키 범위(range) 단위로 파티셔닝되며, 각 범위는 Raft 그룹에 의해 복제된다. CockroachDB는 범위의 리스(lease)를 관리하여, 리스 보유 노드가 해당 범위의 모든 읽기를 로컬에서 처리할 수 있게 한다. 쓰기는 Raft를 통해 다수결 합의 후 커밋된다.

트랜잭션 모델에서 CockroachDB는 Spanner의 lock-based 접근 대신, 낙관적 동시성 제어(OCC)와 비관적 잠금을 상황에 따라 혼합한다. 읽기-쓰기 충돌은 타임스탬프 재시작(timestamp restart)으로 해결하고, 쓰기-쓰기 충돌은 잠금으로 처리한다.

**Dynamo와 비교**: CockroachDB는 Dynamo의 AP 접근법과 정반대인 CP 시스템이다. Dynamo가 해시 기반 파티셔닝으로 범위 스캔이 어려운 반면, CockroachDB는 정렬된 키 범위로 효율적인 범위 쿼리를 지원한다. Dynamo가 단순 get/put API만 제공하는 것과 달리, CockroachDB는 완전한 SQL을 지원한다. 또한 Dynamo는 충돌을 허용하고 벡터 클록으로 감지하는 반면, CockroachDB는 SSI로 충돌을 사전 방지한다.

---

## 10. TiDB

### 10.1 원본 논문

**TiDB: A Raft-based HTAP Database**
- **저자**: Dongxu Huang, Qi Liu, Qiu Cui, Zhuhe Fang, Xiaoyu Ma, Fei Xu, Li Shen, Liu Tang, Yuxing Zhou, Menglong Huang, Wan Wei, Cong Liu, Jian Zhang, Jianjun Li, Xuelian Wu, Lingyu Song, Ruoxi Sun, Shuaipeng Yu, Lei Zhao, Nicholas Cameron, Liquan Pei, Xin Tang
- **학회/연도**: VLDB 2020 (PVLDB, Vol. 13, No. 12)
- **페이지**: 3072-3084
- **DOI**: 10.14778/3415478.3415535
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

TiDB는 PingCAP이 개발한 Raft 기반 분산 HTAP(Hybrid Transactional/Analytical Processing) 데이터베이스이다. HTAP란 동일한 데이터에 대해 OLTP(온라인 트랜잭션 처리)와 OLAP(온라인 분석 처리)를 동시에 수행할 수 있는 능력을 의미한다.

아키텍처는 세 핵심 컴포넌트로 구성된다: (1) **TiDB 서버** - MySQL 호환 SQL 계층으로, 클라이언트 연결을 받아 SQL을 파싱, 최적화, 실행, (2) **TiKV** - 분산 트랜잭셔널 KV 스토어로, Raft 합의를 통해 데이터를 복제하고 Google Percolator의 트랜잭션 모델을 구현, (3) **TiFlash** - 컬럼나 스토리지 엔진으로, Raft Learner 메커니즘을 통해 TiKV의 데이터를 실시간으로 복제받아 OLAP 쿼리를 처리.

TiKV의 트랜잭션 모델은 Google Percolator에서 직접 영감을 받았다. 2단계 커밋(2PC) 프로토콜을 사용하되, Percolator의 스냅샷 격리(Snapshot Isolation)를 구현한다. 타임스탬프 오라클(Timestamp Oracle, TSO)이 전역적으로 단조 증가하는 타임스탬프를 발급하며, 각 트랜잭션은 시작 시점과 커밋 시점의 타임스탬프를 받는다.

TiDB의 HTAP 접근법에서 핵심 혁신은 Raft Learner를 활용한 실시간 행-열 변환이다. TiFlash는 Raft 그룹의 Learner 역할로 참여하여, 행 기반 TiKV 데이터를 실시간으로 컬럼나 형식으로 변환한다. 쿼리 옵티마이저는 비용 기반으로 TiKV(행 스토어)와 TiFlash(열 스토어)를 선택하거나 혼합한다.

**Dynamo와 비교**: TiDB는 Dynamo와 근본적으로 다른 설계 목표를 가진다. Dynamo는 AP 시스템으로 단순 get/put 워크로드에 최적화된 반면, TiDB는 CP 시스템으로 완전한 SQL과 ACID 트랜잭션을 제공한다. TiDB의 Percolator 기반 트랜잭션 모델은 Dynamo의 eventual consistency보다 훨씬 강한 스냅샷 격리를 보장한다. 또한 TiDB는 HTAP 지원으로 분석 쿼리도 처리할 수 있어, Dynamo의 순수 OLTP 영역을 넘어선다.

---

## 11. FoundationDB

### 11.1 원본 논문

**FoundationDB: A Distributed Unbundled Transactional Key Value Store**
- **저자**: Jingyu Zhou, Meng Xu, Alexander Shraer, Bala Namasivayam, Alex Miller, Evan Tschannen, Steve Atherton, Andrew J. Beamon, Rusty Sears, John Leach, Dave Rosenthal, Xin Dong, Will Wilson, Ben Collins, David Scherer, Alec Grieser, Young Liu, Alvin Moore, Bhaskar Muppana, Xiaoge Su, Vishesh Yadav
- **학회/연도**: SIGMOD 2021
- **DOI**: 10.1145/3448016.3457559
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

FoundationDB(FDB)는 Apple이 인수하여 오픈소스화한 분산 트랜잭셔널 키-값 스토어이다. 가장 독특한 설계 원칙은 **"unbundled" 아키텍처**와 **결정론적 시뮬레이션 테스트**이다.

Unbundled 아키텍처란 트랜잭션 처리, 로깅, 스토리지를 독립적인 프로세스로 분리한 것이다. 이를 통해 각 계층을 독립적으로 확장하고 장애를 격리할 수 있다. 구체적으로: (1) **Coordinator** - 시스템 메타데이터 관리, (2) **Cluster Controller** - 클러스터 상태 모니터링, (3) **Sequencer** - 트랜잭션 순서를 결정하는 타임스탬프 발급, (4) **Proxy** - 클라이언트 트랜잭션을 받아 충돌 감지 및 커밋 처리, (5) **Resolver** - 트랜잭션 간 충돌(읽기-쓰기 충돌) 감지, (6) **Log Server** - WAL(Write-Ahead Log) 저장, (7) **Storage Server** - 데이터 서빙.

결정론적 시뮬레이션 테스트는 FDB 개발 초기부터 채택된 혁신적 테스트 방법론이다. 실제 데이터베이스 소프트웨어를 결정론적 이산 사건 시뮬레이션 내에서 실행하되, 무작위 합성 워크로드와 장애 주입을 결합한다. 모든 I/O, 네트워크, 시간이 시뮬레이션으로 제어되므로, 어떤 버그든 동일한 시드로 정확히 재현할 수 있다. 이 접근법은 FDB를 극도로 안정적인 시스템으로 만들었으며, 새 기능을 빠르게 릴리스할 수 있게 했다.

트랜잭션 모델은 OCC(Optimistic Concurrency Control)을 사용하며, 직렬화 가능한 스냅샷 격리를 보장한다. 트랜잭션 크기는 10MB, 실행 시간은 5초로 제한된다.

**Dynamo와 비교**: FoundationDB는 Calvin처럼 결정론적 트랜잭션 처리를 채택하며, Dynamo의 AP 접근과 대비되는 CP 시스템이다. Dynamo가 자체 완결적 시스템인 반면, FDB는 unbundled 아키텍처로 다양한 데이터 모델을 "레이어"로 구현할 수 있다(예: Record Layer, Document Layer). Dynamo가 Vector Clock으로 충돌을 사후 감지하는 반면, FDB는 OCC + Resolver로 충돌을 커밋 시점에 즉시 감지하고 중단시킨다.

### 11.2 후속 논문: FoundationDB Record Layer

**FoundationDB Record Layer: A Multi-Tenant Structured Datastore**
- **저자**: Christos Chrysafis, Ben Collins, Scott Dugas, Jay Dunkelberger, Moussa Ehsan, Scott Gray, Alec Grieser, Ori Herrnstadt, Kfir Lev-Ari, Tao Lin, Mike McMahon, Nicholas Schiefer, Alexander Shraer
- **학회/연도**: SIGMOD 2019
- **DOI**: 10.1145/3299869.3314039

FoundationDB 위에 구축된 구조화된 데이터 레이어로, Protocol Buffers 기반 스키마를 사용하여 멀티 테넌트 환경에서 관계형 스타일의 데이터 관리를 제공한다. Apple의 CloudKit(iCloud 백엔드)이 이 Record Layer 위에서 운영된다. 이 논문은 FDB의 "unbundled" 철학 - 핵심 KV 스토어 위에 다양한 데이터 모델을 레이어로 구현 - 의 실제 적용 사례를 보여준다.

---

## 12. Google Percolator

### 12.1 원본 논문

**Large-scale Incremental Processing Using Distributed Transactions and Notifications**
- **저자**: Daniel Peng, Frank Dabek
- **학회/연도**: OSDI 2010 (9th USENIX Symposium on Operating Systems Design and Implementation)
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

Percolator는 Google 웹 검색 인덱스의 증분 처리를 위해 설계된 시스템이다. 기존의 배치 기반 인덱싱 시스템(MapReduce)을 Percolator 기반의 증분 처리로 교체함으로써, 동일한 수의 문서를 처리하면서도 Google 검색 결과의 평균 문서 연령을 50% 줄였다.

Percolator의 핵심 기여는 Bigtable 위에서 **ACID 스냅샷 격리(Snapshot Isolation) 트랜잭션**을 구현한 것이다. 트랜잭션 프로토콜은 2단계 커밋(2PC)을 사용하되, Bigtable의 단일행 트랜잭션을 빌딩 블록으로 활용한다.

구현에서는 세 개의 Bigtable 컬럼 패밀리를 사용한다:
- **data** - 실제 데이터 값
- **lock** - 트랜잭션 진행 중임을 표시하는 잠금
- **write** - 커밋된 데이터의 타임스탬프를 기록

**Prewrite 단계**: 트랜잭션이 수정하는 모든 셀에 잠금을 획득하고 데이터를 write(임시). 하나의 셀을 "프라이머리(primary)"로 지정하여 동기화 지점으로 사용.

**Commit 단계**: 프라이머리 잠금을 제거하고 write 레코드를 기록하여 커밋 완료를 표시. 이후 나머지 셀의 잠금도 비동기적으로 제거.

타임스탬프 오라클(Timestamp Oracle)이 전역적으로 단조 증가하는 타임스탬프를 발급하며, 이를 통해 스냅샷 읽기와 충돌 감지가 이루어진다.

**Dynamo와 비교**: Percolator는 Bigtable 위에 트랜잭션을 추가한 시스템으로, Dynamo와는 직접적 비교 대상이 아니다. 그러나 Percolator의 트랜잭션 모델은 TiDB/TiKV에 직접 채택되어, Dynamo의 AP 접근과 대비되는 강한 일관성 트랜잭션 시스템의 기반이 되었다. Dynamo가 충돌을 허용하고 vector clock으로 감지하는 반면, Percolator는 잠금과 타임스탬프로 충돌을 방지한다.

**TiKV에서의 적용**: TiKV는 Percolator의 트랜잭션 프로토콜을 최적화하여 구현했다. 주요 최적화로는 (1) 병렬 Prewrite, (2) 비동기 Commit, (3) 1PC(단일 리전 트랜잭션의 1단계 커밋) 등이 있다. CF_WRITE 컬럼 패밀리에는 Put, Delete, Rollback, Lock 네 가지 레코드 타입이 존재한다.

---

## 13. Azure Cosmos DB

### 13.1 학술 논문: Schema-Agnostic Indexing

**Schema-Agnostic Indexing with Azure DocumentDB**
- **저자**: Dharma Shukla, Shireesh Thota, Karthik Raman, et al. (Microsoft)
- **학회/연도**: VLDB 2015 (PVLDB, Vol. 8, No. 12)
- **페이지**: 1668-1679
- **링크**: https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf

**기술적 세부사항**:

Azure Cosmos DB(이전 DocumentDB)는 Microsoft가 2010년부터 "Project Florence"로 시작한 전역 분산 데이터베이스 서비스이다. Dharma Shukla가 주도했으며, 핵심 혁신은 다중 일관성 모델, 다중 데이터 모델, 턴키(turnkey) 전역 분산이다.

이 VLDB 2015 논문은 Cosmos DB의 인덱싱 서브시스템을 설명한다. 모든 문서를 JSON 트리로 모델링하고, 스키마나 보조 인덱스를 요구하지 않고 자동으로 모든 경로(path)를 인덱싱한다. Bw-tree를 기반으로 한 잠금-프리(latch-free) 인덱스 구조를 사용하여 높은 쓰기 처리량을 달성한다.

Cosmos DB의 가장 독특한 특징은 **다섯 가지 일관성 수준**이다: Strong, Bounded Staleness, Session, Consistent Prefix, Eventual. 이는 CAP 정리의 이분법을 넘어서, 일관성을 스펙트럼으로 제공하는 최초의 상용 서비스이다. 각 일관성 수준은 TLA+(Temporal Logic of Actions)로 형식적으로 명세되었다.

전역 분산은 "turnkey" 방식으로 제공된다: 클릭 하나로 전 세계 Azure 리전에 데이터를 복제할 수 있으며, 복제 토폴로지 설정이나 데이터센터 관리가 불필요하다. 다중 마스터(multi-master) 쓰기를 지원하여, Dynamo처럼 모든 리전에서 쓰기가 가능하다.

**Dynamo와 비교**: Cosmos DB는 여러 측면에서 Dynamo의 정신적 후속작이면서도 진화된 시스템이다. 둘 다 "always writable" 다중 마스터 접근을 지원하지만, Cosmos DB는 이에 더해 5단계 일관성 스펙트럼을 제공한다. Dynamo는 vector clock 기반 충돌 해결에 의존하는 반면, Cosmos DB는 자동 충돌 해결(LWW 또는 사용자 정의 병합)과 다양한 일관성 보장을 제공한다.

### 13.2 VLDB 2019 초청 강연

Dharma Shukla가 VLDB 2019에서 Cosmos DB의 설계와 전 세계적 운영 경험을 발표했다. 이는 정식 논문이 아닌 초청 산업 강연(Invited Industry Talk)이었다.

---

## 14. Amazon Aurora

### 14.1 SIGMOD 2017 논문

**Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases**
- **저자**: Alexandre Verbitski, Anurag Gupta, Debanjan Saha, Murali Brahmadesam, Kamal Gupta, Raman Mittal, Sailesh Krishnamurthy, Sandor Maurice, Tengiz Kharatishvili, Xiaofeng Bao
- **학회/연도**: SIGMOD 2017
- **페이지**: 1041-1052
- **DOI**: 10.1145/3035918.3056101
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)

**기술적 세부사항**:

Amazon Aurora는 AWS의 클라우드 네이티브 관계형 데이터베이스 서비스로, MySQL 및 PostgreSQL 호환이다. 핵심 혁신은 **"로그가 데이터베이스다(the log is the database)"** 원칙이다. 전통적 데이터베이스가 데이터 페이지를 네트워크를 통해 복제하는 것과 달리, Aurora는 로그 레코드만 스토리지 노드에 전송하고, 스토리지 노드가 로컬에서 데이터 페이지를 재구성한다.

이를 통해 네트워크 트래픽을 대폭 줄이고(MySQL 대비 네트워크 I/O 1/6), 쓰기 처리량을 5배 향상시켰다. 스토리지는 3개 가용 영역(AZ)에 걸쳐 6개의 복제본을 유지하며, 4/6 쓰기 쿼럼과 3/6 읽기 쿼럼을 사용한다.

아키텍처적으로 Aurora는 컴퓨트와 스토리지를 분리(disaggregate)한다. 컴퓨트 노드(Aurora 인스턴스)는 SQL 처리, 트랜잭션 관리, 캐시를 담당하고, 스토리지 노드는 "Protection Group"이라는 10GB 단위의 세그먼트를 관리한다. 스토리지 노드는 Gossip 프로토콜을 사용하여 누락된 로그 레코드를 상호 보완한다.

**DynamoDB와의 차이**: Aurora와 DynamoDB는 동일한 AWS 내에서 근본적으로 다른 사용 사례를 대상으로 한다. Aurora는 관계형 데이터 모델, SQL, ACID 트랜잭션이 필요한 전통적 애플리케이션을 위한 것이고, DynamoDB는 키-값/문서 모델로 극대의 확장성이 필요한 워크로드를 위한 것이다. Aurora는 단일 리전 내에서 고가용성을 제공하지만 멀티 리전 쓰기에는 제약이 있고, DynamoDB는 전역 테이블로 멀티 리전 쓰기를 지원한다.

### 14.2 SIGMOD 2018 논문

**Amazon Aurora: On Avoiding Distributed Consensus for I/Os, Commits, and Membership Changes**
- **저자**: Alexandre Verbitski, Anurag Gupta, Debanjan Saha, James Corey, Kamal Gupta, Murali Brahmadesam, Raman Mittal, Sailesh Krishnamurthy, Sandor Maurice, Tengiz Kharatishvili, Xiaofeng Bao
- **학회/연도**: SIGMOD 2018
- **페이지**: 789-796
- **DOI**: 10.1145/3183713.3196937

이 후속 논문은 Aurora가 어떻게 대부분의 상황에서 분산 합의를 회피하는지 설명한다. 불변량(invariant)을 설정하고 로컬 일시적 상태(local transient state)를 활용하여, Paxos나 Raft 같은 합의 프로토콜 없이도 I/O, 커밋, 멤버십 변경을 처리한다. 이를 통해 성능을 높이고 지연을 줄이며 비용을 절감한다.

---

## 15. Riak

### 15.1 관련 논문

**Riak Core: Building Distributed Applications Without Shared State**
- **저자**: Rusty Klophaus
- **학회/연도**: ACM SIGPLAN Commercial Users of Functional Programming (CUFP 2010)
- **링크**: Erlang Factory conference

**기술적 세부사항**:

Riak은 Basho Technologies가 개발한 Erlang 기반의 Dynamo 스타일 NoSQL 데이터베이스이다. Amazon의 Dynamo 논문에서 직접적 영감을 받았으며, 해당 논문의 핵심 기술들을 오픈소스로 구현했다.

Riak의 핵심은 "Riak Core"라는 라이브러리로, 분산 애플리케이션 실행의 기본 구조를 제공한다. 중앙 코디네이터나 공유 상태 없이 클러스터를 관리하며, 노드 참여/이탈/장애/복구 시 자동으로 클러스터를 재구성한다.

Dynamo에서 가져온 핵심 기술:
- **Consistent Hashing**: 160비트 링에 데이터와 노드를 매핑
- **Virtual Nodes**: 각 물리 노드가 여러 가상 노드를 담당
- **Sloppy Quorum + Hinted Handoff**: 장애 노드의 데이터를 임시로 다른 노드가 대행
- **Read Repair + Active Anti-entropy**: Merkle tree 기반 데이터 동기화
- **Vector Clocks**: 인과적 순서 추적 (이후 dotted version vectors로 진화)

Riak은 순수 학술 논문이 상대적으로 적지만, Dynamo 논문의 아이디어를 가장 충실하게 구현한 시스템으로 평가된다. Riak이 특히 주목받은 영역은 CRDTs(Conflict-free Replicated Data Types)의 실용적 적용으로, eventual consistency 환경에서 자동 충돌 해결을 제공한다.

**Dynamo와의 관계**: Riak은 Dynamo의 가장 직접적인 오픈소스 구현체이다. Dynamo 논문의 거의 모든 기술(consistent hashing, vector clocks, sloppy quorum, hinted handoff, merkle tree anti-entropy)을 구현했으며, 여기에 CRDT, 검색(Solr 통합), 맵리듀스 등을 추가했다.

---

## 16. Project Voldemort (LinkedIn)

### 16.1 핵심 논문

**Serving Large-scale Batch Computed Data with Project Voldemort**
- **저자**: Roshan Sumbaly, Jay Kreps, Lei Gao, Alex Feinberg, Chinmay Soman, Sam Shah
- **학회/연도**: USENIX FAST 2012 (10th USENIX Conference on File and Storage Technologies)
- **링크**: https://static.usenix.org/events/fast12/tech/full_papers/Sumbaly.pdf

**기술적 세부사항**:

Project Voldemort는 LinkedIn이 개발한 Amazon Dynamo에서 영감받은 분산 키-값 스토어이다. Jay Kreps(이후 Apache Kafka의 창시자)가 주도했으며, LinkedIn의 소셜 네트워크 데이터 서비스에 사용되었다.

FAST 2012 논문은 Voldemort의 배치 데이터 서빙 확장을 다룬다. Hadoop으로 오프라인에서 대규모 인덱스를 구축한 후, Voldemort에 벌크 로딩하는 방식으로 테라바이트 규모의 읽기 전용 데이터를 서비스한다. 오프라인 인덱스 구축은 Hadoop의 내결함성과 병렬성을 활용하며, 온라인 서빙 중에도 무중단으로 데이터를 교체할 수 있다.

Voldemort의 핵심 아키텍처:
- **Consistent Hashing**: Dynamo 스타일 링 기반 파티셔닝
- **Pluggable Storage**: BDB-JE, MySQL, 인메모리 등 다양한 스토리지 엔진
- **Versioning**: Vector clock 기반 충돌 감지
- **Routing**: 클라이언트 사이드 라우팅으로 추가 홉 없음

**Dynamo와의 관계**: Voldemort는 Riak과 함께 Dynamo의 가장 직접적인 오픈소스 구현체 중 하나이다. Dynamo의 consistent hashing, vector clocks, read repair 등을 구현하되, LinkedIn의 대규모 배치 처리 요구에 맞게 Hadoop 통합을 추가했다.

---

## 17. Apache HBase

### 17.1 관련 학술 논문

Apache HBase는 Google Bigtable 논문의 오픈소스 구현체로, Apache Hadoop 생태계의 일부이다. Bigtable 논문이 직접적 사양서 역할을 했으므로 독립적인 "발명" 논문은 없지만, 관련 학술 연구가 존재한다.

**A Comprehensive Study of HBase Storage Architecture — A Systematic Literature Review**
- **학회/연도**: Symmetry (MDPI), Vol. 13, No. 1, Article 109, January 2021

**The Time Machine in Columnar NoSQL Databases: The Case of Apache HBase**
- **학회/연도**: Future Internet (MDPI), Vol. 14, No. 3, Article 92, March 2022

**기술적 세부사항**:

HBase는 Bigtable의 데이터 모델과 API를 충실히 구현한다: 행 키로 정렬된 희소 다차원 맵, column family 기반 데이터 구조, 버전 관리를 위한 타임스탬프. HDFS(Hadoop Distributed File System) 위에 구축되며, Apache ZooKeeper를 Chubby의 대체로 사용한다.

아키텍처는 Bigtable과 유사한 마스터-슬레이브 구조이다: HMaster가 리전(태블릿에 해당) 할당과 부하 분산을 관리하고, RegionServer가 실제 데이터를 서비스한다. 쓰기 경로: WAL(Write-Ahead Log) -> MemStore -> HFile(SSTable에 해당) 플러시 -> 컴팩션.

**Dynamo와 비교**: HBase는 Bigtable의 클론이므로, Bigtable vs. Dynamo 비교가 그대로 적용된다. CP 시스템(강한 일관성), 마스터-슬레이브 아키텍처, 범위 기반 파티셔닝 등 Dynamo와 대조적인 설계를 가진다.

---

## 18. MongoDB

### 18.1 관련 학술 연구

MongoDB는 기업이 개발한 문서 지향 분산 데이터베이스로, 핵심 설계를 설명하는 단일 학술 논문이 없다. 그러나 분산 측면에 대한 학술 연구가 존재한다.

**Sharding and Master-Slave Replication of NoSQL Databases: Comparison of MongoDB and Redis**
- **학회/연도**: DATA 2023 (12th International Conference on Data Science, Technology and Applications)
- **저자**: Vögner, Al-Zaidi

**기술적 세부사항**:

MongoDB의 분산 아키텍처는 세 핵심 메커니즘으로 구성된다:

1. **Replica Set**: 동일한 데이터를 복제하는 노드 그룹. Primary 노드가 모든 쓰기를 처리하고, Secondary 노드가 oplog(operation log)를 통해 비동기적으로 복제. Primary 장애 시 자동 선거(election)로 새 Primary 선출.

2. **Sharding**: 데이터를 shard key 기반으로 여러 shard(각각 replica set)에 분산. Config Server가 shard key 범위 매핑을 관리하고, mongos 라우터가 쿼리를 적절한 shard로 라우팅. Range-based 또는 Hash-based 샤딩 지원.

3. **Transactions**: MongoDB 4.0(2018)부터 멀티 문서 ACID 트랜잭션 지원. 4.2(2019)부터 분산(sharded) 트랜잭션 지원. 2PC 기반의 분산 커밋.

**Dynamo와 비교**: MongoDB는 Dynamo와 다른 설계 철학을 가진다. MongoDB는 primary-secondary 복제로 강한 일관성(읽기 시 primary에서 읽는 경우)을 기본으로 하며, Dynamo는 모든 복제본이 동등한 마스터리스 구조이다. MongoDB의 문서 모델은 Dynamo의 단순 key-value보다 풍부한 쿼리를 지원한다. 그러나 MongoDB의 쓰기 가용성은 primary 의존적으로, Dynamo의 "always writable" 철학과 대비된다.

---

## 19. Redis Cluster

### 19.1 기술 개요 (학술 논문 부재)

Redis Cluster는 정식 학술 논문이 출판되지 않았다. Salvatore Sanfilippo(antirez)가 설계하고, Redis 공식 클러스터 사양서로 문서화되어 있다.

**기술적 세부사항**:

Redis Cluster는 16,384개의 해시 슬롯으로 키 공간을 분할한다. 각 키는 CRC16 해싱 알고리즘으로 슬롯에 매핑되며, 각 마스터 노드가 슬롯의 부분집합을 담당한다. 16,384개의 슬롯을 선택한 이유는 2KB 메모리에 들어가면서 최대 1,000개 마스터 노드까지 확장 가능하기 때문이다.

복제는 비동기적으로 이루어지며, 각 마스터에 하나 이상의 레플리카가 할당된다. 마스터 장애 시 레플리카가 자동으로 승격되며, 레플리카 마이그레이션 기능으로 레플리카가 없는 마스터에 레플리카를 재할당한다.

**Dynamo와 비교**: Redis Cluster는 Dynamo와 유사한 해시 기반 파티셔닝을 사용하지만, consistent hashing 대신 고정된 해시 슬롯 방식을 채택했다. Dynamo의 sloppy quorum과 달리 Redis Cluster는 마스터-레플리카 비동기 복제를 사용하여 장애 시 데이터 손실 가능성이 있다. Redis는 인메모리 데이터스토어로서 Dynamo의 디스크 기반 스토리지와는 성능 특성이 근본적으로 다르다.

---

## 20. ScyllaDB

### 20.1 기술 개요 (학술 논문 부재)

ScyllaDB는 정식 학술 논문이 출판되지 않았지만, 상당한 산업 벤치마크 문헌이 존재한다.

**기술적 세부사항**:

ScyllaDB는 Apache Cassandra의 C++20 재구현이다. Cassandra의 Java를 C++로, 전통적 Linux 프로그래밍 기법(스레드, 공유 메모리, 매핑 파일)을 **Seastar** 비동기 프로그래밍 라이브러리로 대체했다.

핵심 아키텍처 차별점은 **사드 퍼 코어(shard-per-core)** 설계이다. 각 CPU 코어가 데이터의 서로 다른 부분집합을 독립적으로 처리하며, 코어 간 데이터 공유 없이 명시적 메시지 패싱으로만 통신한다. 이를 통해 현대 NUMA SMP 머신에서 훨씬 나은 성능을 달성하고, 코어 수에 비례하여 확장된다.

성능 벤치마크에서 ScyllaDB는 Cassandra 대비 최대 10배의 처리량을 주장하며, Samsung의 2017 벤치마크에서 24코어 머신 클러스터에서 YCSB 워크로드에 따라 10-37배의 속도 향상이 관측되었다. 노드 수를 10배 줄이면서도 동등한 성능을 유지할 수 있다.

CQL(Cassandra Query Language)과 DynamoDB API 호환을 모두 제공하여, Cassandra 또는 DynamoDB에서의 이전이 용이하다.

---

## 21. YugabyteDB

### 21.1 기술 개요 (학술 논문 부재)

YugabyteDB는 정식 학술 논문이 출판되지 않았다. Yugabyte Inc.가 개발한 PostgreSQL 호환 분산 SQL 데이터베이스이다.

**기술적 세부사항**:

YugabyteDB는 Google Spanner의 수평 확장성/지리적 분산과 Amazon Aurora의 PostgreSQL 호환 쿼리 레이어를 결합한 하이브리드 접근법을 채택했다.

아키텍처는 두 계층으로 구성된다: (1) **YSQL** - PostgreSQL의 쿼리 레이어를 직접 포크하여 완전한 SQL 호환성 제공, (2) **DocDB** - Raft 기반 분산 문서 스토어로, 데이터 복제와 트랜잭션 처리를 담당. DocDB는 Google Spanner/CockroachDB와 유사한 범위 기반 샤딩과 Raft 합의를 사용한다.

---

## 22. Anna KVS

### 22.1 원본 논문

**Anna: A KVS for Any Scale**
- **저자**: Chenggang Wu, Jose M. Faleiro, Yihan Lin, Joseph M. Hellerstein
- **학회/연도**: ICDE 2018 (34th IEEE International Conference on Data Engineering)
- **페이지**: 401-412
- **DOI**: 10.1109/ICDE.2018.00044
- **BK21 등급**: 인정 IF **3**

**기술적 세부사항**:

Anna는 UC Berkeley RISE Lab에서 개발한 키-값 스토어로, 단일 머신부터 클라우드 규모까지 다양한 스케일에서 우수한 성능을 제공한다. 핵심 혁신은 **대기 없는(wait-free) 실행**과 **조율 없는(coordination-free) 일관성**의 결합이다.

Anna는 격자(lattice) 기반 합성을 사용하여 다양한 일관성 모델을 우아하게 구현한다. 각 키에 대해 독립적으로 일관성 수준을 지정할 수 있으며, 지원되는 모델로는 eventual consistency, causal consistency, read committed 등이 있다.

스레드 간 조율 없이(공유 메모리 접근 없이) 각 스레드가 독립적으로 동작하며, 주기적인 멀티캐스트로 상태를 동기화한다. 이를 통해 단일 노드에서도 Redis와 TBB 등을 능가하는 성능을 달성했다.

**Dynamo와 비교**: Anna는 Dynamo의 "eventual consistency + 높은 가용성" 철학을 극한까지 추진한 시스템이라 할 수 있다. 조율 비용을 완전히 제거함으로써 Dynamo보다 훨씬 높은 처리량을 달성하면서도, 격자 기반 합성으로 다양한 일관성 수준을 유연하게 지원한다.

### 22.2 후속 논문: Autoscaling Tiered Cloud Storage in Anna

**Autoscaling Tiered Cloud Storage in Anna**
- **학회/연도**: The VLDB Journal, 2020
- **DOI**: 10.1007/s00778-020-00632-7

Anna의 클라우드 환경 확장으로, 자동 스케일링과 계층화된 스토리지(메모리/SSD/S3)를 추가하여 비용 효율적인 클라우드 KVS를 구현한다.

---

## 23. SLOG

### 23.1 원본 논문

**SLOG: Serializable, Low-latency, Geo-replicated Transactions**
- **저자**: Kun Ren, Dennis Li, Daniel J. Abadi
- **학회/연도**: VLDB 2019 (PVLDB, Vol. 12, No. 11)
- **페이지**: 1747-1761
- **DOI**: 10.14778/3342263.3342647

**기술적 세부사항**:

SLOG는 Calvin의 후속 연구로, 지리적으로 분산된 트랜잭션에서 "엄격한 직렬화 가능성 vs. 낮은 지연" 트레이드오프를 우회하는 시스템이다. 핵심 통찰은: 대부분의 워크로드에서 데이터 접근에 물리적 지역성(locality)이 존재한다는 것이다.

SLOG는 각 데이터 항목에 "홈 리전"을 지정한다. 해당 리전에서 시작된 트랜잭션(홈 트랜잭션)은 WAN 왕복 없이 단일 리전 내에서 낮은 지연으로 처리된다. 여러 홈 리전의 데이터에 접근하는 트랜잭션만 교차 리전 조율이 필요하다.

Calvin과의 차이점: Calvin은 모든 트랜잭션이 전역 시퀀서를 통과해야 하므로 WAN 지연이 발생하지만, SLOG는 로컬 트랜잭션을 로컬에서 즉시 처리하면서도 전역 직렬화 가능성을 유지한다.

**Dynamo와 비교**: SLOG는 Dynamo의 "모든 쓰기를 로컬에서 처리"하는 접근에서 영감을 받되, 직렬화 가능한 강한 일관성을 유지한다. Dynamo는 로컬 쓰기로 낮은 지연을 달성하지만 충돌이 발생할 수 있고, SLOG는 홈 리전 개념으로 충돌 없이 로컬 쓰기의 낮은 지연을 달성한다.

---

## 24. Epoxy

### 24.1 원본 논문

**Epoxy: ACID Transactions Across Diverse Data Stores**
- **저자**: Peter Kraft, Qian Li, Xinjing Zhou, Peter Bailis, et al.
- **학회/연도**: VLDB 2023 (PVLDB, Vol. 16, No. 11)
- **페이지**: 2742-2754
- **DOI**: 10.14778/3611479.3611484

**기술적 세부사항**:

Epoxy는 이기종(heterogeneous) 데이터 스토어에 걸쳐 ACID 트랜잭션을 제공하는 프로토콜이다. 현대 애플리케이션은 단일 데이터베이스가 아닌 여러 데이터 스토어(관계형 DB, 키-값 스토어, 메시지 큐 등)를 결합하여 사용하며, 이들 간의 트랜잭션 안전성이 필요하다.

Epoxy는 "shim" 레이어를 각 데이터 스토어에 추가하여, 기존 스토어의 코드를 수정하지 않고도 교차 스토어 트랜잭션을 지원한다. 낙관적 동시성 제어를 기반으로 하며, 각 데이터 스토어의 기존 트랜잭션 메커니즘을 활용한다.

**Dynamo와의 관련성**: Epoxy는 Dynamo와 같은 특화 시스템이 다른 시스템과 공존하는 마이크로서비스 환경에서, 서로 다른 데이터 스토어 간의 일관성 문제를 해결한다. DynamoDB + Aurora + S3 같은 조합에서 트랜잭션 보장이 필요한 시나리오에 적용 가능하다.

---

## 25. Polaris

### 25.1 논문

**POLARIS: The Distributed SQL Engine in Azure Synapse**
- **학회/연도**: VLDB 2020 (PVLDB, Vol. 13, No. 12)
- **DOI**: 10.14778/3415478.3415545

Polaris는 Microsoft Azure Synapse의 분산 SQL 엔진이다. DynamoDB의 쿼리 프로세서와는 다른 시스템이며, 데이터 웨어하우징과 빅데이터 워크로드를 위한 것이다. 참고: 검색 결과에서 "Polaris"라는 이름의 DynamoDB 쿼리 프로세서 논문은 확인되지 않았다. Amazon DynamoDB의 쿼리 처리는 2022/2023 USENIX ATC 논문에서 다루어진다.

---

## 26. 기반 데이터 구조 논문

### 26.1 LSM-Tree

**The Log-Structured Merge-Tree (LSM-Tree)**
- **저자**: Patrick O'Neil, Edward Cheng, Dieter Gawlick, Elizabeth O'Neil
- **학회/연도**: Acta Informatica, Vol. 33, No. 4, pp. 351-385, 1996
- **DOI**: 10.1007/s002360050048

LSM-tree는 현대 분산 데이터베이스의 가장 중요한 기반 데이터 구조이다. 핵심 아이디어는 쓰기를 메모리 내 정렬 구조(memtable)에 버퍼링한 후, 주기적으로 디스크의 정렬된 파일(SSTable/HFile)로 플러시하고, 백그라운드 컴팩션으로 파일을 병합하는 것이다.

이 구조는 랜덤 쓰기를 순차적 I/O로 변환하여 쓰기 처리량을 극적으로 향상시키며, B-tree 대비 쓰기 증폭(write amplification)을 줄인다. 대신 읽기 시 여러 레벨을 탐색해야 하므로 읽기 증폭(read amplification)이 발생하며, 이를 Bloom filter로 완화한다.

**사용 시스템**: Bigtable, HBase, Cassandra, LevelDB, RocksDB, TiKV, CockroachDB, ScyllaDB, FoundationDB 등 거의 모든 현대 분산 KV/DB 시스템.

### 26.2 LSM-Tree 서베이

**LSM-based Storage Techniques: A Survey**
- **저자**: Chen Luo, Michael J. Carey
- **학회/연도**: The VLDB Journal, Vol. 29, pp. 393-418, 2020
- **DOI**: 10.1007/s00778-019-00555-y

LSM-tree에 대한 최근 연구를 종합적으로 조사한 서베이 논문. 분류 체계를 제시하고 다양한 최적화(쓰기 증폭 감소, 읽기 증폭 감소, 공간 증폭 감소)를 비교 분석한다.

### 26.3 Bw-Tree

**The Bw-Tree: A B-tree for New Hardware Platforms**
- **저자**: Justin J. Levandoski, David B. Lomet, Sudipta Sengupta
- **학회/연도**: ICDE 2013 (29th IEEE International Conference on Data Engineering)
- **DOI**: 10.1109/ICDE.2013.6544834

Bw-tree는 Microsoft Research가 설계한 잠금-프리(latch-free) B-tree 변형이다. 현대 멀티코어 프로세서의 캐시를 효과적으로 활용하도록 설계되었으며, Compare-And-Swap(CAS) 연산으로 모든 구조적 수정을 수행한다. 페이지는 논리적 식별자로 참조되며, 매핑 테이블을 통해 물리적 위치로 변환된다. 페이지 수정은 델타 레코드 체인으로 표현되어 인플레이스 수정 없이 갱신이 가능하다.

Azure Cosmos DB의 인덱싱 엔진이 Bw-tree를 기반으로 하며, SQL Server의 Hekaton 인메모리 엔진에도 사용된다.

### 26.4 B-epsilon Tree / Cache-Oblivious Streaming B-Trees

**Cache-Oblivious Streaming B-trees**
- **저자**: Michael A. Bender, Martin Farach-Colton, Jeremy T. Fineman, Yonatan Fogel, Bradley C. Kuszmaul, Jelani Nelson
- **학회/연도**: SPAA 2007 (19th ACM Symposium on Parallel Algorithms and Architectures)
- **DOI**: 10.1145/1248377.1248393

B-epsilon 트리는 B-tree의 쓰기 최적화 변형으로, 각 내부 노드에 버퍼를 추가하여 삽입을 배치 처리한다. 삽입은 O((log_B N) / B^epsilon) 전송으로 수행되어(0 < epsilon < 1), B-tree의 O(log_B N)보다 효율적이다. 읽기 성능은 O(log_B N / epsilon)으로 B-tree보다 약간 느리다.

이 논문의 cache-oblivious 변형인 COLA(Cache-Oblivious Lookahead Array)는 LSM-tree와 개념적으로 유사하며, 둘 다 쓰기를 버퍼링하고 배치 병합하는 전략을 사용한다. BetrFS(B-epsilon tree File System)가 이 데이터 구조를 파일 시스템에 적용한 사례이다.

### 26.5 Brodal-Fagerberg 하한

**Lower Bounds for External Memory Dictionaries**
- **저자**: Gerth Stølting Brodal, Rolf Fagerberg
- **학회/연도**: SODA 2003 (14th ACM-SIAM Symposium on Discrete Algorithms)
- **페이지**: 546-554

외부 메모리 사전(dictionary)에 대한 이론적 하한을 제시하며, B-epsilon 트리가 최적에 가까운 쓰기/읽기 트레이드오프를 달성함을 보인다.

---

## 27. 클라우드 네이티브 데이터베이스 서베이

### 27.1 종합 서베이

**Cloud-Native Databases: A Survey**
- **저자**: Hao Dong, Chenchen Zhang, et al.
- **학회/연도**: IEEE Transactions on Knowledge and Data Engineering (TKDE), 2024
- **DOI**: 10.1109/TKDE.2024.3397508

클라우드 네이티브 OLTP 및 OLAP 데이터베이스의 최신 기술을 종합 조사한다. 스토리지 관리, 쿼리 처리, 서버리스 컴퓨팅, 데이터 보호, 데이터베이스 내 머신러닝 등을 다룬다.

### 27.2 VLDB 서베이

**Cloud Databases: New Techniques, Challenges, and Opportunities**
- **저자**: Guoliang Li, Hao Dong, Chenchen Zhang
- **학회/연도**: VLDB 2022 (PVLDB, Vol. 15, No. 12)
- **페이지**: 3758-3761

"컴퓨트-스토리지 분리(compute-storage disaggregation)"와 "로그가 데이터베이스다" 같은 최신 기법을 소개하며, 높은 탄력성과 낮은 비용을 추구하는 클라우드 데이터베이스의 방향성을 논한다.

### 27.3 HTAP 서베이

**HTAP Databases: What is New and What is Next**
- **저자**: Guoliang Li, Chenchen Zhang
- **학회/연도**: SIGMOD 2022

HTAP(Hybrid Transactional/Analytical Processing) 데이터베이스의 현황과 미래 방향을 다룬다. TiDB, SAP HANA, MemSQL/SingleStore 등의 시스템을 분석한다.

---

## 28. Amazon DynamoDB 최신 논문

### 28.1 DynamoDB at Hyperscale

**Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service**
- **저자**: Mostafa Elhemali, Niall Gallagher, Nick Gordon, Joseph Idziorek, Richard Krog, Colin Lazier, Erben Mo, Akhilesh Mritunjai, Somasundaram Perianayagam, Tim Rath, Swami Sivasubramanian, James Christopher Sorenson III, Sroaj Sosothikul, Doug Terry, Akshat Vig
- **학회/연도**: USENIX ATC 2022
- **페이지**: 1037-1048
- **ISBN**: 978-1-939133-29-4

2012년 서비스 출시 이후 10년간의 DynamoDB 진화를 설명한다. 2021년 Amazon Prime Day 66시간 이벤트에서 수조 개의 API 호출, 초당 8,920만 요청 피크를 처리했다. 원본 Dynamo 논문의 설계에서 크게 진화하여, 강한 일관성(strongly consistent reads) 옵션, 자동 파티셔닝/리밸런싱, 글로벌 테이블(다중 리전 복제) 등을 추가했다.

### 28.2 DynamoDB Distributed Transactions

**Distributed Transactions at Scale in Amazon DynamoDB**
- **저자**: Joseph Idziorek, Alex Keyes, Colin Lazier, Somu Perianayagam, Prithvi Ramanathan, James Christopher Sorenson III, Doug Terry, Akshat Vig
- **학회/연도**: USENIX ATC 2023
- **페이지**: 705-717
- **ISBN**: 978-1-939133-35-9

DynamoDB에 트랜잭션을 추가한 방법을 설명한다. 타임스탬프 순서화 프로토콜(timestamp ordering protocol)을 사용하되, 키-값 스토어의 의미론(semantics)을 활용하여 트랜잭셔널 및 비트랜잭셔널 연산 모두에 대해 낮은 지연을 달성했다.

---

## 29. 비교 요약표

### 시스템별 핵심 비교

| 시스템 | 연도 | 학회 | BK21 IF | 일관성 | CAP | 아키텍처 | 트랜잭션 |
|--------|------|------|---------|--------|-----|---------|---------|
| Bigtable | 2006 | OSDI | 4 | Strong | CP | Master-slave | 단일행만 |
| **Dynamo** | **2007** | **SOSP** | **4** | **Eventually** | **AP** | **Masterless** | **없음** |
| PNUTS | 2008 | VLDB | 4 | Timeline | CP | Per-record master | 단일레코드 |
| Cassandra | 2010 | SIGOPS | - | Tunable | AP | Masterless | LWT (2.0+) |
| Percolator | 2010 | OSDI | 4 | Snapshot Isolation | CP | Over Bigtable | 2PC |
| Megastore | 2011 | CIDR | 미등재 | Strong (per-EG) | CP | Over Bigtable | Per-EG ACID |
| Spanner | 2012 | OSDI | 4 | External | CP | Paxos groups | 전역 ACID |
| Calvin | 2012 | SIGMOD | 4 | Serializable | CP | Deterministic | 결정론적 |
| F1 | 2013 | VLDB | 4 | External | CP | Over Spanner | SQL + ACID |
| TAO | 2013 | ATC | 3 | Eventually | AP | Cache over MySQL | 없음 |
| Anna | 2018 | ICDE | 3 | Tunable (lattice) | AP | Coordination-free | 없음 |
| SLOG | 2019 | VLDB | 4 | Serializable | CP | Det. + locality | 결정론적 |
| CockroachDB | 2020 | SIGMOD | 4 | Serializable (SSI) | CP | Raft groups | SQL + ACID |
| TiDB | 2020 | VLDB | 4 | Snapshot Isolation | CP | Raft + Percolator | SQL + ACID |
| Aria | 2020 | VLDB | 4 | Serializable | CP | Deterministic | 결정론적 |
| FoundationDB | 2021 | SIGMOD | 4 | Serializable | CP | Unbundled | OCC |
| **DynamoDB** | **2022** | **ATC** | **3** | **Strong/Eventually** | **CP/AP 선택** | **Managed** | **TransactWrite** |

### 설계 철학 스펙트럼

```
← AP (가용성 우선) ─────────────────── CP (일관성 우선) →

Dynamo   Cassandra   PNUTS   Megastore   Spanner   Calvin
Riak     TAO         Aurora   CockroachDB  FoundationDB
Voldemort Anna                TiDB        SLOG
                              F1
```

### 데이터 모델 스펙트럼

```
Key-Value      Wide-Column     Document       Relational      Graph
─────────      ───────────     ────────       ──────────      ─────
Dynamo         Bigtable        Cosmos DB      Spanner         TAO
Riak           HBase           MongoDB        F1
Voldemort      Cassandra       FDB Record     CockroachDB
Anna           ScyllaDB        Layer          TiDB
Redis                                         Aurora
FoundationDB                                  Calvin/SLOG
```

### 시간 순서와 영향 관계

```
1996: LSM-Tree (O'Neil) ──────────────────────────────────────────┐
2003: GFS                                                         │
2004: MapReduce                                                   │
2006: Bigtable (OSDI) ─────────> HBase ──────────> TiDB          │
      │                                                           │
2007: Dynamo (SOSP) ───────────> Riak                            │
      │                          Voldemort                        │
      │                          Cassandra ──────> ScyllaDB      │
      │                                                           │
2008: PNUTS (VLDB) ────────────> Sherpa (2019)                   │
      │                                                           │
2010: Percolator (OSDI) ────────> TiKV/TiDB (2020)              │
      │                                                           │
2011: Megastore (CIDR) ─────────> Spanner (2012) ──> F1 (2013)  │
      │                            │                              │
2012: Calvin (SIGMOD) ──────────> SLOG (2019) ──> Aria (2020)   │
      │                            │                              │
      │                            └──> CockroachDB (2020)       │
      │                                                           │
2013: TAO (ATC) ────────────────> RAMP-TAO (2021)               │
      │                                                           │
2015: Cosmos DB (VLDB) ─── Bw-tree ◄─── ICDE 2013              │
      │                                                           │
2017: Aurora (SIGMOD) ──────────> Aurora 2018                    │
      Spanner SQL (SIGMOD)                                        │
      │                                                           │
2018: Anna (ICDE)                                                │
      │                                                           │
2021: FoundationDB (SIGMOD) ◄── Record Layer (2019)              │
      │                                                           │
2022: DynamoDB ATC ◄─── 모든 것의 영향 ◄─────────────────────────┘
2023: DynamoDB Transactions (ATC)
      Epoxy (VLDB)
```

---

## 참고문헌 (전체)

1. F. Chang et al., "Bigtable: A Distributed Storage System for Structured Data," *Proc. OSDI*, 2006. DOI: 10.5555/1267308.1267323
2. G. DeCandia et al., "Dynamo: Amazon's Highly Available Key-value Store," *Proc. SOSP*, 2007. DOI: 10.1145/1294261.1294281
3. B. F. Cooper et al., "PNUTS: Yahoo!'s Hosted Data Serving Platform," *PVLDB*, 1(2), 2008. DOI: 10.14778/1454159.1454167
4. A. Lakshman and P. Malik, "Cassandra: A Decentralized Structured Storage System," *ACM SIGOPS OSR*, 44(2), 2010. DOI: 10.1145/1773912.1773922
5. D. Peng and F. Dabek, "Large-scale Incremental Processing Using Distributed Transactions and Notifications," *Proc. OSDI*, 2010.
6. A. Thomson and D. Abadi, "The Case for Determinism in Database Systems," *PVLDB*, 3(1), 2010. DOI: 10.14778/1920841.1920855
7. J. Baker et al., "Megastore: Providing Scalable, Highly Available Storage for Interactive Services," *Proc. CIDR*, 2011.
8. J. C. Corbett et al., "Spanner: Google's Globally-Distributed Database," *Proc. OSDI*, 2012.
9. A. Thomson et al., "Calvin: Fast Distributed Transactions for Partitioned Database Systems," *Proc. SIGMOD*, 2012. DOI: 10.1145/2213836.2213838
10. J. Shute et al., "F1: A Distributed SQL Database That Scales," *PVLDB*, 6(11), 2013. DOI: 10.14778/2536222.2536232
11. J. Levandoski et al., "The Bw-Tree: A B-tree for New Hardware Platforms," *Proc. ICDE*, 2013. DOI: 10.1109/ICDE.2013.6544834
12. N. Bronson et al., "TAO: Facebook's Distributed Data Store for the Social Graph," *Proc. USENIX ATC*, 2013.
13. K. Ren et al., "An Evaluation of the Advantages and Disadvantages of Deterministic Database Systems," *PVLDB*, 7(10), 2014. DOI: 10.14778/2732951.2732955
14. C. Mathieu et al., "Bigtable Merge Compaction," arXiv:1407.3008, 2014.
15. D. Shukla et al., "Schema-Agnostic Indexing with Azure DocumentDB," *PVLDB*, 8(12), 2015.
16. P. O'Neil et al., "The Log-Structured Merge-Tree (LSM-Tree)," *Acta Informatica*, 33(4), 1996. DOI: 10.1007/s002360050048
17. E. Brewer, "Spanner, TrueTime and the CAP Theorem," Google Research, 2017.
18. D. F. Bacon et al., "Spanner: Becoming a SQL System," *Proc. SIGMOD*, 2017. DOI: 10.1145/3035918.3056103
19. A. Verbitski et al., "Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases," *Proc. SIGMOD*, 2017. DOI: 10.1145/3035918.3056101
20. A. Verbitski et al., "Amazon Aurora: On Avoiding Distributed Consensus for I/Os, Commits, and Membership Changes," *Proc. SIGMOD*, 2018. DOI: 10.1145/3183713.3196937
21. C. Wu et al., "Anna: A KVS for Any Scale," *Proc. ICDE*, 2018. DOI: 10.1109/ICDE.2018.00044
22. D. Abadi, "An Overview of Deterministic Database Systems," *CACM*, 2018. DOI: 10.1145/3181853
23. C. Chrysafis et al., "FoundationDB Record Layer: A Multi-Tenant Structured Datastore," *Proc. SIGMOD*, 2019. DOI: 10.1145/3299869.3314039
24. K. Ren et al., "SLOG: Serializable, Low-latency, Geo-replicated Transactions," *PVLDB*, 12(11), 2019. DOI: 10.14778/3342263.3342647
25. B. F. Cooper et al., "PNUTS to Sherpa: Lessons from Yahoo!'s Cloud Database," *PVLDB*, 12(12), 2019. DOI: 10.14778/3352063.3352146
26. C. Luo and M. Carey, "LSM-based Storage Techniques: A Survey," *The VLDB Journal*, 29, 2020. DOI: 10.1007/s00778-019-00555-y
27. R. Taft et al., "CockroachDB: The Resilient Geo-Distributed SQL Database," *Proc. SIGMOD*, 2020. DOI: 10.1145/3318464.3386134
28. D. Huang et al., "TiDB: A Raft-based HTAP Database," *PVLDB*, 13(12), 2020. DOI: 10.14778/3415478.3415535
29. Y. Lu et al., "Aria: A Fast and Practical Deterministic OLTP Database," *PVLDB*, 13(12), 2020. DOI: 10.14778/3407790.3407808
30. J. Zhou et al., "FoundationDB: A Distributed Unbundled Transactional Key Value Store," *Proc. SIGMOD*, 2021. DOI: 10.1145/3448016.3457559
31. A. Cheng et al., "RAMP-TAO: Layering Atomic Transactions on Facebook's Online TAO Data Store," *PVLDB*, 14(12), 2021. DOI: 10.14778/3476311.3476379
32. R. Sumbaly et al., "Serving Large-scale Batch Computed Data with Project Voldemort," *Proc. USENIX FAST*, 2012.
33. M. Elhemali et al., "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service," *Proc. USENIX ATC*, 2022.
34. J. Idziorek et al., "Distributed Transactions at Scale in Amazon DynamoDB," *Proc. USENIX ATC*, 2023.
35. P. Kraft et al., "Epoxy: ACID Transactions Across Diverse Data Stores," *PVLDB*, 16(11), 2023. DOI: 10.14778/3611479.3611484
36. M. A. Bender et al., "Cache-Oblivious Streaming B-trees," *Proc. SPAA*, 2007. DOI: 10.1145/1248377.1248393
37. G. Li et al., "Cloud Databases: New Techniques, Challenges, and Opportunities," *PVLDB*, 15(12), 2022.
38. H. Dong et al., "Cloud-Native Databases: A Survey," *IEEE TKDE*, 2024. DOI: 10.1109/TKDE.2024.3397508
39. R. Klophaus, "Riak Core: Building Distributed Applications Without Shared State," *CUFP*, 2010.
