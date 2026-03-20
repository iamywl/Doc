# 분산 시스템 이론적 기반 논문 — 심층 학술 연구

Dynamo를 비롯한 분산 데이터베이스는 수십 년간 축적된 분산 시스템 이론 위에 구축되었다. 이 문서는 합의 프로토콜, 시간/순서, 불가능성 정리, 일관성 모델, 복제, 분산 스토리지, 장애 감지, 분산 트랜잭션 등 핵심 이론 논문을 주제별로 심층 분석한다.

---

# Part 1: 합의 프로토콜 (Consensus Protocols)

## 1.1 Paxos — 분산 합의 알고리즘의 원형

### The Part-Time Parliament
- **제목**: "The Part-Time Parliament"
- **저자**: Leslie Lamport
- **학회/저널**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 16, No. 2, pp. 133–169, 1998
- **DOI**: 10.1145/279227.279229
- **원고 작성**: 1989년 (최초 제출 1990년, 게재까지 약 8년 소요)

**기여 및 상세 설명**:

Lamport는 고대 그리스 Paxos 섬의 의회 비유를 통해 비동기 분산 시스템에서 장애가 발생하더라도 합의(consensus)에 도달할 수 있는 알고리즘을 제안하였다. 이 논문은 분산 컴퓨팅 역사에서 가장 영향력 있는 논문 중 하나로, proposer, acceptor, learner라는 세 가지 역할을 정의하고, 두 단계(prepare/accept)를 통해 값에 대한 합의를 달성하는 프로토콜을 기술한다.

Paxos의 핵심 속성은 안전성(safety)과 활성(liveness)이다. 안전성은 항상 보장된다: 한 번 선택된 값은 절대 변경되지 않으며, 두 개의 다른 값이 동시에 선택될 수 없다. 반면 활성은 FLP 불가능성 정리에 의해 비동기 시스템에서 완전히 보장할 수 없으므로, Paxos는 충분히 안정적인 네트워크 조건 하에서만 종료를 보장한다. 이러한 설계는 FLP 결과를 실용적으로 우회하는 접근법이다.

Paxos는 N = 2f+1 노드로 f개의 crash failure를 허용하며, 메시지 손실, 중복, 재정렬, 임의의 지연을 견딘다. 이 알고리즘은 사실상 모든 현대 분산 합의 시스템의 이론적 원형이 되었으며, Google Chubby, Google Spanner, Apache ZooKeeper 등의 시스템에 직접 채택되거나 변형되었다.

**역사적 맥락**: Lamport는 1989년에 이 논문을 작성했으나, Paxos 섬 비유가 지나치게 난해하다는 리뷰어들의 반응으로 인해 1998년에야 출판되었다. 이 논문의 난해함은 이후 "Paxos Made Simple"을 비롯한 수많은 해설 논문을 촉발하였다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 AP 시스템으로 Paxos를 사용하지 않았으나, DynamoDB(2022)는 각 파티션의 복제 그룹에서 Multi-Paxos 기반 리더 선출과 합의를 도입하여 strong consistency 옵션을 제공한다. Paxos는 DynamoDB의 일관성 보장의 핵심 메커니즘이다.

---

## 1.2 Paxos Made Simple

### Paxos Made Simple
- **제목**: "Paxos Made Simple"
- **저자**: Leslie Lamport
- **출처**: *ACM SIGACT News (Distributed Computing Column)*, Vol. 32, No. 4, pp. 51–58, December 2001
- **DOI**: N/A (비공식 기고)

**기여 및 상세 설명**:

PODC 2001 학회에서 Paxos 알고리즘이 이해하기 어렵다는 불만이 계속되자, Lamport는 그리스 의회 비유 없이 순수하게 알고리즘 자체만을 평이한 영어로 기술한 이 논문을 작성하였다. 첫 문장 "The Paxos algorithm, when presented in plain English, is very simple"은 이 논문의 목적을 명확히 한다.

이 논문은 합의 문제를 세 가지 역할—proposer(값을 제안), acceptor(제안을 수락/거부), learner(선택된 값을 학습)—로 분해하고, 두 단계 프로토콜을 단계별로 설명한다. Prepare 단계에서 proposer는 고유한 제안 번호를 가지고 acceptor들에게 약속을 요청하고, Accept 단계에서 과반수의 약속을 받은 proposer는 값을 제안한다. 과반수의 acceptor가 수락하면 값이 선택된다.

이 논문은 또한 단일 값 합의(single-decree)에서 연속적인 값들의 합의(multi-decree, 즉 Multi-Paxos)로의 확장을 간략히 기술한다. Multi-Paxos는 리더가 선출된 후 연속적인 로그 위치에 대해 Prepare 단계를 생략할 수 있어 성능이 크게 향상된다.

**역사적 맥락**: 원본 Paxos 논문의 난해함 때문에 많은 연구자들이 올바르게 구현하는 데 어려움을 겪었다. 이 논문은 Paxos를 실용적으로 채택하는 데 결정적인 역할을 하였으며, 이후 Raft 등 더 이해하기 쉬운 합의 알고리즘의 등장에 동기를 부여하였다.

**Dynamo/DynamoDB 관련**: DynamoDB의 복제 그룹은 Multi-Paxos를 사용하며, 이 논문에서 기술한 리더 기반 최적화(연속적인 Prepare 생략)가 DynamoDB의 쓰기 경로 성능의 핵심이다.

---

## 1.3 Paxos Made Live — 산업 환경에서의 Paxos 구현

### Paxos Made Live: An Engineering Perspective
- **제목**: "Paxos Made Live: An Engineering Perspective"
- **저자**: Tushar Deepak Chandra, Robert Griesemer, Joshua Redstone
- **학회**: *Proceedings of the Twenty-Sixth Annual ACM Symposium on Principles of Distributed Computing (PODC 2007)*, pp. 398–407, Portland, Oregon, August 12–15, 2007
- **DOI**: 10.1145/1281100.1281103

**기여 및 상세 설명**:

이 논문은 Google에서 Paxos를 이용한 장애 허용 데이터베이스(Chubby 잠금 서비스)를 실제 프로덕션 환경에서 구축한 경험을 보고한다. 기존 문헌에서 Paxos 알고리즘 자체는 잘 정의되어 있었으나, 실제 시스템으로 구현하는 과정은 예상보다 훨씬 복잡했음을 강조한다.

논문의 핵심 기여는 이론적 알고리즘과 프로덕션 시스템 사이의 간극을 구체적으로 문서화한 것이다. 디스크 장애, 리더 교체 시의 로그 처리, 스냅샷과 로그 압축, 구성 변경, 멀티-Paxos 최적화 등 실제 엔지니어링에서 마주치는 문제들과 그 해결책을 상세히 기술한다. 저자들은 C++보다 높은 수준의 명세 언어를 개발하여 Multi-Paxos 상태 머신을 간결하게 인코딩했으며, 이를 C++로 번역하는 방식을 사용하였다.

또한 정확성 검증을 위한 접근법도 논의한다. 분산 합의 알고리즘의 구현은 미묘한 버그를 포함하기 쉬우며, Google 팀은 모델 체킹과 광범위한 테스트를 활용하였다. 이 논문은 "알고리즘을 아는 것"과 "시스템을 구축하는 것" 사이의 차이가 얼마나 큰지를 보여주는 교과서적 사례이다.

**역사적 맥락**: Google의 내부 인프라(Chubby, Bigtable, Spanner)는 Paxos 위에 구축되었으며, 이 논문은 산업계에서 합의 프로토콜을 채택하는 데 있어 가장 영향력 있는 경험 보고서 중 하나이다.

**Dynamo/DynamoDB 관련**: DynamoDB가 2022년 논문에서 Multi-Paxos 기반 복제를 도입했을 때, Google의 이 경험 보고서에서 다룬 엔지니어링 과제들(로그 압축, 스냅샷, 구성 변경 등)은 AWS 팀도 동일하게 직면한 문제들이다.

---

## 1.4 Multi-Paxos

### Multi-Paxos (Lamport의 Multi-Decree Parliament)
- **제목**: "The Part-Time Parliament" (Section 3: The Multi-Decree Parliament)에서 기술
- **저자**: Leslie Lamport
- **출처**: *ACM TOCS*, 1998 (상동) / "Paxos Made Simple"에서 재기술
- **DOI**: 10.1145/279227.279229

**기여 및 상세 설명**:

Multi-Paxos는 단일 값 합의(single-decree Paxos)를 복제된 상태 머신(replicated state machine)을 위한 연속적 로그 합의로 확장한 것이다. 원래 Paxos 논문의 Section 3에서 기술되었으며, "Paxos Made Simple"에서 더 명확히 설명되었다. Multi-Paxos 자체는 독립된 논문이 아니라 Paxos의 자연스러운 확장이다.

핵심 최적화는 안정된 리더(stable leader)의 존재 하에 Prepare 단계를 생략하는 것이다. 리더가 이미 과반수로부터 약속을 받은 상태라면, 이후의 로그 슬롯에 대해서는 Accept 메시지만으로 합의를 달성할 수 있다. 이로써 일반적인 경우 합의에 필요한 메시지 왕복이 2회에서 1회로 줄어든다.

실제 구현에서는 리더 선출, 로그 갭 처리, 리더 교체 시의 로그 복구, 스냅샷과 로그 압축 등 추가적인 메커니즘이 필요하다. 이러한 세부사항은 Paxos 논문에서 충분히 다루어지지 않아, 각 구현마다 다른 방식을 채택하게 되었다.

**Dynamo/DynamoDB 관련**: DynamoDB(2022)는 각 파티션의 복제 그룹에서 Multi-Paxos를 핵심 합의 프로토콜로 사용한다. 리더 노드가 쓰기 요청을 처리하고, Multi-Paxos를 통해 복제 로그에 기록하며, strong consistency 읽기는 리더에서만 수행된다.

---

## 1.5 Fast Paxos

### Fast Paxos
- **제목**: "Fast Paxos"
- **저자**: Leslie Lamport
- **학회/저널**: *Distributed Computing*, Vol. 19, No. 2, pp. 79–103, 2006
- **DOI**: 10.1007/s00446-006-0005-x

**기여 및 상세 설명**:

Fast Paxos는 Classic Paxos의 변형으로, 클라이언트가 리더를 거치지 않고 직접 acceptor들에게 제안을 보낼 수 있게 하여, 일반적인 경우 합의 지연을 2 메시지 지연에서 1 메시지 지연으로 줄인다. 그러나 이를 위해 더 큰 쿼럼(N ≥ 3f+1 또는 N ≥ 2f+1이지만 더 큰 쿼럼)이 필요하다.

Fast Paxos의 핵심 통찰은 충돌(collision)이 없는 경우—즉 한 번에 하나의 클라이언트만 제안하는 경우—리더를 우회하여 지연을 줄일 수 있다는 것이다. 충돌이 발생하면 리더가 개입하여 Classic Paxos로 복귀하므로, Fast Paxos는 충돌이 드문 워크로드에서 효과적이다.

그러나 Fast Paxos의 쿼럼 요구사항은 Classic Paxos보다 엄격하다. Fast 라운드에서 충돌을 해결하려면 Phase 2 쿼럼 크기가 ⌈(3N/4)⌉ 이상이어야 한다. 이는 더 많은 노드의 응답을 기다려야 함을 의미하며, 실용성에 제약을 준다.

**Dynamo/DynamoDB 관련**: DynamoDB는 리더 기반 Multi-Paxos를 사용하므로 Fast Paxos를 직접 채택하지 않는다. 그러나 Fast Paxos의 아이디어(리더 우회를 통한 지연 감소)는 EPaxos 등 후속 연구에 영향을 미쳤다.

---

## 1.6 Flexible Paxos — 쿼럼 교차 조건의 재검토

### Flexible Paxos: Quorum Intersection Revisited
- **제목**: "Flexible Paxos: Quorum Intersection Revisited"
- **저자**: Heidi Howard, Dahlia Malkhi, Alexander Spiegelman
- **학회**: *20th International Conference on Principles of Distributed Systems (OPODIS 2016)*, LIPIcs, Vol. 70, Article 25, 2017
- **DOI**: 10.4230/LIPIcs.OPODIS.2016.25

**기여 및 상세 설명**:

Flexible Paxos는 Paxos 프로토콜의 근본적인 가정 중 하나인 "Phase 1 쿼럼과 Phase 2 쿼럼이 모두 과반수여야 한다"는 조건을 재검토한 연구이다. 저자들은 실제로 필요한 것은 Phase 1 쿼럼과 Phase 2 쿼럼의 교차(intersection)뿐이라는 것을 증명하였다. 즉, Q1 + Q2 > N이면 충분하다.

이 발견은 실용적으로 매우 중요한 함의를 가진다. 예를 들어, 5개 노드로 구성된 시스템에서 Phase 1 쿼럼을 4로, Phase 2 쿼럼을 2로 설정할 수 있다. 리더 선출(Phase 1)은 드물게 발생하므로 높은 쿼럼이 부담되지 않고, 일반적인 복제(Phase 2)는 단 2개 노드만 필요하여 지연과 가용성이 향상된다.

Flexible Paxos는 또한 Fast Paxos의 쿼럼 요구사항이 불필요하게 크다는 것을 보여주었다. Fast Paxos에서 요구하는 쿼럼 크기는 실제로 안전성 보장에 필수적인 것보다 더 크다.

**Dynamo/DynamoDB 관련**: DynamoDB의 복제 그룹은 일반적으로 3개 노드(2f+1, f=1)를 사용하는데, Flexible Paxos의 통찰은 쓰기 쿼럼을 2, 읽기/리더 선출 쿼럼을 2로 유지하면서도 다양한 구성을 가능하게 한다. 이는 DynamoDB가 쓰기 성능과 읽기 일관성 사이의 트레이드오프를 유연하게 조정하는 데 이론적 기반을 제공한다.

---

## 1.7 Viewstamped Replication — Paxos보다 먼저 나온 합의 프로토콜

### Viewstamped Replication: A New Primary Copy Method to Support Highly-Available Distributed Systems
- **제목**: "Viewstamped Replication: A New Primary Copy Method to Support Highly-Available Distributed Systems"
- **저자**: Brian M. Oki, Barbara Liskov
- **학회**: *Proceedings of the Seventh Annual ACM Symposium on Principles of Distributed Computing (PODC 1988)*, pp. 8–17, Toronto, Canada, August 1988
- **DOI**: 10.1145/62546.62549

**기여 및 상세 설명**:

Viewstamped Replication(VR)은 Brian Oki의 1988년 MIT 박사 논문에서 제안되었으며, primary copy(주 복사본) 기법을 기반으로 고가용 분산 시스템을 지원하는 복제 알고리즘이다. 주목할 점은 VR이 Lamport의 Paxos(1989년 작성, 1998년 출판)보다 시간적으로 선행한다는 것이다.

VR은 "뷰(view)"라는 개념을 도입한다. 각 뷰에는 하나의 프라이머리와 여러 백업이 있으며, 프라이머리가 실패하면 뷰 변경(view change) 프로토콜을 통해 새로운 프라이머리를 선출한다. "뷰스탬프(viewstamp)"는 뷰 번호와 뷰 내 순서 번호의 쌍으로, 손실된 정보를 감지하는 데 사용된다.

클라이언트 요청은 프라이머리에서 처리되며, 프라이머리는 백업들에게 상태 변경을 알린다. 프라이머리 장애 시, 백업들은 뷰 변경 프로토콜을 실행하여 새 프라이머리를 선출하고 복구한다. 이 과정에서 이전 뷰의 미완료 요청이 올바르게 처리되도록 보장한다.

**역사적 맥락**: VR은 Paxos와 독립적으로 개발되었으나, 두 프로토콜은 본질적으로 동등한 문제를 해결한다. VR은 리더 기반 접근법을 명시적으로 채택하여 Paxos보다 구현이 직관적이며, 이후 Raft에 큰 영향을 미쳤다.

**Dynamo/DynamoDB 관련**: DynamoDB의 리더 기반 복제 구조는 VR의 프라이머리-백업 모델과 개념적으로 유사하다. 파티션 리더가 쓰기를 처리하고 팔로워에게 전파하는 DynamoDB의 구조는 VR의 프라이머리-백업 패턴을 따른다.

---

## 1.8 Viewstamped Replication Revisited

### Viewstamped Replication Revisited
- **제목**: "Viewstamped Replication Revisited"
- **저자**: Barbara Liskov, James Cowling
- **출처**: *MIT Technical Report MIT-CSAIL-TR-2012-021*, July 2012

**기여 및 상세 설명**:

이 기술 보고서는 원래 VR 프로토콜의 현대적 재기술로, 1988년 논문의 프로토콜을 더 명확하고 완전하게 설명한다. 클라이언트 요청 처리, 그룹 재조직(뷰 변경), 장애 노드의 그룹 재합류, 그리고 구성 변경(멤버십 변경)까지 포함하여 실제 구현에 필요한 모든 세부사항을 다룬다.

특히 중요한 기여는 (1) 성능 최적화 기법들(배치 처리, 비동기 복제 옵션)의 기술, (2) 그룹 멤버십과 장애 허용 노드 수를 변경할 수 있는 재구성(reconfiguration) 프로토콜의 제시, (3) 원래 1988년 논문에서 다루지 않았던 복구(recovery) 프로토콜의 상세한 기술이다.

**Dynamo/DynamoDB 관련**: DynamoDB의 파티션 분할/병합 시 복제 그룹의 재구성이 필요하며, 이 논문에서 기술한 재구성 프로토콜은 그러한 동적 구성 변경의 이론적 기반이 된다.

---

## 1.9 Raft — 이해 가능한 합의 알고리즘

### In Search of an Understandable Consensus Algorithm
- **제목**: "In Search of an Understandable Consensus Algorithm"
- **저자**: Diego Ongaro, John Ousterhout
- **학회**: *2014 USENIX Annual Technical Conference (USENIX ATC 14)*, pp. 305–319, Philadelphia, PA, June 2014
- **DOI**: N/A (USENIX proceedings)

**기여 및 상세 설명**:

Raft는 Paxos와 동등한 안전성과 장애 허용을 제공하면서도, "이해 가능성(understandability)"을 핵심 설계 목표로 한 합의 알고리즘이다. Diego Ongaro의 Stanford 박사 논문에서 비롯되었으며, 분산 합의를 세 가지 상대적으로 독립적인 하위 문제로 분해한다: (1) 리더 선출(leader election), (2) 로그 복제(log replication), (3) 안전성(safety).

Raft의 리더 선출은 무작위 타임아웃을 사용하여 간결하게 구현된다. 팔로워가 리더로부터 일정 시간 내에 heartbeat를 받지 못하면 후보(candidate)가 되어 투표를 요청하고, 과반수 투표를 얻으면 리더가 된다. 무작위 타임아웃은 선거 충돌을 효과적으로 해결한다.

로그 복제에서 리더는 클라이언트 요청을 로그 엔트리로 만들어 팔로워에게 복제한다. 과반수가 엔트리를 수신하면 커밋된 것으로 간주하며, 리더의 로그가 항상 "가장 완전한" 것임을 보장하는 Log Matching Property와 Leader Completeness Property가 핵심이다. Raft는 또한 클러스터 멤버십 변경을 위한 joint consensus 메커니즘을 제공한다.

저자들은 스탠포드 학생들을 대상으로 한 사용자 연구를 통해 Raft가 Paxos보다 이해하기 쉬움을 실증적으로 보여주었다. 이는 분산 시스템 논문에서 이해 가능성을 정량적으로 평가한 드문 사례이다.

**역사적 맥락**: Raft 이전에는 Paxos가 사실상 유일한 합의 알고리즘이었으나, 구현이 어렵고 오류가 빈번했다. Raft의 등장 이후 etcd, CockroachDB, TiKV, Consul 등 많은 산업 시스템이 Raft를 채택하여, 합의 프로토콜의 산업적 채택을 크게 가속화하였다.

**Dynamo/DynamoDB 관련**: DynamoDB 자체는 Multi-Paxos를 사용하지만, DynamoDB의 경쟁 시스템인 CockroachDB, TiDB/TiKV는 Raft를 핵심 합의 프로토콜로 사용한다. Raft의 단순성은 DynamoDB 대안 시스템의 빠른 개발과 안정성에 기여하였다.

---

## 1.10 ZAB — ZooKeeper 원자적 방송 프로토콜

### Zab: High-performance broadcast for primary-backup systems
- **제목**: "Zab: High-performance broadcast for primary-backup systems"
- **저자**: Flavio P. Junqueira, Benjamin C. Reed, Marco Serafini
- **학회**: *2011 IEEE/IFIP 41st International Conference on Dependable Systems & Networks (DSN 2011)*, pp. 245–256, June 2011
- **DOI**: 10.1109/DSN.2011.5958223

**기여 및 상세 설명**:

ZAB(ZooKeeper Atomic Broadcast)는 Apache ZooKeeper 분산 조정 서비스의 핵심 합의 프로토콜로, 프라이머리-백업 시스템을 위한 고성능 원자적 방송(atomic broadcast) 프로토콜이다. ZAB는 Paxos와 유사하지만, 프라이머리-백업 시스템의 특성에 맞게 설계되었다는 점에서 차별화된다.

ZAB의 핵심 차별점은 "에포크(epoch)" 기반의 리더십 관리이다. 리더 교체 시 새로운 에포크가 시작되며, 이전 에포크의 모든 미완료 트랜잭션이 새 리더에 의해 올바르게 처리된다. 이는 Paxos에서 리더 교체 시의 복잡한 로그 복구 문제를 구조적으로 해결한다. ZAB는 또한 트랜잭션 간의 전체 순서(total order)를 에포크 전환 시에도 보장한다.

ZAB는 세 단계로 동작한다: (1) Discovery — 새 리더가 팔로워들의 상태를 수집, (2) Synchronization — 팔로워들이 리더의 상태로 동기화, (3) Broadcast — 리더가 클라이언트 요청을 원자적으로 방송. 리더 복구 시 전송되는 상태의 양을 최소화하는 것이 ZAB의 설계 목표 중 하나이다.

**역사적 맥락**: ZooKeeper는 Yahoo!에서 개발되어 Apache 프로젝트가 되었으며, Hadoop, HBase, Kafka 등 수많은 분산 시스템의 조정 서비스로 사용된다. ZAB는 이러한 생태계의 기반이다.

**Dynamo/DynamoDB 관련**: Dynamo는 분산 조정에 gossip 프로토콜을 사용하지만, ZooKeeper/ZAB는 Kafka, HBase 등 DynamoDB의 대안/보완 시스템에서 핵심 역할을 한다. DynamoDB도 메타데이터 관리에 유사한 조정 메커니즘이 필요하다.

---

## 1.11 EPaxos — 리더 없는 합의

### There Is More Consensus in Egalitarian Parliaments
- **제목**: "There Is More Consensus in Egalitarian Parliaments"
- **저자**: Iulian Moraru, David G. Andersen, Michael Kaminsky
- **학회**: *Proceedings of the Twenty-Fourth ACM Symposium on Operating Systems Principles (SOSP 2013)*, pp. 358–372, Farmington, Pennsylvania, November 3–6, 2013
- **DOI**: 10.1145/2517349.2517350

**기여 및 상세 설명**:

EPaxos(Egalitarian Paxos)는 지정된 리더 프로세스가 없는 합의 프로토콜로, 세 가지 중요한 목표를 동시에 달성한 최초의 프로토콜이다: (1) 장애 1–2개를 허용하는 광역 네트워크에서 최적의 커밋 지연, (2) 모든 복제본에 걸친 균일한 부하 분산(따라서 높은 처리량), (3) 복제본이 느리거나 장애 시 우아한 성능 저하.

EPaxos의 핵심 혁신은 "명령 간섭(command interference)" 개념이다. 두 명령이 서로 간섭하지 않으면(즉, 서로 다른 키에 대한 연산이면) 독립적으로, 동시에 합의될 수 있다. 간섭하는 명령의 경우에만 의존성 그래프를 통해 순서를 결정한다. 간섭이 없는 일반적인 경우, 합의는 1 라운드 트립(fast path)으로 완료된다.

EPaxos에서는 클라이언트가 매 요청마다 어떤 복제본에든 보낼 수 있으며, 해당 복제본이 그 명령의 "명령 리더(command leader)"가 된다. 이로써 리더 기반 프로토콜의 병목(특정 노드에 부하 집중)과 단일 장애점 문제를 해결한다.

**역사적 맥락**: EPaxos는 Multi-Paxos와 Mencius의 한계를 극복하려는 연구이다. Multi-Paxos는 리더 병목, Mencius는 느린 노드에 의한 전체 성능 저하 문제가 있었다. EPaxos는 이 두 문제를 동시에 해결하지만, 프로토콜 복잡성이 높아 산업 채택은 제한적이다.

**Dynamo/DynamoDB 관련**: 원래 Dynamo(2007)의 리더 없는 설계 철학과 EPaxos는 유사한 동기를 공유한다: 부하 분산과 가용성. 그러나 DynamoDB(2022)는 리더 기반 Multi-Paxos를 선택하여 구현 단순성과 예측 가능한 성능을 우선시하였다.

---

## 1.12 Mencius — WAN을 위한 다중 리더 합의

### Mencius: Building Efficient Replicated State Machines for WANs
- **제목**: "Mencius: Building Efficient Replicated State Machines for WANs"
- **저자**: Yanhua Mao, Flavio P. Junqueira, Keith Marzullo
- **학회**: *8th USENIX Symposium on Operating Systems Design and Implementation (OSDI 2008)*, pp. 369–384, San Diego, California, December 8–10, 2008

**기여 및 상세 설명**:

Mencius는 WAN(Wide Area Network) 환경에서 효율적으로 동작하는 다중 리더(multi-leader) 복제 상태 머신 프로토콜이다. Paxos의 파생 프로토콜로, 합의 인스턴스 번호를 여러 서버에 사전에 할당(pre-allocating)하여 리더 역할을 라운드 로빈으로 분배한다.

Mencius의 핵심 혁신은 모든 서버가 동등하게 리더 역할을 수행하여 부하를 분산하는 것이다. 합의 시퀀스 번호가 서버들 사이에 분배되므로, 서버 i는 i, i+n, i+2n, ... 번째 인스턴스의 리더가 된다(n은 서버 수). 클라이언트 부하가 모든 사이트에 균등하게 분배될 때, 단일 리더 기반 프로토콜보다 높은 처리량을 달성한다.

그러나 Mencius의 약점은 느린 노드에 대한 취약성이다. 한 서버가 느리거나 장애가 나면, 해당 서버에 할당된 인스턴스가 완료되지 않아 전체 로그의 진행이 지연될 수 있다. 이를 해결하기 위해 "no-op" 메시지를 통한 스킵 메커니즘이 도입되었으나, 이는 추가적인 메시지 오버헤드를 발생시킨다.

**역사적 맥락**: Mencius는 Multi-Paxos(단일 리더)와 EPaxos(리더 없음) 사이의 중간 지점에 위치한다. WAN 환경에서 리더에 가까운 클라이언트만 낮은 지연을 경험하는 Multi-Paxos의 문제를 해결하려는 시도이다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)의 모든 노드가 쓰기를 받을 수 있는 설계는 Mencius의 다중 리더 접근법과 유사한 동기를 가진다. DynamoDB는 리더 기반 접근법을 채택했지만, 글로벌 테이블의 다중 리전 복제에서 Mencius와 유사한 고려사항이 존재한다.

---

# Part 2: 시간과 순서 (Time and Ordering)

## 2.1 Lamport 논리적 시계 — 분산 이벤트 순서의 기초

### Time, Clocks, and the Ordering of Events in a Distributed System
- **제목**: "Time, Clocks, and the Ordering of Events in a Distributed System"
- **저자**: Leslie Lamport
- **학회/저널**: *Communications of the ACM (CACM)*, Vol. 21, No. 7, pp. 558–565, July 1978
- **DOI**: 10.1145/359545.359563

**기여 및 상세 설명**:

이 논문은 분산 시스템 이론의 가장 기본적이고 영향력 있는 논문으로, "happened-before" 관계(→)를 정의하고 물리적 시계 없이 이벤트의 부분 순서(partial ordering)를 결정하는 논리적 시계(logical clock)를 제안하였다. 2013년 기준 11,000회 이상 인용된 분산 컴퓨팅 분야에서 가장 많이 인용된 논문 중 하나이다.

Lamport는 분산 시스템에서 "동시성"의 의미를 엄밀히 정의하였다. 두 이벤트 a, b에 대해 a→b도 b→a도 성립하지 않으면, 두 이벤트는 동시적(concurrent)이다. 이는 물리적 시간이 아닌 인과 관계(causality)에 기반한 순서 정의이다. 논리적 시계 C는 a→b이면 C(a) < C(b)를 보장하지만, 역은 성립하지 않는다(동시적 이벤트도 서로 다른 시계 값을 가질 수 있다).

논문은 또한 이 부분 순서를 전체 순서(total ordering)로 확장하는 방법을 보여주며, 이를 이용한 분산 상호 배제(mutual exclusion) 알고리즘을 제시한다. 물리적 시계의 동기화에 대한 논의도 포함하여, 물리적 시계의 드리프트가 bounded인 조건 하에서 물리적 시계와 논리적 순서를 일치시키는 방법을 기술한다.

**역사적 맥락**: 이 논문은 "분산 시스템에서 시간이란 무엇인가"라는 근본적 질문에 답한 것으로, 이후의 모든 분산 시스템 시간 연구(vector clocks, hybrid logical clocks 등)의 출발점이다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 Lamport 시계를 확장한 Vector Clock을 사용하여 이벤트의 인과 관계를 추적하고 충돌을 감지한다. Lamport 시계는 Dynamo의 일관성 메커니즘의 직접적인 이론적 선행 연구이다.

---

## 2.2 Vector Clocks — 인과 관계의 완전한 포착

### Timestamps in Message-Passing Systems That Preserve the Partial Ordering (Fidge)
- **제목**: "Timestamps in Message-Passing Systems That Preserve the Partial Ordering"
- **저자**: Colin J. Fidge
- **학회**: *Proceedings of the 11th Australian Computer Science Conference (ACSC 1988)*, pp. 56–66, February 1988

### Virtual Time and Global States of Distributed Systems (Mattern)
- **제목**: "Virtual Time and Global States of Distributed Systems"
- **저자**: Friedemann Mattern
- **학회**: *Proceedings of the International Workshop on Parallel and Distributed Algorithms*, Chateau de Bonas, France, October 1988 (published 1989)

**기여 및 상세 설명**:

Colin Fidge와 Friedemann Mattern은 1988년에 독립적으로 벡터 시계(vector clock)를 개발하였다. 벡터 시계는 Lamport의 논리적 시계의 한계—C(a) < C(b)이더라도 a→b인지 알 수 없다—를 극복하여, 인과 관계를 완전하게 포착하는 메커니즘이다.

N개의 프로세스로 구성된 시스템에서, 각 프로세스는 N차원의 정수 벡터를 유지한다. 프로세스 i가 이벤트를 발생시키면 V[i]를 증가시키고, 메시지 전송 시 벡터를 함께 보내며, 수신 시 각 차원의 최댓값을 취한 후 자신의 차원을 증가시킨다. 이로써 V(a) < V(b) (모든 차원에서 ≤이고 하나 이상에서 <)이면 a→b이고, 그 역도 성립한다.

Fidge의 1988년 논문은 호주 컴퓨터과학 학회에서 발표되었고, Mattern의 논문은 1988년 워크숍에서 발표되어 1989년에 출판되었다. 두 사람이 독립적으로 동일한 개념에 도달했다는 사실은 이 아이디어의 자연스러움을 보여준다. 그러나 실제로는 1980년대 초중반에 Liskov, Ladin, Wuu, Bernstein, Parker 등이 유사한 아이디어를 사용한 바 있으며, Fidge와 Mattern의 기여는 벡터 시계의 수학적 성질을 정식으로 확립한 것이다.

벡터 시계의 주요 단점은 공간 복잡도가 O(N)이라는 것으로, 노드 수가 많아지면 벡터 크기도 선형적으로 증가한다. 이는 대규모 분산 시스템에서의 실용성에 제약을 준다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 벡터 시계를 핵심 메커니즘으로 사용하여 동일 키에 대한 동시 쓰기를 감지하고 충돌을 해결한다. 그러나 DynamoDB(2022)에서는 리더 기반 복제로 전환하면서 벡터 시계의 필요성이 줄어들었다. 벡터 시계의 O(N) 공간 오버헤드는 Dynamo에서도 문제가 되었으며, 이를 해결하기 위해 벡터 시계의 크기를 제한하는 가지치기(pruning) 기법이 사용되었다.

---

## 2.3 Hybrid Logical Clocks — 물리적 시계와 논리적 시계의 결합

### Logical Physical Clocks and Consistent Snapshots in Globally Distributed Databases
- **제목**: "Logical Physical Clocks and Consistent Snapshots in Globally Distributed Databases"
- **저자**: Sandeep S. Kulkarni, Murat Demirbas, Deepak Madappa, Bharadwaj Avva, Marcelo Leone
- **학회**: *18th International Conference on Principles of Distributed Systems (OPODIS 2014)*, Lecture Notes in Computer Science, Vol. 8878, pp. 17–32, Springer, 2014
- **DOI**: 10.1007/978-3-319-14472-6_2

**기여 및 상세 설명**:

Hybrid Logical Clock(HLC)은 물리적 시계(NTP 시계)와 논리적 시계의 장점을 결합한 시계 메커니즘이다. HLC는 Lamport 논리적 시계처럼 인과 관계를 포착하면서도, 물리적 시계에 가까운 값을 유지하여 NTP 시계를 대체할 수 있다.

HLC의 핵심 설계는 (물리적 시간, 논리적 카운터)의 쌍으로 구성된다. 로컬 이벤트나 메시지 수신 시, HLC는 먼저 물리적 시간을 확인하고, 물리적 시간이 현재 HLC 값보다 크면 물리적 시간으로 갱신하고 카운터를 0으로 리셋한다. 물리적 시간이 같거나 작으면 카운터만 증가시킨다. 이로써 HLC 값은 항상 물리적 시간에 가까이 유지된다.

HLC의 실용적 가치는 다음과 같다: (1) 벡터 시계와 달리 공간 복잡도가 O(1)이다(노드 수에 무관), (2) NTP 동기화의 불확실성 범위 내에서 인과 순서를 보장한다, (3) wait-free 트랜잭션 순서 결정과 전역 분산 데이터베이스에서의 일관된 스냅샷 읽기에 활용할 수 있다.

**역사적 맥락**: HLC는 Google Spanner의 TrueTime과 Lamport 논리적 시계 사이의 실용적 대안으로 위치한다. TrueTime은 원자시계/GPS가 필요하지만, HLC는 표준 NTP만으로 유사한 기능을 제공한다.

**Dynamo/DynamoDB 관련**: Dynamo의 벡터 시계는 노드 수 증가에 따른 공간 오버헤드 문제가 있었다. HLC는 이러한 문제 없이 인과 순서를 제공하며, CockroachDB 등 DynamoDB의 경쟁 시스템에서 HLC를 채택하여 분산 트랜잭션의 타임스탬프 메커니즘으로 사용하고 있다.

---

## 2.4 Spanner의 TrueTime — 하드웨어 기반 시간 불확실성 관리

### Spanner: Google's Globally-Distributed Database
- **제목**: "Spanner: Google's Globally-Distributed Database"
- **저자**: James C. Corbett, Jeffrey Dean, Michael Epstein, Andrew Fikes, Christopher Frost, J.J. Furman, Sanjay Ghemawat, Andrey Gubarev, Christopher Heiser, Peter Hochschild, Wilson Hsieh, Sebastian Kanthak, Eugene Kogan, Hongyi Li, Alexander Lloyd, Sergey Melnik, David Mwaura, David Nagle, Sean Quinlan, Rajesh Rao, Lindsay Rolig, Yasushi Saito, Michal Szymaniak, Christopher Taylor, Ruth Wang, Dale Woodford
- **학회**: *10th USENIX Symposium on Operating Systems Design and Implementation (OSDI 2012)*, pp. 261–264, Hollywood, CA, October 2012

**기여 및 상세 설명**:

Google Spanner는 전 세계적으로 분산된 최초의 데이터베이스로, 외부 일관성(external consistency)을 전역 규모에서 보장한다. 이를 가능하게 한 핵심 혁신이 TrueTime API이다.

TrueTime은 시간 불확실성을 명시적으로 노출하는 API로, `TT.now()`가 반환하는 값은 단일 시간이 아니라 구간 `[earliest, latest]`이다. 이 구간은 실제 시간이 반드시 포함되는 범위를 나타내며, Google 데이터센터에서 원자시계와 GPS 수신기의 이중 물리적 시계를 사용하여 불확실성을 10ms 이내로 유지한다.

Spanner는 TrueTime의 불확실성 구간을 활용하여 트랜잭션 타임스탬프를 부여한다. 커밋 시 Spanner는 불확실성 구간만큼 대기(commit-wait)하여, 이전 트랜잭션의 타임스탬프보다 현재 트랜잭션의 실제 시간이 반드시 뒤에 오도록 보장한다. 이로써 전역적으로 일관된 스냅샷 읽기와 외부 일관성이 달성된다.

Spanner는 수백만 대의 머신, 수백 개의 데이터센터, 수조 개의 데이터베이스 행으로 확장 가능하며, 다중 버전 동시성 제어(MVCC)를 사용하여 타임스탬프의 순서 보장이 읽기와 쓰기를 차단하지 않고도 일관된 읽기를 가능하게 한다.

**역사적 맥락**: Spanner는 NoSQL과 전통적 RDBMS의 간극을 메운 획기적 시스템이다. Google은 Spanner를 Cloud Spanner로 상용화하여 외부 고객에게도 제공하고 있다.

**Dynamo/DynamoDB 관련**: DynamoDB와 Spanner는 근본적으로 다른 설계 철학을 가진다. DynamoDB는 "predictable performance at any scale"을, Spanner는 "globally consistent transactions"를 목표로 한다. TrueTime은 DynamoDB가 제공하지 않는 외부 일관성을 가능하게 하지만, 특수 하드웨어(원자시계/GPS)가 필요하다. DynamoDB는 AWS 인프라의 NTP 기반 시간 동기화를 사용하며, strong consistency는 리더 읽기를 통해 달성한다.

---

## 2.5 Causal Memory — 인과적 일관성의 형식적 정의

### Causal Memory: Definitions, Implementation, and Programming
- **제목**: "Causal Memory: Definitions, Implementation, and Programming"
- **저자**: Mustaque Ahamad, Gil Neiger, James E. Burns, Prince Kohli, Phillip W. Hutto
- **학회/저널**: *Distributed Computing*, Vol. 9, No. 1, pp. 37–49, 1995
- **DOI**: 10.1007/BF01784241

**기여 및 상세 설명**:

이 논문은 인과적 메모리(causal memory)의 최초의 형식적 정의, 구현, 그리고 프로그래밍 지침을 제공한다. 인과적 메모리는 프로세스들이 인과적으로 관련된 연산의 상대적 순서에 동의하도록 보장하는 추상화로, 순차적 일관성(sequential consistency)이나 원자적 메모리(atomic/linearizable memory)보다 약한 일관성 모델이다.

인과적 메모리의 핵심은 인과적으로 무관한(concurrent) 쓰기는 서로 다른 프로세스에서 다른 순서로 관찰될 수 있다는 것이다. 이는 전통적 메모리 일관성이 모든 프로세스가 공통된 순서에 동의하도록 요구하는 것과 대조된다. 인과적 메모리는 더 약한 보장을 제공하지만, 그로 인해 더 많은 실행을 허용하고 더 높은 동시성을 달성한다.

논문은 또한 메시지 전달 시스템에서의 인과적 메모리 구현을 제시하고, 강한 일관성 메모리를 위해 개발된 프로그램 중 인과적 메모리에서도 올바르게 동작하는 프로그램의 실용적 클래스를 식별한다. 이는 프로그래머가 약한 일관성 모델에서도 올바른 프로그램을 작성할 수 있는 지침을 제공한다.

**역사적 맥락**: 순차적 일관성보다 약하지만 유용한 일관성 모델에 대한 첫 형식적 연구로, 이후 COPS, Eiger 등 인과적 일관성을 보장하는 분산 저장소 시스템의 이론적 기반이 되었다.

**Dynamo/DynamoDB 관련**: Dynamo의 벡터 시계를 통한 인과 추적은 인과적 메모리의 실용적 구현이라 볼 수 있다. Dynamo는 인과적으로 관련된 쓰기의 순서를 보존하면서, 동시적 쓰기는 충돌로 감지하여 클라이언트에 해결을 위임한다.

---

# Part 3: 불가능성 결과와 하한 (Impossibility Results and Lower Bounds)

## 3.1 FLP 불가능성 — 합의의 근본적 한계

### Impossibility of Distributed Consensus with One Faulty Process
- **제목**: "Impossibility of Distributed Consensus with One Faulty Process"
- **저자**: Michael J. Fischer, Nancy A. Lynch, Michael S. Paterson
- **학회/저널**: *Journal of the ACM (JACM)*, Vol. 32, No. 2, pp. 374–382, April 1985
- **DOI**: 10.1145/3149.214121

**기여 및 상세 설명**:

FLP 정리는 분산 컴퓨팅 이론에서 가장 중요한 불가능성 결과로, 비동기 분산 시스템에서 단 하나의 프로세스가 crash failure를 겪는 것만으로도 결정론적 합의 알고리즘이 불가능함을 증명하였다. 더 정확히, 모든 합의 프로토콜은 종료하지 않는(nontermination) 실행 가능성을 가진다.

증명의 핵심은 "이가(bivalent)" 구성 개념이다. 프로토콜의 구성이 0으로 결정될 수도, 1로 결정될 수도 있는 상태를 이가 구성이라 한다. 증명은 두 단계로 진행된다: (1) 프로토콜에 이가 초기 구성이 존재함을 보인다 — 부분적 정확성 가정에 의해 0-가와 1-가 초기 구성이 모두 존재해야 하며, 이들 사이에 이가 구성이 있다. (2) 이가 구성에서 어떤 메시지를 처리하든 이가 구성을 유지할 수 있는 실행을 구성한다 — 이로써 프로토콜이 영원히 미결정 상태에 머무를 수 있음을 보인다.

이 증명 기법은 "이가 논법(bivalency argument)"으로 알려졌으며, 이후 분산 컴퓨팅에서 수많은 불가능성 결과와 하한 결과를 증명하는 데 사용되었다. 이 논문은 2001년 Dijkstra Prize(분산 컴퓨팅 분야 최우수 논문상)를 수상하였다.

**역사적 맥락**: FLP 이전에는 비동기 시스템에서의 합의 가능성이 열린 문제였다. FLP 이후 연구자들은 비동기 시스템에서 합의를 달성하기 위한 우회 방법을 찾게 되었으며, 이는 무작위 알고리즘(Ben-Or), 부분 동기 모델(DLS), 장애 감지기(Chandra & Toueg) 등으로 이어졌다.

**Dynamo/DynamoDB 관련**: FLP 불가능성은 Dynamo가 강한 일관성 대신 최종 일관성(eventual consistency)을 선택한 근본적 이론 배경이다. 비동기 시스템에서 합의를 보장하기 어렵다는 FLP 결과는 Dynamo의 AP(가용성+분할 허용) 설계 철학의 이론적 정당화를 제공한다. DynamoDB(2022)는 리더 선출에 타임아웃을 사용하여 FLP를 실용적으로 우회한다.

---

## 3.2 CAP 정리 — 일관성, 가용성, 분할 허용의 삼각 관계

### Brewer's CAP Conjecture (PODC 2000 Keynote)
- **제목**: "Towards Robust Distributed Systems" (기조 연설)
- **저자**: Eric A. Brewer
- **학회**: *ACM Symposium on Principles of Distributed Computing (PODC 2000)*, July 2000

### Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services
- **제목**: "Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services"
- **저자**: Seth Gilbert, Nancy A. Lynch
- **학회/저널**: *ACM SIGACT News*, Vol. 33, No. 2, pp. 51–59, June 2002
- **DOI**: 10.1145/564585.564601

**기여 및 상세 설명**:

CAP 정리는 분산 시스템에서 일관성(Consistency), 가용성(Availability), 분할 허용(Partition tolerance)의 세 가지 속성 중 동시에 두 가지만 보장할 수 있다고 주장한다. 이 정리는 2000년 Eric Brewer의 PODC 기조 연설에서 추측(conjecture)으로 제시되었고, 2002년 Seth Gilbert와 Nancy Lynch가 형식적으로 증명하여 정리(theorem)가 되었다.

Gilbert와 Lynch의 증명은 비동기 네트워크 모델에서 진행된다. 두 네트워크 구성 요소 사이의 모든 메시지가 손실되는(네트워크 분할) 상황을 가정하고, 한 구성 요소에서 쓰기가 발생한 후 다른 구성 요소에서 읽기가 발생하면, 읽기 연산이 이전 쓰기의 결과를 반환할 수 없음을 보인다. 따라서 시스템은 읽기를 거부하거나(가용성 포기) 오래된 데이터를 반환해야(일관성 포기) 한다.

부분 동기 네트워크에서도 유사한 결과가 성립하며, 타임아웃과 로컬 시계의 개념을 사용하여 증명된다.

CAP 정리는 분산 시스템 설계에 있어 근본적인 트레이드오프를 명확히 하였지만, 이후 여러 연구자들(특히 Brewer 자신과 Abadi)이 CAP의 한계를 지적하였다: 네트워크 분할은 일반적이지 않으며, 분할이 없을 때의 일관성-지연 트레이드오프가 더 실용적으로 중요하다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 CAP의 AP 시스템으로 설계되었다 — 네트워크 분할 시 가용성을 유지하고 일관성을 완화한다. DynamoDB(2022)는 strong consistency 옵션을 추가하여 사용자가 요청별로 CP(일관성 우선) 또는 AP(가용성 우선)를 선택할 수 있게 하였다. CAP 정리는 Dynamo의 설계 철학을 가장 직접적으로 설명하는 이론적 프레임워크이다.

---

## 3.3 PACELC — CAP을 넘어선 트레이드오프

### Consistency Tradeoffs in Modern Distributed Database System Design
- **제목**: "Consistency Tradeoffs in Modern Distributed Database System Design: CAP is Only Part of the Story"
- **저자**: Daniel J. Abadi
- **학회/저널**: *IEEE Computer*, Vol. 45, No. 2, pp. 37–42, February 2012
- **DOI**: 10.1109/MC.2012.33

**기여 및 상세 설명**:

Abadi는 CAP 정리가 분산 데이터베이스 설계에 미친 영향이 과대평가되었다고 주장하며, PACELC라는 새로운 프레임워크를 제안하였다. PACELC는 "Partition(P) 상황에서 Availability(A)와 Consistency(C) 중 선택하고, Else(E, 정상 상황에서) Latency(L)와 Consistency(C) 중 선택한다"를 의미한다.

Abadi의 핵심 통찰은 네트워크 분할이 상대적으로 드문 반면, 일관성과 지연 사이의 트레이드오프는 시스템 운영의 모든 순간에 존재한다는 것이다. 복제된 시스템에서 강한 일관성을 보장하려면 쓰기가 모든 복제본에 전파될 때까지 기다려야 하며, 이는 지연을 증가시킨다. 반대로 지연을 줄이려면 일관성을 약화시켜야 한다.

PACELC에 따른 분류: Dynamo, Cassandra, Riak, Cosmos DB는 PA/EL 시스템(분할 시 가용성, 정상 시 낮은 지연)이며, VoltDB, Megastore, MySQL Cluster는 PC/EC 시스템(분할 시 일관성, 정상 시에도 일관성)이다. 이 분류는 CAP의 단순한 AP/CP 이분법보다 시스템의 실제 동작을 더 정확하게 기술한다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 PACELC 분류에서 PA/EL 시스템이다: 분할 시 가용성을 유지하고, 정상 상황에서도 낮은 지연을 위해 일관성을 완화한다. DynamoDB(2022)는 strong consistency 옵션 추가로 PA/EL과 PC/EC 사이에서 요청별 선택이 가능하다. PACELC는 DynamoDB의 "튜닝 가능한 일관성(tunable consistency)" 접근법의 이론적 배경을 제공한다.

---

## 3.4 Consistency, Availability, and Convergence — CAP의 공백 메우기

### Consistency, Availability, and Convergence
- **제목**: "Consistency, Availability, and Convergence"
- **저자**: Prince Mahajan, Lorenzo Alvisi, Mike Dahlin
- **출처**: *University of Texas at Austin, Technical Report TR-11-22*, 2011

**기여 및 상세 설명**:

이 논문은 CAP 정리에서 "불가능한 영역"(강한 일관성 + 가용성)과 "가능한 영역"(약한 일관성, 예: 최종 일관성 시스템) 사이의 간극을 메우는 연구이다. 저자들은 장애 허용 분산 스토리지에서 안전성(일관성)과 활성(가용성, 수렴)의 근본적 트레이드오프를 탐구한다.

핵심 기여는 "수렴(convergence)"이라는 새로운 속성의 형식적 정의이다. 수렴은 "올바른 노드의 쓰기가 궁극적으로 다른 연결된 올바른 노드에서 관찰 가능해야 한다"는 속성으로, 최종 일관성의 직관적 기대를 형식화한다. 저자들은 기존의 최종 일관성 정의에 있는 허점을 수렴 정의로 닫는다.

주요 결과는: 비동기 시스템에서 **실시간 인과적 일관성(Real-Time Causal Consistency, RTC)**이 가용성과 수렴을 모두 보장하면서 달성 가능한 가장 강한 일관성 모델이라는 것이다. RTC보다 강한 일관성은 가용성이나 수렴을 포기해야 하며, RTC 자체는 항상 가용하고 수렴하는 시스템에서 달성 가능하다.

**Dynamo/DynamoDB 관련**: 이 결과는 Dynamo의 최종 일관성 설계에 이론적 상한을 제시한다. Dynamo가 가용성을 유지하면서 달성할 수 있는 가장 강한 일관성은 인과적 일관성이며, 그보다 강한 일관성(예: 선형화 가능성)을 원하면 가용성을 포기해야 한다.

---

# Part 4: 일관성 모델 (Consistency Models)

## 4.1 Linearizability — 동시 객체의 정확성 조건

### Linearizability: A Correctness Condition for Concurrent Objects
- **제목**: "Linearizability: A Correctness Condition for Concurrent Objects"
- **저자**: Maurice P. Herlihy, Jeannette M. Wing
- **학회/저널**: *ACM Transactions on Programming Languages and Systems (TOPLAS)*, Vol. 12, No. 3, pp. 463–492, July 1990
- **DOI**: 10.1145/78969.78972

**기여 및 상세 설명**:

선형화 가능성(linearizability)은 동시 객체에 대한 가장 강력한 일관성 모델로, "각 연산이 호출과 응답 사이의 어떤 시점에서 원자적으로(즉각적으로) 효력을 발휘하는 것처럼 보이는" 조건이다. 이는 동시 시스템의 정확성을 추상 데이터 타입의 사전/사후 조건으로 명세할 수 있게 한다.

Herlihy와 Wing은 선형화 가능성을 정의하고, 다른 정확성 조건(순차적 일관성 등)과 비교하며, 구현의 정확성을 증명하는 방법을 제시하고, 선형화 가능한 객체에 대한 추론 방법을 보여준다.

선형화 가능성의 핵심 속성 두 가지는: (1) **지역성(locality)** — 시스템이 선형화 가능하려면 각 개별 객체만 독립적으로 선형화 가능하면 된다. (2) **비차단성(nonblocking)** — 진행 중인 다른 연산과 무관하게, 보류 중인 연산에 대한 유효한 응답이 항상 존재한다. 이 두 속성은 선형화 가능성이 구성적(compositional)이고 실용적임을 보장한다.

선형화 가능성은 순차적 일관성(sequential consistency)보다 강하다. 순차적 일관성은 모든 프로세스의 연산이 어떤 순차적 순서로 실행된 것처럼 보이기만 하면 되지만, 선형화 가능성은 이 순서가 실시간 순서와도 일치해야 한다(연산 A가 연산 B 이전에 완료되었으면, A가 B 이전에 나타나야 한다).

**역사적 맥락**: 이 논문은 분산 시스템에서 일관성을 논의할 때 가장 많이 인용되는 논문 중 하나이며, Jepsen 프로젝트 등 분산 시스템 테스팅의 이론적 기반이다.

**Dynamo/DynamoDB 관련**: DynamoDB의 strong consistency 읽기는 선형화 가능성을 제공하는 것을 목표로 한다 — 리더에서 읽기를 수행하여 가장 최근에 완료된 쓰기의 결과를 반환한다. 반면 eventual consistency 읽기는 선형화 가능성을 보장하지 않는다. DynamoDB 사용자는 요청별로 이 트레이드오프를 선택한다.

---

## 4.2 Sequential Consistency — 다중 프로세서 메모리의 정확성

### How to Make a Multiprocessor Computer That Correctly Executes Multiprocess Programs
- **제목**: "How to Make a Multiprocessor Computer That Correctly Executes Multiprocess Programs"
- **저자**: Leslie Lamport
- **학회/저널**: *IEEE Transactions on Computers*, Vol. C-28, No. 9, pp. 690–691, September 1979
- **DOI**: 10.1109/TC.1979.1675439

**기여 및 상세 설명**:

이 짧은(2페이지) 논문에서 Lamport는 순차적 일관성(sequential consistency)을 정의하였다. 다중 프로세서가 순차적으로 일관되려면, "모든 실행의 결과가 모든 프로세서의 연산이 어떤 순차적 순서로 실행된 것과 동일하고, 각 개별 프로세서의 연산이 해당 프로그램이 지정한 순서대로 이 순서에 나타나야" 한다.

Lamport는 순차적 일관성을 달성하기 위한 두 가지 구현 조건을 제시하였다: (R1) 각 프로세서는 프로그램이 지정한 순서대로 메모리 요청을 발행해야 한다, (R2) 모든 프로세서의 메모리 요청은 단일 FIFO 큐에서 서비스되어야 한다.

순차적 일관성은 선형화 가능성보다 약하다 — 전체적인 순차 순서만 존재하면 되며, 이 순서가 실시간 순서와 일치할 필요는 없다. 즉, 나중에 시작된 연산이 먼저 시작된 연산보다 앞에 올 수 있다(두 연산이 서로 다른 프로세서에서 실행되고 인과적 관계가 없는 경우).

**역사적 맥락**: 원래 다중 프로세서 하드웨어를 위해 정의되었으나, 분산 시스템의 공유 메모리 모델에도 광범위하게 적용되었다. 순차적 일관성은 프로그래머에게 직관적이면서도 원자적 일관성보다 구현이 용이하여, 일관성 모델 연구의 중요한 참조점이 되었다.

**Dynamo/DynamoDB 관련**: DynamoDB의 eventually consistent 읽기는 순차적 일관성도 보장하지 않는다 — 여러 읽기가 쓰기의 순서와 다른 순서로 결과를 반환할 수 있다. Strong consistent 읽기는 순차적 일관성보다 강한 선형화 가능성을 제공한다.

---

## 4.3 Session Guarantees — 약한 일관성에서의 실용적 보장

### Session Guarantees for Weakly Consistent Replicated Data
- **제목**: "Session Guarantees for Weakly Consistent Replicated Data"
- **저자**: Douglas B. Terry, Alan J. Demers, Karin Petersen, Mike Spreitzer, Marvin Theimer, Brent B. Welch
- **학회**: *Proceedings of the Third International Conference on Parallel and Distributed Information Systems (PDIS 1994)*, pp. 140–149, Austin, TX, September 1994
- **DOI**: 10.1109/PDIS.1994.331722

**기여 및 상세 설명**:

이 논문은 약하게 일관된 복제 데이터를 사용하는 애플리케이션에 실용적인 보장을 제공하기 위한 네 가지 세션 보장(session guarantee)을 제안하였다:

1. **Read Your Writes (RYW)**: 세션 내에서 이전에 수행한 쓰기의 효과가 이후 읽기에서 반드시 반영된다.
2. **Monotonic Reads (MR)**: 세션 내에서 읽기가 반환하는 데이터는 시간이 지남에 따라 "뒤로 가지" 않는다 — 한 번 특정 버전을 읽으면 이후에는 그 버전 이상만 읽는다.
3. **Writes Follow Reads (WFR)**: 세션 내에서 읽기 후 수행한 쓰기는 인과적으로 읽기에 의존하며, 이 의존성이 모든 복제본에서 보존된다.
4. **Monotonic Writes (MW)**: 세션 내에서 쓰기의 순서가 모든 복제본에서 보존된다.

이 보장들은 개별 애플리케이션에 해당 애플리케이션의 행위와 일관된 데이터베이스 뷰를 제시하는 것을 목적으로 한다. 이 보장들은 read-any/write-any 복제 방식의 장점(고가용성, 단순성, 확장성, 비연결 운영 지원)을 유지하면서 그 위에 층을 쌓을 수 있다.

**역사적 맥락**: Xerox PARC의 Bayou 프로젝트에서 비롯된 이 연구는 이후 모바일/비연결 환경과 대규모 지리적 분산 시스템에서의 일관성 모델 연구에 큰 영향을 미쳤다.

**Dynamo/DynamoDB 관련**: DynamoDB의 eventually consistent 읽기는 이러한 세션 보장을 명시적으로 제공하지 않으나, strongly consistent 읽기는 RYW와 MR을 암묵적으로 보장한다. DynamoDB 사용 시 이러한 세션 보장의 이해는 올바른 애플리케이션 설계에 필수적이다.

---

## 4.4 Eventually Consistent — Amazon CTO의 실용적 일관성 분류

### Eventually Consistent
- **제목**: "Eventually Consistent"
- **저자**: Werner Vogels
- **학회/저널**: *Communications of the ACM (CACM)*, Vol. 52, No. 1, pp. 40–44, January 2009
- **DOI**: 10.1145/1435417.1435432

**기여 및 상세 설명**:

Amazon CTO Werner Vogels가 작성한 이 실무 지향적 논문은 대규모 분산 시스템에서의 일관성 모델을 체계적으로 분류하고, 최종 일관성(eventual consistency)의 실용적 의미를 설명한다. "전 세계 규모의 신뢰성 있는 분산 시스템 구축은 일관성과 가용성 사이의 트레이드오프를 요구한다"는 핵심 메시지를 전달한다.

Vogels는 세 가지 주요 일관성 수준을 정의한다: (1) **강한 일관성(strong consistency)** — 쓰기 완료 후 모든 후속 읽기에서 보인다, (2) **약한 일관성(weak consistency)** — 쓰기 후 읽기에서 보이지 않을 수 있으며, "비일관성 창(inconsistency window)"이 존재한다, (3) **최종 일관성(eventual consistency)** — 새로운 갱신이 없으면 궁극적으로 모든 접근이 마지막 갱신 값을 반환한다.

논문은 CAP 정리를 참조하면서, 실제 시스템에서 일관성은 이진적이 아니라 스펙트럼으로 이해해야 함을 강조한다. 비일관성 창의 크기는 통신 지연, 시스템 부하, 복제본 수 등에 의해 결정되며, 실무에서 이 창은 일반적으로 수 밀리초에서 수 초 사이이다.

**역사적 맥락**: 이 논문은 Dynamo(2007) 발표 이후 최종 일관성에 대한 산업계의 이해를 높이기 위해 작성되었으며, NoSQL 운동의 이론적 기반을 제공하였다.

**Dynamo/DynamoDB 관련**: Vogels는 Dynamo의 설계를 주도한 Amazon CTO이며, 이 논문은 사실상 Dynamo의 일관성 모델에 대한 공식 해설이다. DynamoDB의 eventually consistent 읽기가 반환하는 데이터의 특성을 이해하는 데 필수적인 참고 자료이다.

---

## 4.5 CRDTs — 강한 최종 일관성

### Conflict-Free Replicated Data Types
- **제목**: "Conflict-Free Replicated Data Types"
- **저자**: Marc Shapiro, Nuno Preguiça, Carlos Baquero, Marek Zawirski
- **학회**: *Proceedings of the 13th International Symposium on Stabilization, Safety, and Security of Distributed Systems (SSS 2011)*, Lecture Notes in Computer Science, Vol. 6976, pp. 386–400, Springer, 2011
- **DOI**: 10.1007/978-3-642-24550-3_29

**기여 및 상세 설명**:

충돌 없는 복제 데이터 타입(Conflict-Free Replicated Data Types, CRDTs)은 장애가 얼마나 발생하든 자기 안정화(self-stabilising) 방식으로 복제본이 수렴하도록 보장하는 데이터 타입이다. CRDTs는 원격 동기화 없이 어떤 복제본에서든 갱신을 수락할 수 있어, 대규모 분산 시스템에서의 성능과 확장성을 보장한다.

논문은 두 가지 CRDT 접근법을 형식화한다: (1) **상태 기반(state-based, CvRDT)** — 복제본 간에 전체 상태를 교환하고 수학적 결합(join) 연산으로 병합한다. 수렴을 위해 상태들이 결합 반격자(join semi-lattice)를 형성해야 한다. (2) **연산 기반(operation-based, CmRDT)** — 연산을 전파하고, 교환 가능(commutative)한 연산만 사용하여 적용 순서에 무관하게 동일한 결과를 보장한다.

논문은 카운터, 레지스터, 집합(OR-Set 등), 그래프 등 여러 유용한 CRDT를 연구하며, 특히 추가와 제거를 모두 지원하는 집합 CRDT의 설계와 복잡한 그래프 데이터 타입을 심도 있게 다룬다.

**역사적 맥락**: CRDTs는 최종 일관성의 "강한" 변형인 강한 최종 일관성(Strong Eventual Consistency, SEC)을 보장한다. SEC은 충돌 해결이 자동적이고 결정론적이어서, Dynamo처럼 클라이언트에게 충돌 해결을 위임할 필요가 없다. Redis, Riak, Automerge 등에서 실용화되었다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)에서 동시 쓰기 충돌 시 모든 버전을 유지하고 클라이언트가 해결하는 방식은 비효율적이었다. CRDTs는 이 문제에 대한 이론적으로 우아한 해결책을 제공한다. DynamoDB(2022)는 리더 기반 복제로 충돌 자체를 방지하지만, Dynamo에서 영감을 받은 Riak 등은 CRDTs를 적극 채택하였다.

---

# Part 5: 복제 (Replication)

## 5.1 Primary-Backup Replication — 가장 기본적인 복제 방식

### The Primary-Backup Approach
- **제목**: "The Primary-Backup Approach"
- **저자**: Navin Budhiraja, Keith Marzullo, Fred B. Schneider, Sam Toueg
- **출처**: *Distributed Systems (2nd Ed.)*, Sape Mullender (Ed.), ACM Press/Addison-Wesley, pp. 199–216, 1993 / *Dependable Computing for Critical Applications 3*, Springer, 1993
- **DOI**: 10.1007/978-3-7091-4009-3_14

**기여 및 상세 설명**:

이 논문(및 서적 챕터)은 프라이머리-백업 복제의 이론적 기반과 최적 구현을 체계적으로 연구한 것이다. 프라이머리-백업은 가장 단순하고 직관적인 복제 방식으로, 하나의 서버(프라이머리)가 모든 클라이언트 요청을 처리하고 그 결과를 백업 서버들에게 전파한다. 프라이머리 장애 시 백업이 인수(takeover)한다.

저자들은 프라이머리-백업 프로토콜의 하한(lower bound)과 최적 구현을 제시한다. 핵심 트레이드오프는 동기 복제(synchronous replication)와 비동기 복제(asynchronous replication) 사이에 있다. 동기 복제는 백업에 전파가 완료된 후에만 클라이언트에 응답하므로 장애 시 데이터 손실이 없지만 지연이 크고, 비동기 복제는 즉시 응답하지만 프라이머리 장애 시 최근 갱신이 손실될 수 있다.

**역사적 맥락**: 프라이머리-백업은 데이터베이스 복제의 가장 오래된 패턴이며, MySQL 복제, PostgreSQL 스트리밍 복제, MongoDB 복제 세트 등 현재까지 가장 광범위하게 사용된다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 프라이머리-백업이 아닌 리더 없는 쿼럼 기반 복제를 사용했다. 그러나 DynamoDB(2022)는 각 파티션에 대해 Multi-Paxos 기반 리더-팔로워 구조로 전환하였으며, 이는 본질적으로 합의 알고리즘으로 강화된 프라이머리-백업 모델이다.

---

## 5.2 Chain Replication — 높은 처리량을 위한 복제

### Chain Replication for Supporting High Throughput and Availability
- **제목**: "Chain Replication for Supporting High Throughput and Availability"
- **저자**: Robbert van Renesse, Fred B. Schneider
- **학회**: *6th Symposium on Operating Systems Design and Implementation (OSDI 2004)*, San Francisco, CA, December 2004

**기여 및 상세 설명**:

체인 복제(chain replication)는 fail-stop 스토리지 서버 클러스터를 조정하는 새로운 복제 방식으로, 높은 처리량과 가용성을 강한 일관성 보장과 함께 제공한다. 서버들은 선형 체인으로 조직되며, 역할이 명확히 분리된다.

체인의 **Head**는 모든 갱신 요청을 처리하고 다음 노드로 전파한다. 갱신은 체인을 따라 순서대로 전파되어 **Tail**에 도달하면 커밋된다. **Tail**은 모든 읽기(쿼리) 요청을 처리한다. 중간 노드들은 갱신을 전파하는 역할만 한다. 이로써 읽기는 단일 서버(Tail)에서만 처리되어 구현이 단순하며 강한 일관성이 자연스럽게 보장된다.

프라이머리-백업 대비 체인 복제의 장점은: (1) 읽기와 쓰기의 부하가 서로 다른 노드에 분산된다(Head는 쓰기, Tail은 읽기). (2) 갱신이 체인을 따라 순차적으로 전파되므로 일관성 보장이 단순하다. 단점은 갱신 지연이 체인 길이에 비례하여 증가한다는 것이다.

체인 복제의 장애 처리는 외부 "마스터" 서비스(예: ZooKeeper)가 담당한다: Head 장애 시 다음 노드가 새 Head, Tail 장애 시 이전 노드가 새 Tail, 중간 노드 장애 시 체인에서 제거하고 이전/다음 노드를 연결한다.

**역사적 맥락**: 체인 복제는 HDFS(NameNode → DataNode 체인), Amazon EBS, Windows Azure Storage 등에서 변형된 형태로 채택되었다.

**Dynamo/DynamoDB 관련**: DynamoDB는 체인 복제를 직접 사용하지 않지만, DynamoDB의 기반 스토리지 계층이나 AWS의 다른 서비스(EBS, S3)에서 체인 복제의 변형이 사용될 수 있다. 체인 복제의 강한 일관성 보장은 DynamoDB의 strong consistency 읽기 구현에 대한 대안적 접근법을 제시한다.

---

## 5.3 CRAQ — 체인 복제의 읽기 성능 향상

### Object Storage on CRAQ: High-Throughput Chain Replication for Read-Mostly Workloads
- **제목**: "Object Storage on CRAQ: High-Throughput Chain Replication for Read-Mostly Workloads"
- **저자**: Jeff Terrace, Michael J. Freedman
- **학회**: *2009 USENIX Annual Technical Conference (USENIX ATC 09)*, San Diego, CA, June 2009

**기여 및 상세 설명**:

CRAQ(Chain Replication with Apportioned Queries)는 체인 복제의 핵심 한계—읽기가 Tail 단일 노드에서만 처리됨—를 해결하여 읽기 처리량을 극적으로 향상시킨 분산 객체 스토리지 시스템이다.

CRAQ의 핵심 혁신은 모든 노드에서 읽기를 처리할 수 있도록 한 것이다. 각 노드는 객체의 최신 커밋 버전과 미커밋(dirty) 버전을 함께 유지한다. 읽기 요청을 받은 노드가 clean 상태(커밋된 최신 버전만 보유)이면 즉시 응답하고, dirty 상태이면 Tail에게 최신 커밋 버전 번호를 질의하여 해당 버전을 반환한다. 이로써 강한 일관성을 유지하면서도 읽기를 모든 노드에 분산할 수 있다.

체인 복제에서는 Head와 Tail만 클라이언트 요청을 처리하므로 체인 길이와 무관하게 처리량이 일정하지만, CRAQ에서는 읽기를 체인의 모든 노드에서 처리하므로 체인 길이에 비례하여 읽기 처리량이 선형 증가한다. 3개 노드 체인에서 CRAQ의 읽기 처리량은 원래 체인 복제의 약 3배이다.

**Dynamo/DynamoDB 관련**: DynamoDB의 eventually consistent 읽기는 어떤 복제본에서든 읽을 수 있으므로 CRAQ와 유사한 읽기 분산 효과를 가진다. CRAQ는 강한 일관성을 유지하면서도 읽기를 분산하는 방법을 보여주며, 이는 DynamoDB의 strong consistency 읽기 성능 개선에 대한 참조 아키텍처가 될 수 있다.

---

## 5.4 Optimistic Replication — 낙관적 복제 종합 서베이

### Optimistic Replication
- **제목**: "Optimistic Replication"
- **저자**: Yasushi Saito, Marc Shapiro
- **학회/저널**: *ACM Computing Surveys*, Vol. 37, No. 1, pp. 42–81, March 2005
- **DOI**: 10.1145/1057977.1057980

**기여 및 상세 설명**:

이 종합 서베이 논문은 낙관적 복제(optimistic replication) 알고리즘을 체계적으로 분류하고 분석한다. 낙관적 복제는 단기적으로 복제본의 내용이 다를 수 있도록 허용하여, 동시 작업 관행을 지원하고 저품질 통신 링크에서의 장애를 허용한다.

논문은 낙관적 복제 시스템이 직면하는 네 가지 핵심 도전과제를 식별한다: (1) **연산 순서화(ordering operations)** — 서로 다른 복제본에서 받은 갱신의 전역적 순서를 결정, (2) **충돌 감지 및 해결(detecting and resolving conflicts)** — 동시 갱신이 충돌하는지 판단하고 해결, (3) **변경 전파(propagating changes efficiently)** — 네트워크 자원을 효율적으로 사용하여 갱신을 전파, (4) **복제본 발산 제한(bounding replica divergence)** — 복제본 간의 차이가 일정 범위 내에 유지되도록 보장.

서베이는 gossip 프로토콜, anti-entropy, version vectors, conflict detection, resolution policies 등 다양한 기술을 포괄적으로 다루며, Bayou, Coda, Lotus Notes, CVS 등 실제 시스템의 사례를 분석한다.

**역사적 맥락**: 이 서베이는 낙관적 복제 분야의 결정적 참조 문헌으로, 441회 이상 인용되었다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 전형적인 낙관적 복제 시스템이다 — 쓰기를 어느 노드에서든 받고, 충돌은 나중에 감지하여 해결한다. Dynamo의 anti-entropy(Merkle tree), gossip 프로토콜, 벡터 시계 기반 충돌 감지는 이 서베이에서 분류한 기술들의 구체적 적용이다.

---

## 5.5 Lazy Replication — 의미론 활용 지연 복제

### Providing High Availability Using Lazy Replication
- **제목**: "Providing High Availability Using Lazy Replication"
- **저자**: Rivka Ladin, Barbara Liskov, Liuba Shrira, Sanjay Ghemawat
- **학회/저널**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 10, No. 4, pp. 360–391, November 1992
- **DOI**: 10.1145/138873.138877

**기여 및 상세 설명**:

이 논문은 메일이나 게시판 같은 서비스의 고가용성을 위해 복제된 데이터의 일관성을 효율적으로 유지하는 "지연 복제(lazy replication)" 방식을 제안한다. 핵심 아이디어는 서비스 연산의 의미론(semantics)을 활용하여 순서 제약을 완화하는 것이다.

논문은 세 가지 유형의 연산을 구분한다: (1) **클라이언트 순서 연산** — 클라이언트가 실행 중 동적으로 순서를 정의, (2) **서비스 순서 연산** — 서비스가 순서를 정의, (3) **전역 순서 연산** — 클라이언트 순서와 서비스 순서 모두에 대해 전역적으로 순서가 지정되어야 함. 모든 연산에 강한 순서를 부여하는 대신, 일부 애플리케이션에서는 약한 인과적 순서(causal ordering)로도 일관성을 보존할 수 있으며, 이는 더 나은 성능을 제공한다.

이 연구는 벡터 시계를 활용하여 인과적 순서를 추적하며, gossip 프로토콜을 통해 갱신을 전파한다. 저자 중 Sanjay Ghemawat는 이후 Google에서 GFS와 MapReduce를 공동 개발한 것으로 유명하다.

**Dynamo/DynamoDB 관련**: Dynamo의 최종 일관성 모델은 지연 복제의 현대적 구현이라 볼 수 있다. gossip 프로토콜을 통한 갱신 전파, 벡터 시계를 통한 인과 추적 등 Dynamo의 핵심 메커니즘은 이 논문의 아이디어를 직접 계승한다.

---

# Part 6: 분산 스토리지 기반 기술 (Distributed Storage Fundamentals)

## 6.1 LSM-Trees — 높은 쓰기 처리량을 위한 인덱스 구조

### The Log-Structured Merge-Tree (LSM-Tree)
- **제목**: "The Log-Structured Merge-Tree (LSM-Tree)"
- **저자**: Patrick O'Neil, Edward Cheng, Dieter Gawlick, Elizabeth O'Neil
- **학회/저널**: *Acta Informatica*, Vol. 33, No. 4, pp. 351–385, 1996
- **DOI**: 10.1007/s002360050048

**기여 및 상세 설명**:

LSM-Tree는 높은 빈도의 삽입과 삭제가 발생하는 파일에 대해 저비용 인덱싱을 제공하기 위해 설계된 디스크 기반 데이터 구조이다. 전통적인 B-트리와 같은 디스크 기반 인덱스 구조는 실시간 인덱스 유지를 위해 트랜잭션의 I/O 비용을 효과적으로 두 배로 만들어 총 시스템 비용을 최대 50%까지 증가시키는데, LSM-Tree는 이 문제를 해결한다.

LSM-Tree의 핵심 아이디어는 모든 쓰기를 먼저 메모리 내의 정렬된 구조(C0 트리)에 기록하고, 이 구조가 일정 크기에 도달하면 디스크의 더 큰 구조(C1 트리)와 병합(merge)하는 것이다. 쓰기는 항상 순차적 I/O로 수행되므로 랜덤 I/O를 피할 수 있다. 읽기 시에는 메모리의 C0와 디스크의 C1(및 추가적인 더 큰 레벨들)을 모두 검색해야 하므로 읽기 성능은 B-트리보다 떨어질 수 있다.

다단계(multi-level) LSM-Tree는 여러 레벨의 디스크 구조를 두어 쓰기 증폭(write amplification)을 조절한다. 각 레벨은 이전 레벨보다 크며, 한 레벨이 가득 차면 다음 레벨과 병합(compaction)된다. 이 병합 과정은 백그라운드에서 수행된다.

**역사적 맥락**: LSM-Tree는 1996년에 제안되었지만, 2000년대 후반 NoSQL 운동과 함께 폭발적으로 채택되었다. Google Bigtable, Apache Cassandra, LevelDB, RocksDB, Apache HBase, ScyllaDB 등이 LSM-Tree를 핵심 스토리지 엔진으로 사용한다.

**Dynamo/DynamoDB 관련**: DynamoDB의 스토리지 엔진은 공개적으로 상세히 기술되지 않았으나, 높은 쓰기 처리량 요구사항을 고려할 때 LSM-Tree 기반 구조를 사용할 가능성이 높다. DynamoDB의 기반 기술인 Amazon의 내부 스토리지 서비스는 LSM-Tree의 변형을 사용하는 것으로 알려져 있다. Dynamo에서 영감을 받은 Cassandra는 LSM-Tree(SSTable/Memtable)를 명시적으로 사용한다.

---

## 6.2 Log-Structured File Systems — 순차 쓰기의 위력

### The Design and Implementation of a Log-Structured File System
- **제목**: "The Design and Implementation of a Log-Structured File System"
- **저자**: Mendel Rosenblum, John K. Ousterhout
- **학회/저널**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 10, No. 1, pp. 26–52, February 1992 (Originally: SOSP 1991)
- **DOI**: 10.1145/146941.146943

**기여 및 상세 설명**:

이 논문은 모든 수정 사항을 디스크에 로그 형태로 순차적으로 기록하는 로그 구조 파일 시스템(Log-Structured File System, LFS)을 제안하였다. CPU 속도는 급격히 증가하는 반면 디스크 접근 시간은 느리게 개선되고 있고, 메모리 크기가 증가함에 따라 읽기는 캐시로 처리할 수 있지만 쓰기는 여전히 디스크 I/O가 필요하다는 관찰에서 출발한다.

LFS의 핵심 아이디어는 디스크를 하나의 큰 로그로 취급하여, 파일 데이터와 메타데이터(인덱스 정보 포함)를 모두 로그에 순차적으로 기록하는 것이다. 순차 쓰기는 랜덤 쓰기보다 수십 배 빠르므로, 쓰기 성능이 극적으로 향상된다. 로그에 인덱스 정보가 포함되어 있으므로 읽기도 효율적으로 수행할 수 있다.

로그 구조의 핵심 도전은 빠른 쓰기를 위해 디스크에 큰 연속 공간을 유지하는 것이다. LFS는 로그를 세그먼트(segment)로 나누고, 세그먼트 클리너(segment cleaner)가 백그라운드에서 단편화된 세그먼트의 활성 데이터를 압축하여 연속 공간을 확보한다.

프로토타입 구현인 Sprite LFS는 소규모 파일 쓰기에서 기존 Unix 파일 시스템 대비 약 10배의 성능을 달성하였다.

**역사적 맥락**: LFS는 LSM-Tree의 직접적인 영감이 되었으며, 현대의 SSD에 최적화된 파일 시스템(F2FS 등)과 분산 스토리지 시스템의 WAL(Write-Ahead Log) 설계에 지대한 영향을 미쳤다.

**Dynamo/DynamoDB 관련**: DynamoDB의 WAL(Write-Ahead Log)과 스토리지 계층의 순차 쓰기 최적화는 LFS의 핵심 아이디어를 계승한다. 분산 데이터베이스에서 높은 쓰기 처리량을 달성하기 위한 기본적인 접근법이다.

---

## 6.3 B-Trees — 데이터베이스 인덱싱의 표준

### Organization and Maintenance of Large Ordered Indexes
- **제목**: "Organization and Maintenance of Large Ordered Indexes"
- **저자**: Rudolf Bayer, Edward M. McCreight
- **학회/저널**: *Acta Informatica*, Vol. 1, No. 3, pp. 173–189, 1972
- **DOI**: 10.1007/BF00288683

### The Ubiquitous B-Tree
- **제목**: "The Ubiquitous B-Tree"
- **저자**: Douglas Comer
- **학회/저널**: *ACM Computing Surveys*, Vol. 11, No. 2, pp. 121–137, June 1979
- **DOI**: 10.1145/356770.356776

**기여 및 상세 설명**:

B-트리는 1972년 Bayer와 McCreight가 Boeing Research Labs에서 근무하며 발명한 자기 균형 검색 트리이다. 대규모 순서 인덱스의 조직과 유지를 위해 설계되었으며, 인덱스가 너무 커서 전체가 메인 메모리에 적재될 수 없는 상황을 전제로 한다.

B-트리의 핵심 속성은: (1) 모든 리프가 같은 깊이에 있어 검색, 삽입, 삭제가 O(log N)에 수행된다. (2) 각 노드가 많은 키를 포함하여(높은 분기 계수) 트리의 높이가 낮아 디스크 I/O가 최소화된다. (3) 삽입/삭제 시 분할(split)과 병합(merge)을 통해 자동으로 균형을 유지한다.

Comer의 1979년 서베이 논문은 B-트리가 사실상의 표준(de facto standard)이 된 이유를 분석하고, B+-트리(리프에만 데이터 포인터), B*-트리(형제 노드와의 재분배) 등 주요 변형의 상대적 장단점을 비교한다. B+-트리는 범위 질의에 최적화되어 대부분의 현대 데이터베이스가 채택한다.

**역사적 맥락**: B-트리는 50년 이상 데이터베이스 인덱싱의 표준으로 사용되고 있으며, MySQL InnoDB, PostgreSQL, SQLite 등 거의 모든 관계형 데이터베이스가 B+-트리를 사용한다.

**Dynamo/DynamoDB 관련**: DynamoDB는 키-값 저장소로서 B-트리보다는 해시 기반 파티셔닝을 사용하지만, 정렬 키(sort key)에 대한 범위 질의 지원을 위해 내부적으로 B-트리 또는 유사한 정렬 구조를 사용할 가능성이 있다. LSM-Tree의 디스크 구성 요소도 내부적으로 B-트리와 유사한 정렬 구조를 활용한다.

---

## 6.4 Bloom Filters — 공간 효율적 멤버십 테스트

### Space/Time Trade-offs in Hash Coding with Allowable Errors
- **제목**: "Space/Time Trade-offs in Hash Coding with Allowable Errors"
- **저자**: Burton Howard Bloom
- **학회/저널**: *Communications of the ACM (CACM)*, Vol. 13, No. 7, pp. 422–426, July 1970
- **DOI**: 10.1145/362686.362692

**기여 및 상세 설명**:

블룸 필터는 1970년 Burton Bloom이 제안한 공간 효율적 확률적 데이터 구조로, 원소가 집합에 속하는지를 테스트한다. 질의 결과는 "아마도 집합에 있다" 또는 "확실히 집합에 없다" 중 하나이며, 거짓 양성(false positive)은 가능하지만 거짓 음성(false negative)은 불가능하다.

블룸 필터는 m비트의 비트 배열과 k개의 해시 함수로 구성된다. 원소를 추가할 때 k개의 해시 함수를 적용하여 해당 비트 위치를 1로 설정하고, 질의 시 k개 위치가 모두 1이면 "아마도 있다", 하나라도 0이면 "확실히 없다"로 판단한다. 최적의 k 값과 1%의 오류율 하에서, 원소당 약 9.6비트만 필요하여 원소 크기와 무관한 극단적인 공간 효율성을 제공한다.

블룸 필터의 분산 시스템 응용은 광범위하다: LSM-Tree 스토리지 엔진에서 SSTable에 키가 존재하는지 빠르게 판단(불필요한 디스크 읽기 방지), 분산 캐시에서의 캐시 미스 감소, 네트워크 라우팅에서의 효율적 룩업, P2P 네트워크에서의 콘텐츠 탐색 등에 사용된다.

**역사적 맥락**: 1970년에 제안된 이 간단한 데이터 구조는 50년 이상 활발히 사용되고 있으며, Counting Bloom Filter, Cuckoo Filter, Quotient Filter 등 수많은 변형이 개발되었다.

**Dynamo/DynamoDB 관련**: Cassandra(Dynamo 영감)는 LSM-Tree의 각 SSTable에 블룸 필터를 사용하여 키 검색을 최적화한다. DynamoDB의 스토리지 엔진도 유사한 최적화를 사용할 가능성이 높다. 블룸 필터는 분산 스토리지에서 불필요한 네트워크/디스크 접근을 줄이는 핵심 기법이다.

---

# Part 7: 장애 감지 (Failure Detection)

## 7.1 Unreliable Failure Detectors — 합의를 위한 최소 장애 감지 조건

### Unreliable Failure Detectors for Reliable Distributed Systems
- **제목**: "Unreliable Failure Detectors for Reliable Distributed Systems"
- **저자**: Tushar Deepak Chandra, Sam Toueg
- **학회/저널**: *Journal of the ACM (JACM)*, Vol. 43, No. 2, pp. 225–267, March 1996
- **DOI**: 10.1145/226643.226647

**기여 및 상세 설명**:

Chandra와 Toueg는 비동기 분산 시스템에서 합의를 해결하기 위한 장애 감지기(failure detector)의 역할을 체계적으로 연구하였다. FLP 정리가 비동기 시스템에서 결정론적 합의의 불가능성을 보여주었지만, 이 논문은 불완전한(unreliable) 장애 감지기로도 합의를 해결할 수 있음을 증명하였다.

장애 감지기는 두 가지 속성으로 특성화된다: (1) **완전성(completeness)** — 실제로 장애가 난 프로세스가 궁극적으로 의심(suspect)된다, (2) **정확성(accuracy)** — 정상 프로세스가 잘못 의심되는 정도가 제한된다. 이 두 속성의 강약에 따라 장애 감지기가 분류된다.

핵심 결과는 ◇S(eventually strong) 클래스의 장애 감지기가 과반수의 올바른 프로세스가 있는 비동기 시스템에서 합의를 해결하기에 충분하다는 것이다. ◇S는 궁극적으로 하나의 올바른 프로세스가 어떤 올바른 프로세스에 의해서도 의심되지 않음을 보장한다. 더 나아가, ◇S는 합의를 해결할 수 있는 가장 약한 장애 감지기임이 증명되었다.

이 논문은 또한 합의와 원자적 방송(atomic broadcast)이 crash failure가 있는 비동기 시스템에서 서로 환원 가능(reducible)함을 보여, 위 결과가 원자적 방송에도 적용됨을 증명하였다.

**역사적 맥락**: 이 연구는 FLP 불가능성을 실용적으로 우회하는 가장 우아한 이론적 프레임워크를 제공하였다. 타임아웃 기반 장애 감지는 ◇S의 실용적 구현이며, 이는 Paxos, Raft 등 실제 합의 프로토콜의 활성(liveness)을 보장하는 메커니즘이다.

**Dynamo/DynamoDB 관련**: DynamoDB의 리더 선출과 장애 감지는 타임아웃 기반으로 구현되며, 이는 ◇S 장애 감지기의 실용적 구현이다. Dynamo(2007)의 gossip 기반 멤버십은 더 약한 장애 감지에 해당한다.

---

## 7.2 SWIM — 대규모 프로세스 그룹 멤버십

### SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol
- **제목**: "SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol"
- **저자**: Abhinandan Das, Indranil Gupta, Ashish Motivala
- **학회**: *Proceedings of the 2002 International Conference on Dependable Systems and Networks (DSN 2002)*, pp. 303–312, 2002
- **DOI**: 10.1109/DSN.2002.1028914

**기여 및 상세 설명**:

SWIM은 대규모 프로세스 그룹을 위한 확장 가능한 멤버십 프로토콜이다. 전통적인 heartbeat 프로토콜은 네트워크 부하가 그룹 크기의 제곱에 비례하여 증가하거나, 응답 시간 또는 거짓 양성 빈도를 희생해야 하는 확장성 문제를 가진다. SWIM은 이 문제를 해결한다.

SWIM의 핵심 설계는 장애 감지와 멤버십 갱신 전파를 분리하는 것이다. 장애 감지는 무작위로 선택된 프로세스에 ping을 보내고, 응답이 없으면 다른 프로세스에 대리 ping(indirect ping)을 요청하여 확인한다. 거짓 장애 감지를 줄이기 위해, 즉시 "장애"로 판정하지 않고 중간 "의심(suspected)" 상태를 도입하여, 해당 프로세스가 의심 상태에서 복구되거나 최종적으로 장애로 확정될 수 있다.

멤버십 갱신 전파는 감염 방식(infection-style) — 즉 gossip — 을 사용한다. 장애 감지 메시지에 멤버십 갱신 정보를 피기백(piggyback)하여 추가적인 네트워크 비용 없이 전파한다. 이로써 O(log N) 시간에 멤버십 변경이 전체 그룹에 전파된다.

**역사적 맥락**: SWIM은 Uber의 Ringpop, HashiCorp의 memberlist(Consul, Nomad에서 사용) 등에 구현되어 산업적으로 널리 사용된다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 gossip 기반 멤버십 프로토콜을 사용하며, 이는 SWIM과 유사한 접근법이다. 노드 장애 감지와 멤버십 갱신이 gossip을 통해 전파되어 중앙 조정자 없이 동작한다. DynamoDB(2022)는 더 중앙화된 메타데이터 관리를 사용하지만, 여전히 노드 간 장애 감지에 유사한 메커니즘이 활용된다.

---

## 7.3 Phi Accrual Failure Detector — 연속적 의심 수준

### The Phi (Φ) Accrual Failure Detector
- **제목**: "The Φ Accrual Failure Detector"
- **저자**: Naohiro Hayashibara, Xavier Défago, Rami Yared, Takuya Katayama
- **학회**: *23rd International Symposium on Reliable Distributed Systems (SRDS 2004)*, pp. 66–78, Florianópolis, Brazil, October 18–20, 2004
- **DOI**: 10.1109/RSDPS.2004.1353004

**기여 및 상세 설명**:

Phi(Φ) 축적 장애 감지기는 전통적 장애 감지기의 이진적(trust/suspect) 출력 대신, 연속적인 의심 수준(suspicion level)을 출력하는 새로운 추상화이다. 이 접근법은 유연성과 표현력을 강조하여 분산 시스템에서 장애 감지를 구현하는 기본 구성 요소로 사용될 수 있다.

Φ 값은 현재 네트워크 조건을 반영하여 동적으로 조정되는 스케일에서 표현된다. 높은 Φ 값은 해당 노드가 장애일 가능성이 높음을 의미한다. 구체적으로, Φ = -log10(1 - P(faulty))로 정의되어, Φ = 1이면 장애 확률 90%, Φ = 2이면 99%, Φ = 3이면 99.9%에 해당한다. 구성 가능한 임계값(threshold)을 사용하여 응용이 장애 판정을 제어할 수 있다.

전통적 장애 감지기가 여러 애플리케이션의 요구사항을 동시에 만족시키지 못하는 문제를 해결한다 — IP 주소 조회나 시간 동기화처럼 장애 감지를 범용 서비스로 제공하려는 시도가 성공하지 못한 이유가 이진적 출력의 한계에 있음을 지적한다.

**역사적 맥락**: Phi accrual failure detector는 Apache Cassandra와 Apache Akka에서 구현되어 실용적으로 검증되었다.

**Dynamo/DynamoDB 관련**: Cassandra(Dynamo에서 영감)는 Φ 축적 장애 감지기를 사용하여 노드 장애를 감지한다. DynamoDB 자체의 장애 감지 메커니즘은 공개되지 않았지만, 유사한 적응형 장애 감지 기법을 사용할 가능성이 있다. 연속적 의심 수준은 네트워크 조건이 변동하는 대규모 분산 시스템에서 거짓 양성을 줄이는 데 효과적이다.

---

# Part 8: 분산 트랜잭션 (Transactions in Distributed Systems)

## 8.1 Two-Phase Commit (2PC) — 분산 원자적 커밋의 원형

### Notes on Data Base Operating Systems
- **제목**: "Notes on Data Base Operating Systems"
- **저자**: Jim Gray
- **출처**: *Operating Systems: An Advanced Course*, Lecture Notes in Computer Science, Vol. 60, pp. 393–481, Springer, 1978
- **DOI**: 10.1007/3-540-08755-9_9

**기여 및 상세 설명**:

Jim Gray는 이 논문에서 분산 환경에서 원자적(atomic) 트랜잭션 커밋을 보장하는 2단계 커밋(Two-Phase Commit, 2PC) 프로토콜을 제안하였다. 2PC는 분산 트랜잭션 처리의 가장 기본적이고 영향력 있는 프로토콜이다.

2PC는 두 단계로 구성된다: (1) **투표 단계(Voting/Prepare Phase)** — 코디네이터(coordinator)가 모든 참여자(participant)에게 커밋 준비를 요청하고, 각 참여자는 "Yes"(커밋 가능) 또는 "No"(중단 필요)로 투표한다. (2) **결정 단계(Decision/Commit Phase)** — 모든 투표가 "Yes"이면 코디네이터가 커밋을 결정하고, 하나라도 "No"이면 중단을 결정하여 모든 참여자에게 알린다.

2PC의 근본적 한계는 **차단(blocking)** 문제이다. 코디네이터 장애와 참여자 장애가 동시에 발생하면, 남은 참여자들은 장애 참여자가 커밋 또는 중단 상태 중 어느 것인지 알 수 없어 트랜잭션을 완료할 수 없다. 새로 선출된 코디네이터도 하나 이상의 참여자 상태를 모르면 결정할 수 없다. 이 차단 문제는 가용성에 치명적이다.

**역사적 맥락**: 2PC는 데이터베이스 트랜잭션의 기본 프로토콜로, X/Open XA 표준의 기반이 되었다. Jim Gray는 이후 트랜잭션 처리 분야에서 1998년 튜링상을 수상하였다.

**Dynamo/DynamoDB 관련**: Dynamo(2007)는 2PC를 사용하지 않았다 — 가용성 저하를 우려했기 때문이다. 그러나 DynamoDB(2022)는 TransactWriteItems/TransactGetItems API를 통해 다중 항목 트랜잭션을 지원하며, 이는 2PC의 변형된 형태를 내부적으로 사용한다. DynamoDB의 트랜잭션은 단일 리전 내에서만 동작하여 WAN 지연 문제를 피한다.

---

## 8.2 Three-Phase Commit (3PC) — 비차단 원자적 커밋

### Nonblocking Commit Protocols
- **제목**: "Nonblocking Commit Protocols"
- **저자**: Dale Skeen
- **학회**: *Proceedings of the 1981 ACM SIGMOD International Conference on Management of Data*, pp. 133–142, 1981
- **DOI**: 10.1145/582318.582339

**기여 및 상세 설명**:

Dale Skeen은 2PC의 차단 문제를 해결하기 위한 3단계 커밋(Three-Phase Commit, 3PC) 프로토콜을 제안하였다. 3PC는 2PC의 투표 단계와 결정 단계 사이에 "커밋 준비(prepare to commit)" 단계를 추가하여, 운영 중인 사이트가 장애 참여자의 복구를 기다리지 않고 진행할 수 있도록 한다.

3PC의 세 단계: (1) **CanCommit** — 코디네이터가 참여자들에게 커밋 가능 여부를 질의, (2) **PreCommit** — 모든 참여자가 동의하면 코디네이터가 pre-commit 메시지를 전송하여 참여자들이 커밋 준비 상태로 전환, (3) **DoCommit** — 코디네이터가 최종 커밋 명령을 전송.

핵심은 pre-commit 단계에서 모든 참여자가 커밋 준비 상태에 있음이 알려지므로, 코디네이터 장애 시 새 코디네이터가 참여자들의 상태를 확인하여 안전하게 커밋 또는 중단을 결정할 수 있다는 것이다.

그러나 3PC는 네트워크 분할 상황에서는 여전히 올바르게 동작하지 않는다 — 분할된 두 그룹이 각각 다른 결정(한쪽은 커밋, 다른 쪽은 중단)을 내릴 수 있다. 이 한계로 인해 실제 시스템에서는 2PC가 더 널리 사용되며, 3PC는 주로 이론적 의의를 가진다.

**Dynamo/DynamoDB 관련**: DynamoDB는 3PC를 사용하지 않으며, 대신 Paxos 기반 합의와 결합된 트랜잭션 프로토콜을 사용한다. 3PC의 비차단 속성은 매력적이지만 네트워크 분할에 대한 취약성이 있어, Paxos/Raft 기반 합의가 더 실용적인 해결책으로 자리잡았다.

---

## 8.3 Sagas — 장기 실행 트랜잭션의 보상 기반 처리

### Sagas
- **제목**: "Sagas"
- **저자**: Hector Garcia-Molina, Kenneth Salem
- **학회**: *Proceedings of the 1987 ACM SIGMOD International Conference on Management of Data (SIGMOD 1987)*, pp. 249–259, San Francisco, CA, May 1987
- **DOI**: 10.1145/38713.38742

**기여 및 상세 설명**:

장기 실행 트랜잭션(Long Lived Transactions, LLTs)은 데이터베이스 자원을 상대적으로 오래 점유하여, 더 짧고 일반적인 트랜잭션의 종료를 지연시킨다. Garcia-Molina와 Salem은 LLT를 일련의 하위 트랜잭션(sub-transaction)으로 분해하는 **Saga** 개념을 제안하였다.

Saga는 T1, T2, ..., Tn의 트랜잭션 시퀀스로 구성되며, 각 Ti에 대해 보상 트랜잭션(compensating transaction) Ci가 정의된다. DBMS는 Saga의 모든 트랜잭션이 성공적으로 완료되거나, 실패 시점까지의 트랜잭션을 보상 트랜잭션으로 역순으로 취소하는 것을 보장한다. 예를 들어, x달러의 입금 트랜잭션은 x달러의 출금 보상 트랜잭션으로 취소된다.

Saga의 핵심 특성은 원자성이 아니라 궁극적 일관성이라는 점이다 — 중간 상태가 다른 트랜잭션에 의해 관찰될 수 있다(격리성이 보장되지 않음). 그러나 이 트레이드오프로 인해 데이터베이스 자원의 장기 잠금이 필요 없어져 동시성이 크게 향상된다.

**역사적 맥락**: 1987년에 제안된 Saga 패턴은 2010년대 마이크로서비스 아키텍처의 부상과 함께 재발견되었다. 분산 마이크로서비스에서 여러 서비스에 걸친 트랜잭션을 처리하는 표준 패턴으로 자리잡았으며, Temporal, AWS Step Functions, Uber Cadence 등에서 구현되었다.

**Dynamo/DynamoDB 관련**: DynamoDB의 트랜잭션은 단일 리전, 최대 100개 항목으로 제한된다. 이 제한을 넘는 복잡한 비즈니스 로직은 Saga 패턴으로 구현해야 한다. AWS Step Functions + DynamoDB 조합은 Saga 패턴의 일반적인 구현 방식이다.

---

## 8.4 Percolator — 대규모 증분 처리를 위한 분산 트랜잭션

### Large-scale Incremental Processing Using Distributed Transactions and Notifications
- **제목**: "Large-scale Incremental Processing Using Distributed Transactions and Notifications"
- **저자**: Daniel Peng, Frank Dabek
- **학회**: *9th USENIX Symposium on Operating Systems Design and Implementation (OSDI 2010)*, pp. 251–264, Vancouver, BC, October 2010

**기여 및 상세 설명**:

Percolator는 Google의 웹 검색 인덱스를 증분적으로 처리하기 위해 개발된 시스템으로, Bigtable 위에 다중 행 트랜잭션(multi-row transaction)과 알림(notification) 메커니즘을 구현한다. MapReduce와 같은 배치 처리 시스템이 작은 갱신을 개별적으로 처리하기 비효율적인 문제를 해결한다.

Percolator의 트랜잭션 모델은 **스냅샷 격리(snapshot isolation)**를 제공하며, Bigtable의 단일 행 트랜잭션 위에 구축된다. 핵심 메커니즘은 분산 잠금(distributed lock)으로, Bigtable의 컬럼에 잠금 상태를 저장한다. 트랜잭션은 (1) 시작 타임스탬프에서의 일관된 스냅샷을 읽고, (2) 쓰기 시 프라이머리 행에 잠금을 획득하고, (3) 2PC와 유사한 방식으로 커밋한다.

Percolator를 사용한 인덱싱 시스템으로 전환한 결과, Google은 하루에 동일한 수의 문서를 처리하면서 검색 결과의 평균 문서 연령을 50% 줄였다. 그러나 전통적 데이터베이스 아키텍처 대비 약 30배의 오버헤드가 발생하며, 이는 수천 대의 범용 머신에서의 확장성과 교환한 것이다.

**역사적 맥락**: Percolator의 트랜잭션 모델은 TiDB/TiKV에 직접 채택되어, TiDB의 분산 트랜잭션의 기반이 되었다. CockroachDB도 유사한 접근법을 사용한다.

**Dynamo/DynamoDB 관련**: DynamoDB의 트랜잭션 구현과 Percolator의 트랜잭션 모델은 서로 다른 접근법을 취한다. DynamoDB는 파티션 리더에서 트랜잭션을 조정하는 반면, Percolator는 기저 스토리지(Bigtable)의 단일 행 원자성 위에 다중 행 트랜잭션을 구축한다. TiDB(Percolator 기반)는 DynamoDB의 대안으로 분산 SQL 트랜잭션을 제공한다.

---

## 8.5 Snapshot Isolation — ANSI SQL 격리 수준의 비판과 새로운 격리 수준

### A Critique of ANSI SQL Isolation Levels
- **제목**: "A Critique of ANSI SQL Isolation Levels"
- **저자**: Hal Berenson, Phil Bernstein, Jim Gray, Jim Melton, Elizabeth O'Neil, Patrick O'Neil
- **학회**: *Proceedings of the 1995 ACM SIGMOD International Conference on Management of Data (SIGMOD 1995)*, pp. 1–10, San Jose, CA, June 1995
- **DOI**: 10.1145/223784.223785

**기여 및 상세 설명**:

이 논문은 ANSI SQL-92 표준이 정의하는 격리 수준(isolation level)의 결함을 체계적으로 분석하고, 새로운 격리 수준인 **스냅샷 격리(Snapshot Isolation, SI)**를 정의하였다. ANSI SQL-92는 격리 수준을 현상(phenomena) — Dirty Read, Non-Repeatable Read, Phantom — 으로 정의하지만, 이러한 정의가 모호하고 불완전함을 보여주었다.

저자들은 ANSI 현상 정의의 모호성을 분석하고 더 엄밀한 정의를 제시하며, ANSI 정의로는 포착되지 않는 새로운 현상(예: Lost Update, Read Skew, Write Skew)을 도입하였다. 이 확장된 현상 집합으로 격리 수준을 더 정확하게 특성화할 수 있다.

**스냅샷 격리(SI)**는 이 논문에서 정의된 중요한 다중 버전 격리 수준이다. SI 하에서 트랜잭션은 시작 시점의 데이터베이스 스냅샷에서 읽기를 수행하므로, 읽기가 쓰기에 의해 차단되지 않는다. 커밋 시에는 "First-committer-wins" 규칙이 적용되어, 동일한 데이터를 수정한 동시 트랜잭션 중 먼저 커밋한 것만 성공하고 나머지는 중단된다. SI는 Lost Update를 방지하지만 Write Skew 현상은 허용하여, 직렬화 가능(serializable) 격리보다 약하다.

**역사적 맥락**: Oracle, PostgreSQL, SQL Server 등 주요 데이터베이스가 스냅샷 격리를 구현하고 있으며, 이 논문은 데이터베이스 동시성 제어 분야에서 가장 영향력 있는 논문 중 하나이다.

**Dynamo/DynamoDB 관련**: DynamoDB의 TransactWriteItems는 직렬화 가능 격리에 가까운 보장을 제공하지만, 정확한 격리 수준은 내부 구현에 따라 다를 수 있다. DynamoDB 트랜잭션의 충돌 처리(동시에 같은 항목을 수정하는 트랜잭션 중 하나만 성공)는 SI의 First-committer-wins 규칙과 유사하다. Spanner는 직렬화 가능 스냅샷 격리를 제공하여 DynamoDB보다 강한 트랜잭션 보장을 제공한다.

---

# 시간 흐름 요약 (Comprehensive Timeline)

```
1970  Bloom Filter (Bloom, CACM)
  |
1972  B-Tree (Bayer & McCreight, Acta Informatica)
  |
1978  Lamport Clocks (Lamport, CACM)
  |
1978  2-Phase Commit (Gray, Springer LNCS)
  |
1979  Sequential Consistency (Lamport, IEEE TC)
  |
1979  Ubiquitous B-Tree survey (Comer, ACM Computing Surveys)
  |
1981  3-Phase Commit (Skeen, SIGMOD)
  |
1985  ★ FLP Impossibility (Fischer, Lynch, Paterson, JACM) ★
  |
1987  Sagas (Garcia-Molina & Salem, SIGMOD)
  |
1988  Vector Clocks (Fidge / Mattern, independently)
  |
1988  Viewstamped Replication (Oki & Liskov, PODC)
  |
1990  Linearizability (Herlihy & Wing, TOPLAS)
  |
1991  Log-Structured File System (Rosenblum & Ousterhout, SOSP/TOCS)
  |
1992  Lazy Replication (Ladin, Liskov, Shrira, Ghemawat, TOCS)
  |
1993  Primary-Backup Approach (Budhiraja et al.)
  |
1994  Session Guarantees (Terry et al., PDIS)
  |
1995  Causal Memory (Ahamad et al., Distributed Computing)
  |
1995  Snapshot Isolation (Berenson et al., SIGMOD)
  |
1996  LSM-Tree (O'Neil et al., Acta Informatica)
  |
1996  Unreliable Failure Detectors (Chandra & Toueg, JACM)
  |
1998  Paxos — The Part-Time Parliament (Lamport, TOCS)
  |
2000  ★ CAP Conjecture (Brewer, PODC keynote) ★
  |
2001  Paxos Made Simple (Lamport, SIGACT News)
  |
2002  CAP Theorem Proof (Gilbert & Lynch, SIGACT News)
  |
2002  SWIM Membership Protocol (Das et al., DSN)
  |
2004  Chain Replication (van Renesse & Schneider, OSDI)
  |
2004  Phi Accrual Failure Detector (Hayashibara et al., SRDS)
  |
2005  Optimistic Replication Survey (Saito & Shapiro, ACM Computing Surveys)
  |
2006  Fast Paxos (Lamport, Distributed Computing)
  |
2007  Paxos Made Live (Chandra et al., PODC)
  |
2007  ★ Dynamo (DeCandia et al., SOSP) ★
  |
2008  Mencius (Mao, Junqueira, Marzullo, OSDI)
  |
2009  Eventually Consistent (Vogels, CACM)
  |
2009  CRAQ (Terrace & Freedman, USENIX ATC)
  |
2010  Percolator (Peng & Dabek, OSDI)
  |
2011  ZAB (Junqueira, Reed, Serafini, DSN)
  |
2011  CRDTs (Shapiro et al., SSS)
  |
2011  Consistency, Availability, Convergence (Mahajan et al.)
  |
2012  PACELC (Abadi, IEEE Computer)
  |
2012  Spanner & TrueTime (Corbett et al., OSDI)
  |
2012  Viewstamped Replication Revisited (Liskov & Cowling, MIT TR)
  |
2013  EPaxos (Moraru, Andersen, Kaminsky, SOSP)
  |
2014  Raft (Ongaro & Ousterhout, USENIX ATC)
  |
2014  Hybrid Logical Clocks (Kulkarni et al., OPODIS)
  |
2016  Flexible Paxos (Howard, Malkhi, Spiegelman, OPODIS)
  |
2022  ★ DynamoDB (Elhemali et al., USENIX ATC) ★
```

---

# 참고문헌 (Complete References)

## 합의 프로토콜
1. L. Lamport, "The Part-Time Parliament," *ACM Trans. Comput. Syst.*, vol. 16, no. 2, pp. 133–169, 1998. DOI: 10.1145/279227.279229
2. L. Lamport, "Paxos Made Simple," *ACM SIGACT News*, vol. 32, no. 4, pp. 51–58, 2001.
3. T. D. Chandra, R. Griesemer, J. Redstone, "Paxos Made Live: An Engineering Perspective," *Proc. PODC*, pp. 398–407, 2007. DOI: 10.1145/1281100.1281103
4. L. Lamport, "Fast Paxos," *Distributed Computing*, vol. 19, no. 2, pp. 79–103, 2006. DOI: 10.1007/s00446-006-0005-x
5. H. Howard, D. Malkhi, A. Spiegelman, "Flexible Paxos: Quorum Intersection Revisited," *Proc. OPODIS*, 2016. DOI: 10.4230/LIPIcs.OPODIS.2016.25
6. B. M. Oki, B. Liskov, "Viewstamped Replication: A New Primary Copy Method to Support Highly-Available Distributed Systems," *Proc. PODC*, pp. 8–17, 1988. DOI: 10.1145/62546.62549
7. B. Liskov, J. Cowling, "Viewstamped Replication Revisited," *MIT-CSAIL-TR-2012-021*, 2012.
8. D. Ongaro, J. Ousterhout, "In Search of an Understandable Consensus Algorithm," *Proc. USENIX ATC*, pp. 305–319, 2014.
9. F. P. Junqueira, B. C. Reed, M. Serafini, "Zab: High-performance broadcast for primary-backup systems," *Proc. DSN*, pp. 245–256, 2011. DOI: 10.1109/DSN.2011.5958223
10. I. Moraru, D. G. Andersen, M. Kaminsky, "There Is More Consensus in Egalitarian Parliaments," *Proc. SOSP*, pp. 358–372, 2013. DOI: 10.1145/2517349.2517350
11. Y. Mao, F. P. Junqueira, K. Marzullo, "Mencius: Building Efficient Replicated State Machines for WANs," *Proc. OSDI*, pp. 369–384, 2008.

## 시간과 순서
12. L. Lamport, "Time, Clocks, and the Ordering of Events in a Distributed System," *Commun. ACM*, vol. 21, no. 7, pp. 558–565, 1978. DOI: 10.1145/359545.359563
13. C. J. Fidge, "Timestamps in Message-Passing Systems That Preserve the Partial Ordering," *Proc. ACSC*, pp. 56–66, 1988.
14. F. Mattern, "Virtual Time and Global States of Distributed Systems," *Proc. Intl. Workshop on Parallel and Distributed Algorithms*, 1988.
15. S. S. Kulkarni, M. Demirbas, D. Madappa, B. Avva, M. Leone, "Logical Physical Clocks and Consistent Snapshots in Globally Distributed Databases," *Proc. OPODIS*, pp. 17–32, 2014. DOI: 10.1007/978-3-319-14472-6_2
16. J. C. Corbett et al., "Spanner: Google's Globally-Distributed Database," *Proc. OSDI*, pp. 261–264, 2012.
17. M. Ahamad, G. Neiger, J. E. Burns, P. Kohli, P. W. Hutto, "Causal Memory: Definitions, Implementation, and Programming," *Distributed Computing*, vol. 9, no. 1, pp. 37–49, 1995. DOI: 10.1007/BF01784241

## 불가능성 결과
18. M. J. Fischer, N. A. Lynch, M. S. Paterson, "Impossibility of Distributed Consensus with One Faulty Process," *J. ACM*, vol. 32, no. 2, pp. 374–382, 1985. DOI: 10.1145/3149.214121
19. S. Gilbert, N. Lynch, "Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services," *ACM SIGACT News*, vol. 33, no. 2, pp. 51–59, 2002. DOI: 10.1145/564585.564601
20. D. J. Abadi, "Consistency Tradeoffs in Modern Distributed Database System Design," *IEEE Computer*, vol. 45, no. 2, pp. 37–42, 2012. DOI: 10.1109/MC.2012.33
21. P. Mahajan, L. Alvisi, M. Dahlin, "Consistency, Availability, and Convergence," *Univ. of Texas at Austin TR-11-22*, 2011.

## 일관성 모델
22. M. P. Herlihy, J. M. Wing, "Linearizability: A Correctness Condition for Concurrent Objects," *ACM Trans. Program. Lang. Syst.*, vol. 12, no. 3, pp. 463–492, 1990. DOI: 10.1145/78969.78972
23. L. Lamport, "How to Make a Multiprocessor Computer That Correctly Executes Multiprocess Programs," *IEEE Trans. Comput.*, vol. C-28, no. 9, pp. 690–691, 1979. DOI: 10.1109/TC.1979.1675439
24. D. B. Terry, A. J. Demers, K. Petersen, M. Spreitzer, M. Theimer, B. B. Welch, "Session Guarantees for Weakly Consistent Replicated Data," *Proc. PDIS*, pp. 140–149, 1994. DOI: 10.1109/PDIS.1994.331722
25. W. Vogels, "Eventually Consistent," *Commun. ACM*, vol. 52, no. 1, pp. 40–44, 2009. DOI: 10.1145/1435417.1435432
26. M. Shapiro, N. Preguiça, C. Baquero, M. Zawirski, "Conflict-Free Replicated Data Types," *Proc. SSS*, pp. 386–400, 2011. DOI: 10.1007/978-3-642-24550-3_29

## 복제
27. N. Budhiraja, K. Marzullo, F. B. Schneider, S. Toueg, "The Primary-Backup Approach," in *Distributed Systems (2nd Ed.)*, ACM Press, pp. 199–216, 1993.
28. R. van Renesse, F. B. Schneider, "Chain Replication for Supporting High Throughput and Availability," *Proc. OSDI*, 2004.
29. J. Terrace, M. J. Freedman, "Object Storage on CRAQ: High-Throughput Chain Replication for Read-Mostly Workloads," *Proc. USENIX ATC*, 2009.
30. Y. Saito, M. Shapiro, "Optimistic Replication," *ACM Computing Surveys*, vol. 37, no. 1, pp. 42–81, 2005. DOI: 10.1145/1057977.1057980
31. R. Ladin, B. Liskov, L. Shrira, S. Ghemawat, "Providing High Availability Using Lazy Replication," *ACM Trans. Comput. Syst.*, vol. 10, no. 4, pp. 360–391, 1992. DOI: 10.1145/138873.138877

## 분산 스토리지 기반
32. P. O'Neil, E. Cheng, D. Gawlick, E. O'Neil, "The Log-Structured Merge-Tree (LSM-Tree)," *Acta Informatica*, vol. 33, no. 4, pp. 351–385, 1996. DOI: 10.1007/s002360050048
33. M. Rosenblum, J. K. Ousterhout, "The Design and Implementation of a Log-Structured File System," *ACM Trans. Comput. Syst.*, vol. 10, no. 1, pp. 26–52, 1992. DOI: 10.1145/146941.146943
34. R. Bayer, E. M. McCreight, "Organization and Maintenance of Large Ordered Indexes," *Acta Informatica*, vol. 1, no. 3, pp. 173–189, 1972. DOI: 10.1007/BF00288683
35. D. Comer, "Ubiquitous B-Tree," *ACM Computing Surveys*, vol. 11, no. 2, pp. 121–137, 1979. DOI: 10.1145/356770.356776
36. B. H. Bloom, "Space/Time Trade-offs in Hash Coding with Allowable Errors," *Commun. ACM*, vol. 13, no. 7, pp. 422–426, 1970. DOI: 10.1145/362686.362692

## 장애 감지
37. T. D. Chandra, S. Toueg, "Unreliable Failure Detectors for Reliable Distributed Systems," *J. ACM*, vol. 43, no. 2, pp. 225–267, 1996. DOI: 10.1145/226643.226647
38. A. Das, I. Gupta, A. Motivala, "SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol," *Proc. DSN*, pp. 303–312, 2002. DOI: 10.1109/DSN.2002.1028914
39. N. Hayashibara, X. Défago, R. Yared, T. Katayama, "The Φ Accrual Failure Detector," *Proc. SRDS*, pp. 66–78, 2004. DOI: 10.1109/RSDPS.2004.1353004

## 분산 트랜잭션
40. J. Gray, "Notes on Data Base Operating Systems," *Operating Systems: An Advanced Course*, LNCS vol. 60, pp. 393–481, 1978. DOI: 10.1007/3-540-08755-9_9
41. D. Skeen, "Nonblocking Commit Protocols," *Proc. SIGMOD*, pp. 133–142, 1981. DOI: 10.1145/582318.582339
42. H. Garcia-Molina, K. Salem, "Sagas," *Proc. SIGMOD*, pp. 249–259, 1987. DOI: 10.1145/38713.38742
43. D. Peng, F. Dabek, "Large-scale Incremental Processing Using Distributed Transactions and Notifications," *Proc. OSDI*, pp. 251–264, 2010.
44. H. Berenson, P. Bernstein, J. Gray, J. Melton, E. O'Neil, P. O'Neil, "A Critique of ANSI SQL Isolation Levels," *Proc. SIGMOD*, pp. 1–10, 1995. DOI: 10.1145/223784.223785
