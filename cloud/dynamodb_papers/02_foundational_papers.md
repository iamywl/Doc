# Dynamo를 가능하게 한 기반 논문들: 포괄적 학술 리서치

Dynamo(SOSP 2007)는 기존의 분산 시스템 이론과 기법들을 독창적으로 조합하여 설계되었다. 이 문서는 Dynamo가 직접 참조하거나 영향을 받은 **모든** 기반 논문들을 학술적 깊이로 정리한다. 각 논문의 핵심 기여, Dynamo에 대한 구체적 영향, 그리고 해당 논문이 해결한 문제를 상세히 분석한다.

**원논문**: Giuseppe DeCandia, Deniz Hastorun, Madan Jampani, Gunavardhan Kakulapati, Avinash Lakshman, Alex Pilchin, Swaminathan Sivasubramanian, Peter Vosshall, Werner Vogels. "Dynamo: Amazon's Highly Available Key-value Store." *Proceedings of the 21st ACM Symposium on Operating Systems Principles (SOSP '07)*, pp. 205-220, October 2007. DOI: 10.1145/1294261.1294281

---

## 1. Consistent Hashing -- 데이터 파티셔닝의 기반

### 1.1 Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web

- **저자**: David Karger, Eric Lehman, Frank Thomson Leighton, Rina Panigrahy, Matthew Levine, Daniel Lewin
- **학회**: **STOC 1997** (ACM Symposium on Theory of Computing), pp. 654-663
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)
- **DOI**: 10.1145/258533.258660

**핵심 기여 (상세)**:

이 논문은 분산 캐싱 시스템에서 핫스팟(hot spot) 문제를 해결하기 위해 consistent hashing이라는 혁신적 개념을 제안했다. 전통적인 모듈러 해싱(hash(key) mod N)에서는 서버 수 N이 변경되면 거의 모든 키가 재배치되어야 하는 심각한 문제가 있었다. Karger 등은 해시 함수의 출력 공간을 원(ring)으로 매핑하고, 키와 서버 모두를 이 링 위에 배치하여 키가 시계 방향으로 가장 가까운 서버에 할당되도록 설계했다.

이 접근법의 핵심 수학적 성질은 서버 한 대가 추가되거나 제거될 때 평균적으로 K/N개의 키만 재배치된다는 것이다(K는 전체 키 수, N은 서버 수). 이는 기존 모듈러 해싱의 O(K) 재배치에 비해 극적인 개선이다. 또한 논문은 random tree를 이용한 캐싱 프로토콜도 제안하여, 웹 캐시 서버들이 요청을 효율적으로 분산 처리할 수 있게 했다.

논문은 부하 불균형 문제를 해결하기 위해 각 서버에 여러 개의 "가상 포인트(virtual points)"를 할당하는 기법도 제안했다. 이것이 나중에 Dynamo의 virtual node 개념의 직접적 원형이 된다. 가상 포인트를 O(log N)개 할당하면 부하가 높은 확률로 균등하게 분배된다는 것을 증명했다.

**Dynamo에서의 구체적 활용**: Dynamo는 이 consistent hashing을 데이터 파티셔닝의 핵심 기법으로 채택했다. 각 데이터 항목의 키를 MD5 해시하여 128비트 해시 링 위에 배치하고, 시계 방향으로 가장 가까운 노드에 할당한다. Dynamo는 여기에 **virtual node** 개념을 확장 적용하여, 각 물리 노드가 여러 개의 가상 노드를 담당하게 함으로써 이질적인(heterogeneous) 하드웨어 환경에서도 부하를 균등하게 분배할 수 있게 했다.

**해결한 문제**: 노드의 동적 추가/제거가 빈번한 Amazon의 대규모 분산 환경에서, 최소한의 데이터 이동으로 부하를 재분배할 수 있는 파티셔닝 기법이 필요했다.

### 1.2 Novel Architectures for P2P Applications: the Continuous-Discrete Approach

- **저자**: Moni Naor, Udi Wieder
- **학회**: **SPAA 2003** (ACM Symposium on Parallelism in Algorithms and Architectures), pp. 50-59
- **DOI**: 10.1145/777412.777422

**핵심 기여 (상세)**:

Naor와 Wieder는 consistent hashing의 부하 균형 특성을 더욱 엄밀하게 분석하고, Distance Halving이라는 새로운 오버레이 네트워크를 제안했다. 이 연구는 Chord, CAN 등 기존 DHT의 consistent hashing 기반 데이터 매핑에서 발생하는 부하 불균형 문제에 대한 이론적 하한(lower bound)을 제시하고, 이를 달성하는 최적 알고리즘을 설계했다.

특히 논문은 각 서버의 캐시가 평균적으로 O(1)번 히트되도록 보장하는 swamp-prevention 기법을 제시하여, 핫스팟 없이 균등한 부하 분배가 가능함을 증명했다. 이는 consistent hashing의 이론적 토대를 강화하고, Dynamo와 같은 실제 시스템에서 virtual node의 수를 결정하는 이론적 근거를 제공했다.

**Dynamo에서의 영향**: Dynamo의 virtual node 전략과 부하 균형 최적화에 이론적 배경을 제공했다.

### 1.3 Simple Efficient Load Balancing Algorithms for Peer-to-Peer Systems

- **저자**: John Byers, Jeffrey Considine, Michael Mitzenmacher
- **학회**: **SPAA 2003** (ACM Symposium on Parallelism in Algorithms and Architectures), pp. 36-43
- **DOI**: 10.1145/777412.777420

**핵심 기여**: "Power of two choices" 기법을 P2P 시스템의 부하 균형에 적용하여, consistent hashing에서 발생하는 부하 불균형 문제를 효율적으로 해결하는 방법을 제시했다. 각 항목을 d개의 후보 위치 중 가장 적은 부하를 가진 곳에 배치하면, 최대 부하가 O(log log N / log d + 1)로 극적으로 줄어든다.

**Dynamo에서의 영향**: Dynamo의 virtual node 부하 균형 전략의 이론적 근거 중 하나로, 노드 간 부하 편차를 최소화하는 데 기여했다.

---

## 2. Vector Clocks와 버전 추적 -- 인과적 순서 추적

### 2.1 Time, Clocks, and the Ordering of Events in a Distributed System

- **저자**: Leslie Lamport
- **학회**: *Communications of the ACM*, Vol. 21, No. 7, pp. 558-565, July 1978
- **DOI**: 10.1145/359545.359563
- 컴퓨터 과학 역사상 가장 많이 인용된 논문 중 하나

**핵심 기여 (상세)**:

Lamport의 이 획기적 논문은 분산 시스템에서 이벤트 순서를 정의하는 "happened-before" 관계(->)를 공식적으로 정의했다. 물리적 시간이 아닌 논리적 시간(logical time)의 개념을 도입하여, 분산 시스템에서 시계 동기화 없이도 이벤트의 인과 관계를 추적할 수 있음을 보였다.

Lamport 시계(Lamport clock)는 단일 정수 카운터로 구성되며, 세 가지 규칙으로 동작한다: (1) 각 프로세스는 이벤트 발생 시 카운터를 증가, (2) 메시지 전송 시 현재 카운터 값을 포함, (3) 메시지 수신 시 자신의 카운터와 수신된 카운터의 최대값에 1을 더함. 이를 통해 a -> b이면 C(a) < C(b)가 보장된다.

그러나 Lamport 시계의 한계는 역이 성립하지 않는다는 것이다. 즉 C(a) < C(b)라고 해서 a -> b라고 결론지을 수 없다. 두 이벤트가 동시에 발생했는지(concurrent) 아닌지를 구별할 수 없는 것이다. 이 한계가 바로 vector clock의 필요성을 야기했다.

**Dynamo에서의 영향**: Lamport 시계는 vector clock의 직접적 전신이다. Dynamo가 채택한 vector clock 기법은 Lamport의 happened-before 관계를 확장한 것이다.

**해결한 문제**: 물리적 시계 동기화 없이 분산 시스템에서 이벤트 순서를 추적하는 이론적 기반을 제공했다.

### 2.2 Timestamps in Message-Passing Systems That Preserve the Partial Ordering

- **저자**: Colin J. Fidge
- **학회**: *Proceedings of the 11th Australian Computer Science Conference (ACSC '88)*, Vol. 10, No. 1, pp. 56-66, 1988

**핵심 기여 (상세)**:

Fidge는 Lamport 시계의 근본적 한계를 해결하기 위해 vector clock을 독립적으로 발명했다. Lamport 시계가 단일 정수 카운터를 사용하는 반면, vector clock은 시스템의 각 프로세스마다 하나의 카운터를 가지는 벡터를 사용한다. N개의 프로세스가 있는 시스템에서 각 프로세스는 N차원 벡터를 유지한다.

Vector clock의 핵심 혁신은 **양방향 관계 추적**이 가능하다는 것이다. 두 이벤트 a, b에 대해: VC(a) < VC(b)이면 a -> b (인과적 선행), VC(a) > VC(b)이면 b -> a (인과적 후행), VC(a)와 VC(b)가 비교불가능하면 a || b (동시 발생). 이를 통해 Lamport 시계에서 불가능했던 동시 발생 이벤트 감지가 가능해졌다.

Fidge의 구체적 알고리즘: 프로세스 i는 벡터 VC를 유지하고, (1) 로컬 이벤트 시 VC[i]++, (2) 메시지 전송 시 현재 VC를 첨부, (3) 메시지 수신 시 VC[j] = max(VC[j], received_VC[j]) for all j, 그리고 VC[i]++.

**Dynamo에서의 구체적 활용**: Dynamo는 각 데이터 항목에 vector clock을 첨부하여, 여러 복제본에서 동시에 발생한 업데이트를 정확히 감지한다. 읽기 시 여러 버전이 반환되면 클라이언트가 충돌을 해결한다.

**해결한 문제**: 동시 쓰기 감지와 인과적 순서 추적의 정확성을 보장하여, Dynamo의 "always writable" 정책에서 발생하는 충돌을 식별할 수 있게 했다.

### 2.3 Virtual Time and Global States of Distributed Systems

- **저자**: Friedemann Mattern
- **학회**: *Parallel and Distributed Algorithms* (North-Holland), pp. 215-226, 1989

**핵심 기여 (상세)**:

Mattern은 Fidge와 독립적으로 vector clock을 발명했으며, 보다 광범위한 이론적 프레임워크를 제시했다. Mattern의 핵심 통찰은 선형적으로 정렬된 시간 구조가 분산 시스템에 부적합하며, 대신 벡터 시계로 구성되는 **부분 순서(partial order)** 구조가 인과성의 동형 사상(isomorphism)을 제공한다는 것이다.

Mattern은 벡터 시계가 형성하는 **래티스(lattice)** 구조를 엄밀하게 분석하고, 이 구조가 분산 시스템의 글로벌 상태(global state) 계산에 어떻게 활용될 수 있는지를 보였다. 또한 Parker 등의 1983년 version vector 연구를 인용하며, 자신의 "time vector"가 Parker의 version vector와 "매우 유사(very similar)"하다고 명시적으로 언급했다.

Mattern의 논문은 분산 디버깅, 글로벌 스냅샷 생성, 분산 종료 감지 등 vector clock의 다양한 응용을 탐구하여, 이 기법의 범용성을 보여주었다.

**Dynamo에서의 영향**: Fidge와 함께 Dynamo의 vector clock 기반 버전 추적의 이론적 토대를 형성했다.

### 2.4 Detection of Mutual Inconsistency in Distributed Systems

- **저자**: D. Stott Parker Jr., Gerald J. Popek, Gerard Rudisin, Allen Stoughton, Bruce J. Walker, Evelyn Walton, Johanna M. Chow, David Edwards, Stephen Kiser, Charles Kline
- **학회**: *IEEE Transactions on Software Engineering*, Vol. SE-9, No. 3, pp. 240-247, May 1983
- **DOI**: 10.1109/TSE.1983.236733

**핵심 기여 (상세)**:

Parker 등은 vector clock보다 5년 앞서 "version vector"라는 개념을 도입했다. 이 논문은 분산 파일 시스템에서 복제본 간의 상호 비일관성(mutual inconsistency)을 감지하는 메커니즘을 제안했다. LOCUS라는 UCLA의 로컬 네트워크 운영체제 설계에 사용되었다.

Version vector는 각 복제본에 대해 카운터를 유지하며, 업데이트 시 해당 복제본의 카운터만 증가시킨다. 두 version vector를 비교하여 한쪽이 다른 쪽을 지배(dominate)하면 인과적 순서가 존재하고, 어느 쪽도 지배하지 않으면 동시 업데이트(충돌)가 발생한 것이다.

Vector clock과 version vector의 핵심 차이는 사용 목적에 있다. Vector clock은 이벤트의 인과 순서를 추적하는 반면, version vector는 데이터 복제본의 상태 진화를 추적한다. Dynamo는 사실상 이 두 개념을 결합하여 사용한다.

**Dynamo에서의 영향**: Dynamo의 버전 추적 메커니즘은 vector clock이라고 명명되었지만, 실제 동작은 version vector에 더 가깝다. 각 노드가 업데이트 시 자신의 카운터를 증가시키는 방식이 Parker의 version vector와 동일하다.

### 2.5 Dotted Version Vectors: Logical Clocks for Optimistic Replication

- **저자**: Nuno Preguica, Carlos Baquero, Paulo Sergio Almeida, Victor Fonte, Ricardo Goncalves
- **학회**: *arXiv:1011.5808*, 2010; 이후 학술지 게재
- **DOI**: 10.48550/arXiv.1011.5808

**핵심 기여 (상세)**:

Dotted Version Vector(DVV)는 Dynamo 이후에 발견된 Dynamo의 vector clock 구현의 한계를 직접 해결하기 위해 개발되었다. 기존 version vector에서는 클라이언트가 여러 번 동일 키를 업데이트하면 false conflict(실제로는 충돌이 아닌데 충돌로 판단하는 상황)가 발생할 수 있다.

DVV의 핵심 혁신은 마지막 이벤트를 version vector 자체와 분리하여 별도의 "dot"(id, counter 쌍)으로 표현하는 것이다. 예를 들어 dot (A, 3)은 오직 A의 이벤트 3만을 나타내며, 이전 이벤트 1, 2와는 독립적이다. 이를 통해 정확한 인과성 추적이 가능하며, 클라이언트 수와 서버 수 모두에서 확장성이 보장된다.

DVV는 Riak 데이터베이스에서 Dynamo의 vector clock을 대체하여 사용되었으며, Dynamo의 설계 한계를 극복한 직접적 후속 연구이다.

**Dynamo에서의 영향**: Dynamo의 vector clock 구현에서 발견된 문제점(sibling explosion, false conflict)에 대한 직접적 해결책을 제공했다.

### 2.6 Interval Tree Clocks: A Logical Clock for Dynamic Systems

- **저자**: Paulo Sergio Almeida, Carlos Baquero, Victor Fonte
- **학회**: *OPODIS 2008* (12th International Conference on Principles of Distributed Systems), LNCS 5401, pp. 259-274, 2008
- **DOI**: 10.1007/978-3-540-92221-6_18

**핵심 기여 (상세)**:

Interval Tree Clock(ITC)은 동적 시스템(프로세스가 자주 생성되고 소멸되는 환경)에서의 인과성 추적 문제를 해결한다. 기존 vector clock과 version vector는 전역 고유 식별자(globally unique identifier)에 대한 정수 카운터 매핑에 의존하는데, 동적 시스템에서는 식별자 관리가 매우 어렵다.

ITC는 구간 트리(interval tree) 기반의 표현을 사용하여, 전역 식별자나 전역 조율 없이 완전히 분산적으로 프로세스/복제본을 생성할 수 있다. 표현의 크기가 현존하는 엔티티 수에 자동으로 적응하여 확장 또는 축소되며, 고정된 수의 엔티티에서도 vector clock이나 version vector의 대체재로 사용할 수 있을 만큼 컴팩트하다.

**Dynamo에서의 영향**: Dynamo의 vector clock은 클러스터 크기 증가 시 벡터 크기도 증가하는 문제가 있다. ITC는 이 문제에 대한 후속 해결책을 제시했다.

---

## 3. Gossip Protocols -- 분산 정보 전파와 멤버십 관리

### 3.1 Epidemic Algorithms for Replicated Database Maintenance

- **저자**: Alan Demers, Dan Greene, Carl Hauser, Wes Irish, John Larson, Sue Manning, Scott Shenker, Howard Sturgis, Dan Swinehart, Doug Terry, Don Woods
- **학회**: **PODC 1987** (ACM Symposium on Principles of Distributed Computing), Vancouver, August 1987; 또한 Xerox PARC Technical Report CSL-89-1
- **BK21 등급**: 인정 IF **3** (Top-tier 우수)
- **DOI**: 10.1145/41840.41841

**핵심 기여 (상세)**:

이 논문은 분산 데이터베이스의 복제본 간 일관성을 유지하기 위한 랜덤화 알고리즘을 제안했으며, gossip protocol의 시초가 된 기념비적 연구이다. Xerox의 Clearinghouse 네임 서버에서 발생하는 실제 문제를 해결하기 위해 개발되었다.

논문은 두 가지 핵심 접근법을 제시한다. **Anti-entropy**는 주기적으로 임의의 두 복제본을 선택하여 모든 차이를 동기화하는 방식이다(push, pull, push-pull 변형 존재). **Rumor mongering**(루머 퍼뜨리기)은 새로운 업데이트를 가진 노드가 임의의 이웃에게 정보를 전달하되, 이미 대부분이 알고 있다고 판단되면 전파를 중단하는 방식이다.

전염병학(epidemiology) 문헌의 SIR(Susceptible-Infected-Recovered) 모델을 활용하여 이 알고리즘들의 수학적 분석을 수행했다. N개의 노드가 있을 때, O(N log N) 메시지로 모든 노드에 업데이트가 전파됨을 증명했다. anti-entropy는 확실한 수렴을 보장하지만 대역폭 비용이 높고, rumor mongering은 효율적이지만 소수의 노드가 업데이트를 놓칠 수 있다. 실용적으로는 두 기법을 결합하여 사용한다.

**Dynamo에서의 구체적 활용**: Dynamo는 gossip 프로토콜을 세 가지 핵심 기능에 활용한다: (1) **클러스터 멤버십 관리** -- 노드의 참여/이탈 정보를 가십으로 전파, (2) **파티션 매핑 전파** -- consistent hash ring의 토큰 매핑 정보를 가십으로 공유, (3) **장애 감지 정보 전파** -- 각 노드가 관찰한 장애 정보를 가십으로 확산. 이를 통해 중앙 코디네이터 없이 완전한 분산 관리가 가능하다.

**해결한 문제**: 중앙 집중식 조율자 없이 대규모 분산 시스템에서 메타데이터와 상태 정보를 효율적으로 전파하는 방법이 필요했다.

### 3.2 SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol

- **저자**: Abhinandan Das, Indranil Gupta, Ashish Motivala
- **학회**: **DSN 2002** (International Conference on Dependable Systems and Networks), pp. 303-312, June 2002
- **DOI**: 10.1109/DSN.2002.1028914

**핵심 기여 (상세)**:

SWIM은 기존 heartbeat 기반 장애 감지 및 멤버십 프로토콜의 확장성 문제를 근본적으로 해결했다. 기존의 heartbeat 방식에서는 모든 노드가 모든 다른 노드에게 주기적으로 heartbeat을 전송하여 O(N^2)의 메시지 복잡도를 가지며, false positive(정상 노드를 장애로 오판)와 감지 시간 사이의 트레이드오프가 심각했다.

SWIM의 핵심 혁신은 장애 감지(failure detection)와 멤버십 업데이트 전파(membership update dissemination)를 분리한 것이다. **장애 감지**: 각 프로토콜 주기 T'에서 노드 M_i는 임의의 노드 M_j에게 ping을 전송한다. M_j가 응답하지 않으면, M_i는 k개의 다른 멤버를 임의로 선택하여 M_j에 대한 간접 ping을 요청한다(ping-req). 이 중 아무도 M_j로부터 응답을 받지 못하면 M_j를 의심(suspect) 상태로 표시한다. **멤버십 전파**: 멤버십 변경 정보(join, leave, suspect, faulty)는 ping/ack 메시지에 piggyback되어 gossip 방식으로 전파된다.

이를 통해 메시지 복잡도가 O(N)으로 줄어들면서도, 장애 감지의 완전성(completeness)과 정확도가 확률적으로 보장된다. 실험 결과 2,000개 이상의 노드 클러스터에서도 효과적으로 동작함을 보였다.

**Dynamo에서의 구체적 활용**: Dynamo의 gossip 기반 멤버십 프로토콜은 SWIM의 핵심 아이디어를 채택했다. 특히 ping/ack 메시지에 멤버십 정보를 piggyback하는 기법과, 간접 프로빙을 통한 false positive 감소 기법이 Dynamo의 장애 감지 메커니즘에 반영되었다.

**해결한 문제**: 수백~수천 대의 노드로 구성된 Dynamo 클러스터에서 확장 가능한 장애 감지와 멤버십 관리 메커니즘이 필요했다.

### 3.3 The Phi Accrual Failure Detector

- **저자**: Naohiro Hayashibara, Xavier Defago, Rami Yared, Takuya Katayama
- **학회**: *Proceedings of the 23rd IEEE International Symposium on Reliable Distributed Systems (SRDS 2004)*, pp. 66-78, October 2004
- **DOI**: 10.1109/RELDIS.2004.1353004

**핵심 기여 (상세)**:

전통적 장애 감지기는 이진 출력(장애/정상)을 생성하는데, 이는 네트워크 지연 변동이 큰 환경에서 적절한 타임아웃 설정이 어렵다는 문제가 있다. Hayashibara 등은 이를 해결하기 위해 **accrual(누적) 장애 감지기**라는 새로운 추상화를 제안했다.

Phi accrual failure detector는 이진 판단 대신 연속적인 의심 수준(suspicion level) phi를 출력한다. phi 값이 높을수록 해당 노드가 장애 상태일 확률이 높다. 이 값은 과거 heartbeat 도착 간격의 통계적 분석(정규 분포 가정)을 기반으로 동적으로 계산된다. 응용 프로그램은 phi의 임계값을 자유롭게 설정하여 false positive와 감지 시간 사이의 트레이드오프를 제어할 수 있다.

핵심 수식: phi(t) = -log10(P_later(t - T_last))로, T_last는 마지막 heartbeat 수신 시각이고, P_later(t)는 과거 heartbeat 간격 분포에 기반한 다음 heartbeat가 t 이후에 도착할 확률이다. 이를 통해 네트워크 조건에 자동으로 적응하는 장애 감지가 가능하다.

**Dynamo에서의 영향**: Dynamo는 로컬 gossip 프로토콜에서 일시적 장애와 영구적 장애를 구분해야 하는데, phi accrual failure detector의 연속적 의심 수준 개념이 이를 지원한다. Cassandra(Dynamo의 후속 시스템)는 phi accrual failure detector를 직접 구현했다.

### 3.4 Unreliable Failure Detectors for Reliable Distributed Systems

- **저자**: Tushar Deepak Chandra, Sam Toueg
- **학회**: *Journal of the ACM (JACM)*, Vol. 43, No. 2, pp. 225-267, March 1996
- **DOI**: 10.1145/226643.226647

**핵심 기여 (상세)**:

Chandra와 Toueg의 이 기념비적 논문은 비동기 분산 시스템에서의 장애 감지기를 형식적으로 정의하고, "불완전한(unreliable)" 장애 감지기로도 합의(consensus) 문제를 해결할 수 있음을 증명했다. FLP 불가능성 결과(Fischer, Lynch, Paterson, 1985)가 비동기 시스템에서의 합의 불가능성을 보인 후, 이 논문은 약간의 추가 가정(장애 감지기)으로 합의가 가능해짐을 보였다.

장애 감지기를 두 가지 속성으로 특성화했다: **완전성(Completeness)** -- 모든 장애 노드가 결국 감지됨, **정확도(Accuracy)** -- 정상 노드가 잘못 의심되지 않음. 이 두 속성의 조합으로 8개 클래스의 장애 감지기를 정의했으며, 가장 약한 장애 감지기 클래스(Eventually Weak, 기호 <>W)로도 합의가 가능함을 증명했다.

**Dynamo에서의 영향**: Dynamo의 장애 감지 메커니즘은 완전한 정확도를 보장하지 않는 "불완전한" 장애 감지기를 전제로 설계되었다. Chandra-Toueg의 이론적 프레임워크는 이러한 설계가 올바른 것임을 정당화한다.

---

## 4. Distributed Hash Tables (DHTs) -- 구조적 오버레이 네트워크

### 4.1 Chord: A Scalable Peer-to-Peer Lookup Service for Internet Applications

- **저자**: Ion Stoica, Robert Morris, David Karger, M. Frans Kaashoek, Hari Balakrishnan
- **학회**: **SIGCOMM 2001** (ACM SIGCOMM Conference), San Diego, CA, August 2001, pp. 149-160
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)
- **DOI**: 10.1145/383059.383071
- 확장판: **IEEE/ACM Transactions on Networking**, Vol. 11, No. 1, pp. 17-32, February 2003. DOI: 10.1109/TNET.2002.808407
- **2011년 ACM SIGCOMM Test of Time Award 수상**

**핵심 기여 (상세)**:

Chord는 분산 해시 테이블(DHT)의 가장 영향력 있는 설계 중 하나로, P2P 네트워크에서 효율적인 키 탐색 서비스를 제공한다. Chord는 consistent hashing을 기반으로 키와 노드를 m비트 식별자 공간의 원(ring) 위에 배치하고, 각 키를 시계 방향으로 가장 가까운 노드(successor)에 할당한다.

Chord의 핵심 혁신은 **finger table**이다. 각 노드 n은 m개의 엔트리로 구성된 finger table을 유지하며, i번째 엔트리는 (n + 2^i) mod 2^m 위치의 successor를 가리킨다. 이 지수적(exponential) 간격의 포인터 구조를 통해, N개의 노드가 있는 네트워크에서 O(log N) 홉으로 임의의 키를 찾을 수 있다. 또한 각 노드는 O(log N)의 상태만 유지하면 된다.

Chord는 노드의 동적 참여/이탈을 처리하기 위한 안정화(stabilization) 프로토콜도 제시했다. 새 노드가 참여할 때 자신의 successor만 알면 되고, 주기적 안정화 프로토콜이 finger table을 점진적으로 갱신한다. 노드 장애 시에는 successor list를 유지하여 robustness를 보장한다.

**Dynamo에서의 구체적 활용**: Dynamo의 consistent hash ring 기반 데이터 분산 구조는 Chord의 설계에서 직접적 영향을 받았다. 특히 키 공간을 원으로 매핑하고 successor 노드에 데이터를 할당하는 기본 구조와, preference list를 통한 장애 허용 메커니즘이 Chord의 successor list와 유사하다. 그러나 Dynamo는 Chord의 O(log N) 라우팅 대신, 모든 노드가 전체 멤버십 정보를 가지는 zero-hop DHT 방식을 채택했다(라우팅 홉 없이 직접 통신).

**해결한 문제**: 대규모 분산 시스템에서 키를 효율적으로 담당 노드에 매핑하는 확장 가능한 메커니즘이 필요했다.

### 4.2 Pastry: Scalable, Decentralized Object Location and Routing for Large-Scale Peer-to-Peer Systems

- **저자**: Antony Rowstron, Peter Druschel
- **학회**: *IFIP/ACM International Conference on Distributed Systems Platforms (Middleware 2001)*, Heidelberg, Germany, November 2001, pp. 329-350
- **DOI**: 10.1007/3-540-45518-3_18

**핵심 기여 (상세)**:

Pastry는 Microsoft Research와 Rice University에서 개발된 P2P 오버레이 네트워크로, 대규모 인터넷 환경에서의 확장 가능한 객체 위치 지정과 라우팅을 제공한다. Pastry의 각 노드에는 128비트 무작위 식별자(nodeId)가 할당되며, 키와 가장 수치적으로 가까운 nodeId를 가진 노드로 메시지가 라우팅된다.

Pastry의 라우팅 구조는 세 가지 요소로 구성된다: (1) **라우팅 테이블** -- nodeId의 각 접두사(prefix) 수준에서 해당 수준의 접두사를 공유하면서 다음 자릿수가 다른 노드들에 대한 포인터를 유지(O(log_{2^b} N) 행, 2^b - 1 열). (2) **Leaf Set** -- 현재 노드와 수치적으로 가장 가까운 |L|개의 노드(|L|/2은 더 크고, |L|/2은 더 작은). (3) **Neighborhood Set** -- 네트워크 거리(proximity) 기준으로 가장 가까운 M개의 노드.

Pastry의 가장 중요한 특성은 **네트워크 지역성(network locality)**을 라우팅에 반영한다는 것이다. 라우팅 테이블 구축 시 네트워크적으로 가까운 노드를 우선 선택하여, 논리적 라우팅 경로가 물리적으로도 효율적이게 한다. 100,000개 노드 시뮬레이션에서 확장성과 자기 구성 능력이 검증되었다.

**Dynamo에서의 영향**: Pastry의 leaf set 개념은 Dynamo의 preference list와 유사하다. 또한 Pastry의 네트워크 지역성 최적화 아이디어는 Dynamo가 지리적으로 분산된 데이터센터에서의 복제 전략을 설계할 때 참고되었다.

### 4.3 A Scalable Content-Addressable Network (CAN)

- **저자**: Sylvia Ratnasamy, Paul Francis, Mark Handley, Richard Karp, Scott Shenker
- **학회**: **SIGCOMM 2001** (ACM SIGCOMM Conference), San Diego, CA, August 2001, pp. 161-172
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)
- **DOI**: 10.1145/383059.383072

**핵심 기여 (상세)**:

CAN은 d차원 토러스(torus)를 오버레이 네트워크로 사용하는 DHT이다. d차원 좌표 공간을 노드들이 분할하여 소유하며, 각 키-값 쌍은 키의 해시값이 가리키는 좌표 영역을 소유한 노드에 저장된다. 라우팅은 욕심쟁이(greedy) 알고리즘으로, 각 노드가 d차원 공간에서 목적지에 가장 가까운 이웃으로 메시지를 전달한다.

CAN의 고유한 특성은 라우팅 테이블 크기가 O(d)로 노드 수 N과 무관하다는 것이다. 반면 라우팅 경로 길이는 O(d/4 * N^(1/d))로, d를 적절히 선택하면 O(N^(1/d))의 라우팅 경로를 얻을 수 있다. d = log N으로 설정하면 O(log N) 라우팅이 되지만, 라우팅 테이블 크기도 O(log N)이 된다.

CAN은 노드 장애 시 이웃 노드가 해당 영역을 인수하는 간단한 복구 메커니즘과, 여러 복제본을 다른 좌표 공간에 배치하여 가용성을 높이는 기법도 제시했다.

**Dynamo에서의 영향**: CAN의 분산 데이터 저장 모델과 이웃 노드 간 장애 복구 메커니즘이 Dynamo의 설계에 참고되었다. 특히 Dynamo의 hinted handoff에서 이웃 노드가 임시로 데이터를 인수하는 개념은 CAN의 영역 인수와 유사하다.

### 4.4 Kademlia: A Peer-to-Peer Information System Based on the XOR Metric

- **저자**: Petar Maymounkov, David Mazieres
- **학회**: *IPTPS 2002* (1st International Workshop on Peer-to-Peer Systems), LNCS 2429, pp. 53-65, March 2002
- **DOI**: 10.1007/3-540-45748-8_5

**핵심 기여 (상세)**:

Kademlia는 XOR 거리 메트릭이라는 혁신적 개념을 DHT에 도입했다. 두 노드 a, b의 거리를 d(a,b) = a XOR b로 정의하며, 이 메트릭은 삼각 부등식을 만족하고 대칭적(d(a,b) = d(b,a))이라는 독특한 성질을 가진다.

각 노드는 자신과의 XOR 거리가 [2^i, 2^(i+1)) 범위에 있는 노드들의 리스트(k-bucket)를 유지한다. 총 160개의 k-bucket이 있으며(160비트 키 공간 가정), 각 bucket에는 최대 k개의 엔트리가 저장된다. 라우팅 시 목적지 키와의 XOR 거리를 절반 이상 줄이는 노드를 선택하여 전달하며, O(log N) 홉으로 라우팅이 완료된다.

Kademlia의 핵심 장점은 **대칭적 라우팅**이다. 노드 A가 B를 알고 있으면 B도 A를 알 확률이 높아, 라우팅 테이블 유지 비용이 줄어든다. 또한 k-bucket의 LRU(Least Recently Used) 정책으로 오래 살아있는 노드를 우선하여, churn이 높은 환경에서도 안정적으로 동작한다. BitTorrent, Ethereum 등에서 실제 사용되고 있다.

**Dynamo에서의 영향**: Kademlia의 k-bucket 기반 근접 노드 관리와, churn 환경에서의 안정적 동작 전략이 Dynamo의 설계에 참고되었다.

### 4.5 Tapestry: An Infrastructure for Fault-tolerant Wide-area Location and Routing

- **저자**: Ben Y. Zhao, John Kubiatowicz, Anthony D. Joseph
- **학회**: UC Berkeley Technical Report UCB/CSD-01-1141, April 2001; 이후 *IEEE Journal on Selected Areas in Communications (JSAC)*, 2004
- **DOI**: 10.1109/JSAC.2003.818784 (JSAC 버전)

**핵심 기여 (상세)**:

Tapestry는 Plaxton, Rajaraman, Richa의 분산 검색 기법을 확장하여 실제 인터넷 규모의 위치 지정과 라우팅 인프라를 구축한 것이다. 포인트-투-포인트 링크만을 사용하여 중앙화된 자원 없이 객체나 서비스의 가장 가까운 복제본으로 메시지를 직접 라우팅한다.

Tapestry의 라우팅은 접두사 매칭(prefix matching) 기반으로, 각 홉에서 목적지 ID와 한 자릿수씩 더 많이 일치하는 노드로 메시지를 전달한다. 라우팅 테이블의 각 수준에서 네트워크적으로 가까운 노드를 우선 선택하여 네트워크 지역성을 최적화한다. 또한 모든 라우팅과 디렉토리 정보가 **소프트 스테이트(soft state)**로 관리되어 장애 시 쉽게 복구된다.

Tapestry는 자기 조직적(self-organizing)이고, 확장 가능하며, 장애와 부하 변동에 강인하다. OceanStore(영구적 글로벌 규모 스토리지 시스템)의 하부 구조로 활용되었다.

**Dynamo에서의 영향**: Tapestry의 soft state 기반 메타데이터 관리와 네트워크 지역성 최적화 개념이 Dynamo의 설계에 참고되었다.

### 4.6 Accessing Nearby Copies of Replicated Objects in a Distributed Environment

- **저자**: C. Greg Plaxton, Rajmohan Rajaraman, Andrea W. Richa
- **학회**: **SPAA 1997** (ACM Symposium on Parallelism in Algorithms and Architectures), pp. 311-320, June 1997; 이후 *Theory of Computing*, Vol. 1, pp. 17-48, 2005
- **DOI**: 10.1145/258492.258523

**핵심 기여 (상세)**:

Plaxton, Rajaraman, Richa는 분산 네트워크에서 복제된 객체의 가장 가까운 복사본에 효율적으로 접근하는 랜덤화 알고리즘을 설계했다. 이 논문에서 제안된 "Plaxton mesh"는 Tapestry와 Pastry의 직접적 기반이 되었다.

알고리즘의 핵심은 객체를 위한 랜덤 트리(random tree)를 구성하고, 임의의 리프에서 루트 방향으로 메시지를 전달하되, 경로상에서 객체의 복제본을 만나면 즉시 반환하는 것이다. 이를 통해 각 접근 요청이 가까운 복제본에 의해 처리되는 것이 확률적으로 보장된다.

**Dynamo에서의 영향**: Plaxton mesh의 기본 구조는 Pastry, Tapestry를 통해 간접적으로 Dynamo의 설계에 영향을 미쳤다. 특히 복제된 데이터의 가까운 복사본에 접근하는 개념은 Dynamo의 지역성 기반 라우팅 전략과 관련된다.

---

## 5. CAP Theorem -- 분산 시스템의 근본적 트레이드오프

### 5.1 Harvest, Yield, and Scalable Tolerant Systems

- **저자**: Armando Fox, Eric A. Brewer
- **학회**: *HotOS-VII 1999* (7th Workshop on Hot Topics in Operating Systems), pp. 174-178, March 1999
- **DOI**: 10.1109/HOTOS.1999.798396

**핵심 기여 (상세)**:

Fox와 Brewer는 대규모 인터넷 서비스의 가용성을 **harvest**와 **yield**라는 두 가지 메트릭으로 재정의할 것을 제안했다. **Yield**는 성공적으로 응답된 요청의 비율(전통적 가용성과 유사하지만 미묘하게 다름)이고, **Harvest**는 응답에 실제로 포함된 데이터의 완전성 비율이다.

이 프레임워크의 혁신은 가용성을 이진적(가용/불가용)이 아닌 연속적 스펙트럼으로 보는 것이다. 시스템 장애 시 yield를 약간 낮추더라도 harvest를 유지하거나, harvest를 줄이더라도 yield를 유지하는 "우아한 성능 저하(graceful degradation)" 전략을 제안했다. 예를 들어 검색 엔진은 인덱스 서버 일부가 장애이면 불완전한 결과(낮은 harvest)를 반환하되, 응답 자체는 항상 제공(높은 yield)할 수 있다.

또한 논문은 대규모 시스템에서 장애를 격리하기 위한 두 가지 메커니즘을 제안했다: (1) 장애 도메인의 확률적 격리, (2) 복제와 파티셔닝을 통한 격리. 이러한 아이디어는 Brewer의 CAP 추측(conjecture)의 직접적 전조가 되었다.

**Dynamo에서의 구체적 활용**: Dynamo의 "always writable" 정책은 harvest/yield 프레임워크의 직접적 적용이다. 네트워크 파티션 시 일부 데이터의 일관성(harvest)을 희생하더라도, 쓰기 요청은 항상 처리(높은 yield)하는 전략을 취한다. 이는 Amazon의 "장바구니는 항상 쓸 수 있어야 한다"는 비즈니스 요구사항을 정확히 반영한다.

### 5.2 Towards Robust Distributed Systems (CAP Keynote)

- **저자**: Eric A. Brewer
- **학회**: **PODC 2000** (ACM Symposium on Principles of Distributed Computing), Keynote Address, Portland, Oregon, July 19, 2000
- **BK21 등급**: 인정 IF **3** (Top-tier 우수)
- 슬라이드: https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf

**핵심 기여 (상세)**:

이 기조 연설에서 Brewer는 분산 시스템의 세 가지 바람직한 속성 -- **Consistency(일관성)**, **Availability(가용성)**, **Partition tolerance(분할 내성)** -- 을 동시에 만족하는 것이 불가능하다는 추측(conjecture)을 공식적으로 발표했다. 이것이 후에 "CAP theorem"으로 알려지게 된다.

Brewer의 논지는 Inktomi(당시 웹 검색의 50%를 처리)에서의 실무 경험에 기반한 것이었다. 핵심 관찰: (1) 네트워크 파티션은 실제로 빈번하게 발생한다, (2) 원격 호출은 로컬 호출과 근본적으로 다르다, (3) 장애는 상시적이다. 따라서 강건한 분산 시스템은 이상적 추상화가 아닌 이러한 현실에서 출발해야 한다.

Brewer는 ACID(데이터베이스) vs BASE(Basically Available, Soft state, Eventually consistent) 프레임워크를 제시하여, 전통적 데이터베이스 시스템과 대규모 분산 시스템의 설계 철학 차이를 명확히 했다. 이 연설은 CAP이 엄밀한 수학적 정리가 아닌 실무적 가이드라인으로 제안되었다는 점에서 중요하다.

**Dynamo에서의 구체적 활용**: Dynamo는 Brewer의 CAP 추측에 기반하여, 의도적으로 **AP(Availability + Partition Tolerance)** 설계를 선택하고 Consistency를 eventually consistent로 완화했다. 이는 Dynamo 논문 전체를 관통하는 핵심 설계 원칙이다.

**해결한 문제**: 대규모 분산 시스템에서의 근본적 트레이드오프를 명확히 인식하고, 비즈니스 요구에 맞는 설계 선택을 할 수 있는 프레임워크를 제공했다.

### 5.3 Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services

- **저자**: Seth Gilbert, Nancy Lynch
- **학회**: *ACM SIGACT News*, Vol. 33, No. 2, pp. 51-59, June 2002
- **DOI**: 10.1145/564585.564601

**핵심 기여 (상세)**:

Gilbert와 Lynch(MIT)는 Brewer의 추측을 형식적으로 증명하여 정리(theorem)로 격상시켰다. 비동기 네트워크 모델(asynchronous network model)에서 consistency, availability, partition tolerance를 엄밀하게 정의하고, 이 세 가지를 동시에 만족하는 것이 불가능함을 증명했다.

구체적으로: **Consistency**는 모든 노드가 동일한 시점에 동일한 데이터를 반환하는 것(linearizability와 동등), **Availability**는 장애가 아닌 모든 노드의 모든 요청이 유한 시간 내에 응답을 받는 것, **Partition Tolerance**는 네트워크 파티션(임의의 메시지 손실)이 발생해도 시스템이 올바르게 동작하는 것이다.

증명의 핵심 아이디어는 간단하다: 파티션이 발생하여 두 그룹의 노드가 통신할 수 없을 때, 한쪽 그룹에서 쓰기가 발생하면 다른 쪽 그룹의 노드는 이 쓰기를 반영할 수 없다. 따라서 다른 쪽에서의 읽기는 최신 값을 반환할 수 없거나(consistency 포기), 응답하지 않거나(availability 포기) 해야 한다.

논문은 또한 부분 동기(partially synchronous) 모델에서는 세 속성 모두를 어느 정도 만족하는 해법이 존재할 수 있음을 논의한다.

**Dynamo에서의 구체적 활용**: 이 형식적 증명은 Dynamo의 설계 선택(AP)에 대한 이론적 정당성을 제공한다. 네트워크 파티션이 발생할 수 있는 Amazon의 대규모 인프라에서 가용성을 우선하는 것이 불가피한 선택임을 증명한다.

### 5.4 CAP Twelve Years Later: How the "Rules" Have Changed

- **저자**: Eric A. Brewer
- **학회**: *IEEE Computer*, Vol. 45, No. 2, pp. 23-29, February 2012
- **DOI**: 10.1109/MC.2012.37

**핵심 기여 (상세)**:

Brewer 자신이 CAP theorem 발표 12년 후에 쓴 회고적 논문으로, 널리 퍼진 오해를 바로잡고 실용적 가이드라인을 제시한다. 핵심 메시지들:

첫째, CAP가 "셋 중 둘을 선택"이라는 일반적 해석은 오해를 유발한다. 파티션은 상시적이 아니라 간헐적으로 발생하므로, 정상 상태에서는 세 가지 모두 충족할 수 있다. 파티션 발생 시에만 C와 A 사이의 선택이 강제된다.

둘째, 파티션을 명시적으로 처리(detect - enter partition mode - initiate partition recovery)하면 C와 A를 상황에 따라 유연하게 조율할 수 있다. 예를 들어 특정 연산은 일관성을 우선하고, 다른 연산은 가용성을 우선할 수 있다.

셋째, CAP의 "consistency"와 ACID의 "consistency"는 다른 개념이다. CAP의 C는 linearizability를, ACID의 C는 응용 수준의 불변량(invariant) 보존을 의미한다.

**Dynamo에서의 영향**: Dynamo의 설계가 발표된 후, CAP에 대한 더 세밀한 이해가 발전했다. Brewer의 회고는 Dynamo 류의 시스템이 파티션 발생 시에만 일관성을 완화하는 것이 합리적이며, 평소에는 높은 일관성을 유지할 수 있음을 명확히 했다.

### 5.5 Consistency Tradeoffs in Modern Distributed Database System Design: CAP is Only Part of the Story

- **저자**: Daniel J. Abadi
- **학회**: *IEEE Computer*, Vol. 45, No. 2, pp. 37-42, February 2012
- **DOI**: 10.1109/MC.2012.33

**핵심 기여 (상세)**:

Abadi는 CAP theorem이 분산 데이터베이스 시스템의 실제 설계에 미치는 영향이 흔히 인식되는 것보다 제한적이라고 주장하며, **PACELC** 프레임워크를 제안했다. PACELC: **P**artition이 발생하면 **A**vailability와 **C**onsistency 사이에서 선택해야 하고, **E**lse(정상 상태에서도) **L**atency와 **C**onsistency 사이에서 선택해야 한다.

이 확장의 핵심 통찰은 파티션이 없는 정상 상태에서도 일관성과 지연시간 사이의 트레이드오프가 존재한다는 것이다. 예를 들어 강한 일관성(linearizability)을 보장하려면 복제본 간 동기화가 필요하여 지연시간이 증가한다. 반면 약한 일관성을 허용하면 로컬 복제본에서 즉시 응답하여 낮은 지연시간을 달성할 수 있다.

Abadi는 실제 시스템들을 PACELC으로 분류했다: Dynamo/Cassandra/Riak = PA/EL(파티션 시 가용성, 평소에도 저지연 선호), HBase/MongoDB = PC/EC(파티션 시 일관성, 평소에도 일관성 선호), PNUTS = PC/EL(파티션 시 일관성, 평소에는 저지연 선호).

**Dynamo에서의 영향**: PACELC는 Dynamo의 설계 선택을 더 정확하게 설명한다. Dynamo는 PA/EL 시스템으로, 파티션 발생 시 가용성을 우선할 뿐 아니라, 정상 상태에서도 eventual consistency를 허용하여 낮은 지연시간을 달성한다. 이는 CAP만으로는 설명할 수 없는 Dynamo의 설계 동기를 포착한다.

### 5.6 Perspectives on the CAP Theorem

- **저자**: Seth Gilbert, Nancy Lynch
- **학회**: *IEEE Computer*, Vol. 45, No. 2, pp. 30-36, February 2012
- **DOI**: 10.1109/MC.2012.55

**핵심 기여**: CAP theorem의 원저자들이 12년 후 다양한 관점에서 정리를 재검토한다. CAP의 다양한 정의(공식적/비공식적), 일관성 모델의 스펙트럼(linearizability, sequential consistency, eventual consistency 등), 그리고 실제 시스템에서의 적용 방법을 논의한다.

---

## 6. Quorum Systems -- 읽기/쓰기 일관성 제어

### 6.1 Weighted Voting for Replicated Data

- **저자**: David K. Gifford
- **학회**: **SOSP 1979** (7th ACM Symposium on Operating Systems Principles), pp. 150-162, December 1979
- **BK21 등급**: 인정 IF **4** (Top-tier 최우수)
- **DOI**: 10.1145/800215.806583

**핵심 기여 (상세)**:

Gifford의 이 선구적 논문은 복제된 데이터의 일관성을 보장하기 위한 **가중 투표(weighted voting)** 메커니즘을 제안했다. 각 데이터 복제본에 임의의 투표 수(vote)를 할당하고, 읽기 쿼럼(read quorum, r)과 쓰기 쿼럼(write quorum, w)을 정의하여 다음 조건을 만족하도록 한다:

- **r + w > V** (V는 전체 투표 수): 읽기 쿼럼과 쓰기 쿼럼이 반드시 겹치므로, 읽기는 항상 최신 쓰기 결과를 포함하는 복제본에 접근
- **w > V/2**: 두 개의 쓰기 쿼럼이 반드시 겹치므로, 쓰기 순서가 직렬화 가능

이 프레임워크의 유연성은 r과 w의 비율을 조절하여 읽기 중심 또는 쓰기 중심 워크로드에 최적화할 수 있다는 점이다. 예를 들어 r=1, w=V이면 읽기가 매우 빠르지만 쓰기에 모든 복제본의 참여가 필요하다. 반대로 r=V, w=1이면 쓰기가 빠르지만 읽기에 모든 복제본 접근이 필요하다.

또한 각 복제본에 서로 다른 투표 수를 할당할 수 있어, 네트워크 위상이나 하드웨어 성능에 따른 유연한 복제 전략이 가능하다. 이는 균일하지 않은 환경에서의 복제 관리에 중요한 기여이다.

**Dynamo에서의 구체적 활용**: Dynamo는 N=3, R=2, W=2를 기본 쿼럼으로 사용하되, 응용에 따라 R과 W를 조절할 수 있는 **tunable quorum** 메커니즘을 제공한다. R=1으로 설정하면 빠른 읽기, W=1로 설정하면 빠른 쓰기가 가능하다. Dynamo는 여기에 **sloppy quorum**을 도입하여 Gifford의 엄격한 쿼럼을 완화한다.

**해결한 문제**: 복제된 데이터의 일관성과 가용성 사이의 트레이드오프를 유연하게 제어할 수 있는 메커니즘이 필요했다.

### 6.2 A Majority Consensus Approach to Concurrency Control for Multiple Copy Databases

- **저자**: Robert H. Thomas
- **학회**: *ACM Transactions on Database Systems (TODS)*, Vol. 4, No. 2, pp. 180-209, June 1979
- **DOI**: 10.1145/320071.320076

**핵심 기여 (상세)**:

Thomas는 분산 데이터베이스에서의 동시성 제어를 위한 **과반수 합의(majority consensus)** 알고리즘을 제안했다. 이 알고리즘은 분산 제어를 기반으로 하며, 통신 장애와 데이터베이스 사이트 장애 상황에서도 올바르게 동작한다.

핵심 아이디어는 각 업데이트 트랜잭션이 전체 복제본의 과반수 이상에서 승인을 받아야 커밋되는 것이다. 이를 통해 임의의 두 트랜잭션이 승인된 복제본 집합이 반드시 겹치게 되어, 충돌하는 업데이트가 일관되게 직렬화된다. Thomas의 알고리즘은 타임스탬프 기반 충돌 해결과 결합하여, 교착상태(deadlock) 없이 동시성을 제어한다.

이 논문은 또한 "Thomas's write rule"이라고 알려진 최적화를 포함한다: 더 최신의 타임스탬프를 가진 쓰기가 이미 존재하면, 이전 타임스탬프의 쓰기를 무시할 수 있다는 규칙이다. 이는 불필요한 충돌 해결을 줄인다.

**Dynamo에서의 영향**: Thomas의 과반수 합의 개념은 Dynamo의 쿼럼 기반 읽기/쓰기의 이론적 근거 중 하나이다. R + W > N 조건은 과반수 합의의 일반화된 형태이다.

### 6.3 A Quorum-Consensus Replication Method for Abstract Data Types

- **저자**: Maurice P. Herlihy
- **학회**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 4, No. 1, pp. 32-53, February 1986
- **DOI**: 10.1145/6306.6308

**핵심 기여 (상세)**:

Herlihy는 Gifford의 가중 투표를 추상 데이터 타입(abstract data type)으로 확장했다. Gifford의 방식이 해석되지 않은 파일(uninterpreted file)만을 지원하는 반면, Herlihy의 방식은 집합(set), 큐(queue), 디렉토리(directory) 등의 타입별 특성을 체계적으로 활용하여 더 효과적인 복제를 가능하게 한다.

핵심 혁신은 데이터 타입의 대수적 구조(algebraic structure)를 분석하여, 쿼럼 교차(quorum intersection)에 대한 필요충분 조건을 도출하는 것이다. 예를 들어 읽기 전용 연산은 쓰기 연산과의 교차만 보장하면 되고, 교환 가능한(commutative) 연산은 교차 조건이 완화될 수 있다.

이를 통해 연산의 의미론(semantics)에 따라 쿼럼 크기를 줄일 수 있어, 성능과 가용성이 향상된다. 논문은 또한 복제 방법의 정확성에 대한 형식적 증명을 제공한다.

**Dynamo에서의 영향**: Herlihy의 연구는 키-값 저장소의 특성(get/put 연산의 단순한 의미론)을 쿼럼 설계에 활용할 수 있는 이론적 근거를 제공했다. Dynamo의 단순한 API(get/put)는 복잡한 쿼럼 교차 조건 없이도 효과적으로 동작할 수 있다.

### 6.4 Probabilistic Quorum Systems

- **저자**: Dahlia Malkhi, Michael Reiter, Rebecca Wright
- **학회**: **PODC 1997** (16th ACM Symposium on Principles of Distributed Computing), pp. 267-273, 1997; 이후 *Information and Computation*, Vol. 170, No. 2, pp. 184-206, 2001
- **DOI**: 10.1145/259380.259458

**핵심 기여 (상세)**:

Malkhi, Reiter, Wright는 전통적 쿼럼 시스템의 교차 속성을 **높은 확률로만** 보장하면 어떤 결과가 나타나는지를 탐구했다. 전통적 쿼럼은 모든 읽기 쿼럼과 모든 쓰기 쿼럼이 반드시 교차해야 하므로, N개의 서버에서 쿼럼 크기가 최소 O(sqrt(N))이어야 한다.

확률적 쿼럼에서는 교차를 높은 확률(예: 1 - 1/N)로만 보장하여, 쿼럼 크기를 O(sqrt(N log N))까지 줄일 수 있다. 이는 성능과 가용성의 극적인 개선을 가져온다. 또한 비잔틴(Byzantine) 장애가 있는 환경으로 확장한 "Byzantine quorum system"도 제시했다.

**Dynamo에서의 영향**: Dynamo의 sloppy quorum은 확률적 쿼럼의 실용적 변형으로 볼 수 있다. 엄격한 쿼럼 교차를 보장하지 않지만, 높은 확률로 일관성을 유지하면서 가용성을 극대화한다.

### 6.5 Byzantine Quorum Systems

- **저자**: Dahlia Malkhi, Michael Reiter
- **학회**: **STOC 1997** (29th ACM Symposium on Theory of Computing), pp. 569-578; 이후 *Distributed Computing*, Vol. 11, No. 4, pp. 203-213, 1998
- **DOI**: 10.1007/s004460050050

**핵심 기여**: 임의의(비잔틴) 장애를 허용하면서도 데이터의 가용성과 일관성을 보장하는 쿼럼 시스템을 최초로 연구했다. N개의 서버에서 최대 f개의 비잔틴 장애를 허용하면서 O(1/sqrt(N)) 부하를 달성하는 구성을 제시했다.

**Dynamo에서의 영향**: Dynamo는 비잔틴 장애를 가정하지 않지만, Malkhi-Reiter의 쿼럼 이론은 Dynamo의 쿼럼 설계의 이론적 배경을 넓혔다.

---

## 7. Merkle Trees -- 효율적 데이터 동기화

### 7.1 A Digital Signature Based on a Conventional Encryption Function

- **저자**: Ralph C. Merkle
- **학회**: *Advances in Cryptology -- CRYPTO '87*, LNCS 293, pp. 369-378, August 1987
- **DOI**: 10.1007/3-540-48184-2_32

**핵심 기여 (상세)**:

Merkle는 기존의 암호화 함수(DES 등)만을 이용한 디지털 서명 체계를 제안했으며, 이 과정에서 **Merkle tree**(해시 트리)라는 데이터 구조를 도입했다. Merkle tree에서 각 리프 노드는 데이터 블록의 해시값이고, 비-리프 노드는 자식 노드들의 해시값의 해시이다. 최종적으로 단일 루트 해시(root hash)가 전체 데이터의 요약을 나타낸다.

이 구조의 핵심 속성은 **효율적 검증**이다. 두 대규모 데이터 집합이 동일한지 확인하려면 루트 해시만 비교하면 된다. 불일치가 있으면 트리를 재귀적으로 내려가며 차이가 있는 정확한 데이터 블록을 O(log N)의 비교로 식별할 수 있다. 이는 N개의 블록을 하나하나 비교하는 O(N) 방식에 비해 극적으로 효율적이다.

서명 시스템에서 Merkle tree는 무한한 수의 메시지에 서명할 수 있게 하며, 서명 크기는 서명된 메시지 수의 로그에 비례하여 증가한다. 보안은 기저 암호화 함수의 충돌 저항성(collision resistance)에만 의존한다.

**Dynamo에서의 구체적 활용**: Dynamo는 복제본 간 **anti-entropy** 프로세스에서 Merkle tree를 핵심적으로 활용한다. 각 노드는 자신이 담당하는 키 범위에 대해 Merkle tree를 유지한다. 동기화 시 두 노드는 Merkle tree의 루트 해시를 비교하고, 불일치하면 자식 노드의 해시를 비교하는 방식으로 재귀적으로 내려가, 실제로 다른 키-값 쌍만을 식별하여 동기화한다. 이를 통해 대규모 데이터셋에서도 최소한의 데이터 전송으로 복제본 동기화가 가능하다.

**해결한 문제**: 수백만 개의 키-값 쌍을 가진 복제본 간 동기화에서, 전체 데이터를 비교하지 않고 차이점만 효율적으로 식별하는 방법이 필요했다.

### 7.2 Secrecy, Authentication, and Public Key Systems (Merkle's Thesis)

- **저자**: Ralph C. Merkle
- **학회**: Stanford University Ph.D. Thesis, 1979

**핵심 기여**: Merkle의 박사 논문에서 해시 트리 개념이 최초로 도입되었다. CRYPTO '87 논문이 더 널리 인용되지만, 원래 아이디어는 1979년 논문에서 시작되었다. 이 논문은 공개키 암호 시스템의 기초를 놓은 연구이기도 하다.

---

## 8. Replication Strategies -- 복제 전략

### 8.1 The Dangers of Replication and a Solution

- **저자**: Jim Gray, Pat Helland, Patrick O'Neil, Dennis Shasha
- **학회**: **SIGMOD 1996** (ACM SIGMOD International Conference on Management of Data), pp. 173-182, Montreal, June 1996
- **DOI**: 10.1145/233269.233330

**핵심 기여 (상세)**:

Gray 등은 "update anywhere-anytime-anyway" 트랜잭션 복제가 워크로드 증가에 따라 **불안정한 동작**을 보인다는 것을 형식적으로 분석했다. 핵심 발견: 노드 수와 트래픽이 10배 증가하면, 교착상태(deadlock)나 재조정(reconciliation) 횟수가 **1000배** 증가한다. 이는 eager replication(동기적 복제)이 대규모 시스템에서 비실용적임을 의미한다.

논문은 **primary copy(마스터 복제본)** 방식이 이 문제를 완화하지만, 마스터가 장애이면 쓰기가 불가능하다는 한계가 있음을 지적한다. 이에 대한 대안으로 **2단계 복제 알고리즘(two-tier replication)**을 제안했다: 모바일/분리된 응용이 잠정적(tentative) 업데이트 트랜잭션을 제안하고, 이것이 나중에 마스터 복제본에 적용되는 방식이다.

이 논문의 "잠정적 업데이트가 나중에 확정되는" 모델은 Dynamo의 eventually consistent 모델과 직접적으로 관련된다.

**Dynamo에서의 구체적 활용**: Dynamo의 설계는 Gray 등이 경고한 eager replication의 확장성 문제를 정면으로 회피한다. Dynamo는 optimistic replication(낙관적 복제)을 채택하여 쓰기를 비동기적으로 전파하고, 충돌이 발생하면 사후에 해결한다. 이는 Gray 등의 분석이 제시한 "eager replication의 위험성"에 대한 실용적 해답이다.

**해결한 문제**: Amazon의 글로벌 규모 시스템에서 동기적 복제의 확장성 한계를 극복하는 복제 전략이 필요했다.

### 8.2 Chain Replication for Supporting High Throughput and Availability

- **저자**: Robbert van Renesse, Fred B. Schneider
- **학회**: **OSDI 2004** (6th Symposium on Operating Systems Design and Implementation), pp. 91-104, December 2004
- **USENIX**: https://www.usenix.org/conference/osdi-04/chain-replication-supporting-high-throughput-and-availability

**핵심 기여 (상세)**:

van Renesse와 Schneider는 fail-stop 스토리지 서버 클러스터를 조율하기 위한 새로운 복제 방식인 **chain replication**을 제안했다. 복제본들을 체인(chain)으로 구성하여, **쓰기는 체인의 헤드(head)에서 시작하여 테일(tail)로 전파**되고, **읽기는 항상 테일에서 처리**된다.

이 구조의 핵심 장점: (1) **강한 일관성** -- 읽기는 항상 테일(모든 쓰기가 적용된 후의 상태)에서 처리되므로 linearizability가 자동 보장, (2) **높은 처리량** -- 읽기 부하가 테일에, 쓰기 부하가 전체 체인에 분산, (3) **단순한 장애 복구** -- 헤드 장애 시 두 번째가 새 헤드, 테일 장애 시 이전 노드가 새 테일, 중간 노드 장애 시 전후 연결.

체인 복제는 primary-backup 복제에 비해 쓰기 처리량이 더 높고(primary가 병목이 되지 않음), 읽기 처리량도 높다(전용 테일 노드). 실험 결과 primary-backup 대비 일관된 성능 향상이 확인되었다.

**Dynamo에서의 영향**: Dynamo는 체인 복제가 아닌 쿼럼 기반 복제를 채택했지만, 체인 복제의 분석은 Dynamo의 복제 전략 선택에 대한 비교 기준이 된다. 체인 복제가 강한 일관성을 제공하는 반면, Dynamo는 가용성을 위해 일관성을 완화하는 다른 설계 선택을 했다.

### 8.3 Optimistic Replication

- **저자**: Yasushi Saito, Marc Shapiro
- **학회**: *ACM Computing Surveys*, Vol. 37, No. 1, pp. 42-81, March 2005
- **DOI**: 10.1145/1057977.1057980

**핵심 기여 (상세)**:

이 포괄적 서베이 논문은 낙관적 복제(optimistic replication) 알고리즘을 체계적으로 분류하고 분석한다. 낙관적 복제란 복제본 내용이 단기적으로 발산(diverge)하는 것을 허용하여, 동시 작업(concurrent work)을 지원하고 저품질 통신 링크에서의 장애를 허용하는 알고리즘을 말한다.

논문은 낙관적 복제의 핵심 구성 요소를 다섯 가지로 분류한다: (1) **연산 제출(operation submission)** -- 사용자가 임의의 복제본에 업데이트 제출, (2) **전파(propagation)** -- 업데이트를 다른 복제본으로 백그라운드 전파, (3) **스케줄링(scheduling)** -- 업데이트 적용 순서 결정, (4) **충돌 감지(conflict detection)** -- 동시 업데이트 간의 충돌 발견, (5) **충돌 해결(conflict resolution)** -- 감지된 충돌의 해소.

전파 방식은 **state transfer**(전체 상태 전송)과 **operation transfer**(연산 전송)으로 구분되며, 충돌 해결은 **syntactic**(내용 기반)과 **semantic**(의미 기반)으로 구분된다. 또한 **전염병(epidemic) 전파**, **version vector 기반 충돌 감지**, **last-writer-wins** 등의 구체적 기법을 상세히 분석한다.

**Dynamo에서의 구체적 활용**: Dynamo는 전형적인 낙관적 복제 시스템이다. 쓰기는 비동기적으로 전파(epidemic 방식)되고, vector clock을 통해 충돌이 감지되며, 클라이언트에게 충돌 해결을 위임(semantic conflict resolution)한다. Saito-Shapiro의 분류 체계에서 Dynamo의 정확한 위치를 이해할 수 있다.

### 8.4 Conflict-free Replicated Data Types

- **저자**: Marc Shapiro, Nuno Preguica, Carlos Baquero, Marek Zawirski
- **학회**: *SSS 2011* (13th International Symposium on Stabilization, Safety, and Security of Distributed Systems), LNCS 6976, pp. 386-400, 2011
- **DOI**: 10.1007/978-3-642-24550-3_29

**핵심 기여 (상세)**:

CRDT(Conflict-free Replicated Data Type)는 분산 시스템에서 충돌 없이 복제된 데이터를 관리하는 데이터 구조이다. Shapiro 등은 두 가지 접근법을 형식화했다: (1) **State-based CRDT (CvRDT)** -- 복제본 간에 상태를 교환하고, 상태들을 반(semi)격자의 join 연산으로 병합. 교환적(commutative), 결합적(associative), 멱등적(idempotent) join이 보장되므로 순서와 중복에 무관하게 수렴. (2) **Operation-based CRDT (CmRDT)** -- 연산을 복제본에 전파하되, 교환적 연산을 사용하여 순서에 무관하게 수렴.

CRDT의 핵심 성질은 **강한 최종 일관성(Strong Eventual Consistency)**이다: 동일한 업데이트 집합을 수신한 두 복제본은, 업데이트 수신 순서에 관계없이, 반드시 동일한 상태로 수렴한다. 이는 eventual consistency보다 강력한 보장으로, 충돌 해결 로직이 필요 없다.

논문은 G-Counter, PN-Counter, G-Set, 2P-Set, LWW-Register, OR-Set 등 다양한 유용한 CRDT를 정의하고, 더 복잡한 Graph 데이터 타입까지 탐구한다.

**Dynamo에서의 영향**: CRDT는 Dynamo의 "장바구니"와 같은 데이터 구조의 충돌 해결에 직접 적용 가능하다. Dynamo에서 클라이언트가 수동으로 수행해야 했던 충돌 해결을 CRDT로 자동화할 수 있다. Riak(Dynamo 후속 시스템)은 실제로 CRDT를 내장하여 지원한다.

---

## 9. Sloppy Quorum과 Hinted Handoff

### 9.1 Sloppy Quorum의 기원

Sloppy quorum이라는 용어와 기법은 **Dynamo 논문 자체에서 처음 명명**되었다. 이전의 관련 연구들이 유사한 아이디어의 기반을 마련했지만, "sloppy quorum"이라는 구체적 메커니즘은 Dynamo의 독창적 기여이다.

**관련 선행 연구**:

Gifford의 가중 투표(1979)와 Thomas의 과반수 합의(1979)가 쿼럼 시스템의 기반을 마련했다. Malkhi와 Reiter의 확률적 쿼럼(1997)은 쿼럼 교차를 확률적으로 보장하는 아이디어를 제시했다. Gray 등의 "dangers of replication"(1996)에서의 잠정적 업데이트 개념과 Bayou의 약한 연결 환경에서의 비확정적 쓰기(1995)가 sloppy quorum의 사상적 기원이다.

**Dynamo에서의 핵심 혁신**: 전통적 쿼럼은 키를 담당하는 고정된 N개의 노드에서 쿼럼을 구성해야 한다. 그러나 이 노드들 중 일부가 장애이면 쿼럼을 구성할 수 없어 요청이 실패한다. Sloppy quorum은 이를 해결하기 위해, 장애 노드 대신 consistent hash ring에서 다음 건강한 노드를 사용하여 쿼럼을 구성한다. 이렇게 임시로 대리 저장된 데이터는 **hinted handoff**를 통해 원래 노드가 복구되면 전달된다.

### 9.2 Hinted Handoff의 선행 연구

Hinted handoff 역시 Dynamo에서 명명되었지만, 유사한 아이디어는 이전에 존재했다:

- **CAN**의 영역 인수(zone takeover): 장애 노드의 이웃이 해당 영역을 임시로 관리
- **Bayou**의 세션 전이: 장애 서버에서 다른 서버로의 세션 이동
- **Grapevine system** (Birrell et al., 1982): 메일 서버 장애 시 다른 서버가 임시로 메일을 저장하고 복구 시 전달

---

## 10. Eventually Consistent 모델 -- 약한 일관성의 정립

### 10.1 Managing Update Conflicts in Bayou, a Weakly Connected Replicated Storage System

- **저자**: Douglas B. Terry, Marvin M. Theimer, Karin Petersen, Alan J. Demers, Mike J. Spreitzer, Carl H. Hauser
- **학회**: **SOSP 1995** (15th ACM Symposium on Operating Systems Principles), pp. 172-182, December 1995
- **DOI**: 10.1145/224056.224070

**핵심 기여 (상세)**:

Bayou는 모바일 컴퓨팅 환경을 위해 설계된 약하게 연결된(weakly connected) 복제 스토리지 시스템이다. 가용성을 최대화하기 위해 사용자는 어떤 접근 가능한 복제본에서든 읽기와 쓰기를 수행할 수 있다. 이 설계에서 필연적으로 발생하는 업데이트 충돌을 관리하기 위한 새로운 메커니즘을 제안했다.

Bayou의 핵심 혁신은 **응용 특화 충돌 감지(dependency check)와 충돌 해결(merge procedure)**이다. 각 쓰기 연산에는 의존성 검사(dependency check)와 병합 절차(merge procedure)가 첨부된다. 의존성 검사는 쓰기가 기반한 전제 조건을 명시하고, 이 조건이 위반되면 병합 절차가 자동으로 대안적 업데이트를 생성한다. 예를 들어 회의실 예약 시스템에서, 원하는 시간에 이미 예약이 있으면 자동으로 다음 빈 시간을 예약하는 식이다.

또한 Bayou는 쓰기를 **잠정적(tentative)**과 **확정적(committed)** 두 상태로 구분한다. 잠정적 쓰기는 로컬에서 즉시 반영되지만 다른 복제본에서는 재정렬될 수 있다. 프라이머리 서버(primary server)가 쓰기에 대한 전역 순서를 결정하면 확정적이 된다. 이 2단계 확정 메커니즘으로 eventual consistency가 달성된다.

Bayou의 **anti-entropy 프로토콜**은 복제본 간 업데이트를 게으르게(lazy) 그리고 점진적으로(incremental) 전파하여 확장성을 보장한다.

**Dynamo에서의 구체적 활용**: Dynamo의 핵심 설계 원칙 -- "always writable", eventually consistent, 클라이언트 측 충돌 해결 -- 은 모두 Bayou에서 직접적 영향을 받았다. Dynamo가 충돌 해결을 클라이언트에 위임하는 것은 Bayou의 응용 특화 merge procedure 아이디어를 채택한 것이다. 또한 Dynamo의 anti-entropy 프로세스(Merkle tree 기반)는 Bayou의 게으른 동기화 개념을 확장한 것이다.

**해결한 문제**: 네트워크 파티션이나 노드 장애가 빈번한 환경에서도 쓰기를 항상 허용하면서, 최종적으로 모든 복제본이 수렴하는 메커니즘이 필요했다.

### 10.2 Session Guarantees for Weakly Consistent Replicated Data

- **저자**: Douglas B. Terry, Alan J. Demers, Karin Petersen, Mike J. Spreitzer, Marvin M. Theimer, Brent W. Welch
- **학회**: *PDIS 1994* (3rd International Conference on Parallel and Distributed Information Systems), pp. 140-149, September 1994
- **DOI**: 10.1109/PDIS.1994.331722

**핵심 기여 (상세)**:

Terry 등은 약한 일관성(weak consistency)을 제공하는 복제 시스템에서 개별 클라이언트의 경험을 개선하기 위한 네 가지 **세션 보장(session guarantee)**을 제안했다:

1. **Read Your Writes**: 클라이언트가 쓴 데이터를 이후에 반드시 읽을 수 있음
2. **Monotonic Reads**: 한번 읽은 데이터보다 오래된 데이터를 이후에 읽지 않음
3. **Writes Follow Reads**: 쓰기가 이전에 읽은 데이터를 반영한 상태에서 수행됨
4. **Monotonic Writes**: 쓰기 순서가 보존됨

이 보장들은 개별적으로 또는 조합하여 제공될 수 있으며, read-any/write-any 복제 체계 위에 레이어로 구현할 수 있다. 핵심 아이디어는 각 세션에서 읽기/쓰기 집합(read set, write set)을 추적하고, 새로운 서버에 접근할 때 해당 서버가 이 집합의 업데이트를 포함하는지 확인하는 것이다.

이 논문의 중요성은 "eventual consistency"만으로는 사용자 경험이 매우 혼란스러울 수 있으며, 세션 보장이라는 중간 수준의 일관성이 실용적으로 중요하다는 것을 보인 데 있다.

**Dynamo에서의 영향**: Dynamo는 read-your-writes 일관성을 보장하기 위해 coordinator 노드 선택 전략을 사용한다. 클라이언트가 특정 키에 대해 항상 같은 coordinator에 접근하면 자연스럽게 read-your-writes가 보장된다. 이는 Terry의 세션 보장 개념의 직접적 적용이다.

### 10.3 Eventually Consistent

- **저자**: Werner Vogels
- **학회**: *Communications of the ACM*, Vol. 52, No. 1, pp. 40-44, January 2009 (원래 *ACM Queue*, Vol. 6, No. 6, October 2008)
- **DOI**: 10.1145/1435417.1435432

**핵심 기여 (상세)**:

Amazon CTO Werner Vogels(Dynamo 논문의 공저자)가 다양한 일관성 모델을 체계적으로 정리하고, Amazon의 대규모 분산 시스템 설계에서의 트레이드오프를 논의한다. 이 논문은 Dynamo의 설계 철학에 대한 사후적 해설(retrospective explanation)의 성격을 가진다.

Vogels는 일관성 모델을 다음과 같이 분류한다:
- **Strong consistency**: 업데이트 완료 후 모든 후속 읽기가 최신 값 반환
- **Eventual consistency**: 업데이트가 충분한 시간이 지나면 모든 복제본에 전파되어, 결국 모든 읽기가 최신 값 반환
- **Causal consistency**: 인과적으로 관련된 쓰기가 모든 곳에서 동일 순서로 관찰
- **Read-your-writes consistency**: 클라이언트가 자신의 쓰기를 항상 읽을 수 있음
- **Session consistency**: 세션 내에서 read-your-writes 보장
- **Monotonic read consistency**: 시간이 지남에 따라 점점 더 최신 데이터를 읽음
- **Monotonic write consistency**: 쓰기가 순서대로 직렬화

Vogels는 Amazon의 S3, SimpleDB, EC2 등의 인프라 서비스가 이러한 일관성 모델의 다양한 조합을 사용한다고 설명한다.

**Dynamo에서의 구체적 활용**: Dynamo의 eventual consistency 모델을 학술적으로 정리하고 정당화한 논문이다. Dynamo의 R + W > N 쿼럼에서 R = W = N = 3, R = 2, W = 2 설정이 어떻게 "합리적 시간 내의 eventual consistency"를 제공하는지를 설명한다.

### 10.4 Providing High Availability Using Lazy Replication

- **저자**: Rivka Ladin, Barbara Liskov, Liuba Shrira, Sanjay Ghemawat
- **학회**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 10, No. 4, pp. 360-391, November 1992
- **DOI**: 10.1145/138873.138877

**핵심 기여 (상세)**:

Ladin, Liskov, Shrira, Ghemawat는 높은 가용성을 제공하면서도 일관성을 유지하는 **게으른 복제(lazy replication)** 기법을 제안했다. 핵심 아이디어는 모든 연산을 동일 순서로 모든 사이트에 강제하는 대신, 약한 **인과적 순서(causal ordering)**를 보장하는 것이 대부분의 응용에 충분하다는 것이다.

논문은 세 가지 유형의 연산을 지원한다: (1) **인과적 연산(causal operation)** -- 인과적 순서만 보장되면 충분한 연산, (2) **강제적 연산(forced operation)** -- 서로 간에 전체 순서가 보장되어야 하는 연산, (3) **즉시적 연산(immediate operation)** -- 다른 모든 연산에 대해 전체 순서가 보장되어야 하는 연산.

인과적 연산은 gossip 프로토콜을 통해 저비용으로 전파되고, 강제적/즉시적 연산만 더 비싼 합의 프로토콜을 사용한다. 이 계층적 접근법으로 대부분의 연산(인과적)이 빠르게 처리되면서도, 필요한 경우 강한 일관성을 제공할 수 있다.

**Dynamo에서의 구체적 활용**: Dynamo의 get/put 연산은 본질적으로 "인과적 연산"에 해당하며, gossip을 통한 비동기적 복제와 vector clock을 통한 인과적 순서 추적은 Ladin 등의 모델을 직접 반영한다. 또한 Sanjay Ghemawat(이 논문의 공저자)은 후에 Google File System의 설계자가 되었으며, 분산 시스템 연구의 흐름에서 이 논문의 중요성을 보여준다.

**해결한 문제**: 강한 일관성의 비용을 지불하지 않으면서도 인과적 일관성을 보장하는 효율적 복제 기법이 필요했다.

---

## 11. Virtual Synchrony와 Process Groups

### 11.1 Exploiting Virtual Synchrony in Distributed Systems

- **저자**: Kenneth P. Birman, Thomas A. Joseph
- **학회**: **SOSP 1987** (11th ACM Symposium on Operating Systems Principles), Austin, Texas, November 1987, pp. 123-138
- **DOI**: 10.1145/41457.37515

**핵심 기여 (상세)**:

Birman과 Joseph는 **가상 동기(virtual synchrony)** 모델을 제안하여, 분산 시스템에서 프로세스 그룹 기반의 프로그래밍을 가능하게 했다. Virtual synchrony는 프로세스들을 그룹으로 구성하고, 그룹에 대한 브로드캐스트, 멤버십 변경(view change), 마이그레이션 등의 이벤트가 **동기적으로 일어나는 것처럼** 보이게 하는 추상화이다.

핵심 속성: 같은 그룹의 모든 멤버가 동일한 순서의 "뷰(view)" 변경을 관찰한다. 뷰 v에서 전송된 메시지는 모든 멤버가 뷰 v에 있는 동안 전달되거나, 전혀 전달되지 않는다(all-or-nothing). 이를 통해 복잡한 분산 애플리케이션(복제 데이터베이스, 분산 잠금 관리 등)을 훨씬 단순하게 구현할 수 있다.

이 모델은 ISIS 시스템에서 구현되었으며, 1987년 공개 이후 빠르게 인기를 얻어 뉴욕 증권거래소, 프랑스 항공 교통 관제 시스템 등에서 사용되었다.

**Dynamo에서의 영향**: Dynamo는 virtual synchrony를 사용하지 않지만, 이 연구는 분산 시스템에서의 그룹 통신과 멤버십 관리의 중요성을 확립했다. Dynamo의 gossip 기반 멤버십 관리는 ISIS의 virtual synchrony에 비해 약한 보장을 제공하지만, 확장성에서 우위를 가진다.

### 11.2 The Process Group Approach to Reliable Distributed Computing

- **저자**: Kenneth P. Birman
- **학회**: *Communications of the ACM*, Vol. 36, No. 12, pp. 37-53, December 1993
- **DOI**: 10.1145/163298.163303

**핵심 기여 (상세)**:

Birman은 프로세스 그룹 접근법을 통한 신뢰성 있는 분산 컴퓨팅을 종합적으로 설명한다. 이 논문은 virtual synchrony, group membership, ordered multicast 등의 개념을 통합적으로 제시하며, ISIS 시스템의 설계 철학과 실무 경험을 공유한다.

핵심 주장: 네트워크가 보장을 제공하면 사용자 수준의 설계 복잡성이 감소한다. 즉 통신 계층에서 인과적 순서나 전체 순서를 보장하면, 응용 프로그래머가 이를 직접 구현할 필요가 없다.

**Dynamo에서의 영향**: Birman의 접근법은 "네트워크/미들웨어가 강한 보장을 제공"하는 반면, Dynamo는 "응용이 약한 보장을 수용하고 충돌을 직접 해결"하는 정반대의 설계 철학을 택했다. 이 대조는 Dynamo의 설계 선택을 더 깊이 이해하는 데 도움이 된다.

### 11.3 Lightweight Causal and Atomic Group Multicast

- **저자**: Kenneth Birman, Andre Schiper, Pat Stephenson
- **학회**: *ACM Transactions on Computer Systems (TOCS)*, Vol. 9, No. 3, pp. 272-314, August 1991
- **DOI**: 10.1145/128738.128742

**핵심 기여 (상세)**:

이 논문은 인과적으로 정렬된(causally ordered) 멀티캐스트를 효율적으로 구현하는 프로토콜을 제시하고, 이를 전체 순서(totally ordered) 멀티캐스트로 쉽게 확장할 수 있음을 보였다. 프로토콜은 vector clock을 사용하여 메시지의 인과적 순서를 추적하며, hold-back 큐와 delivery 큐를 통해 순서를 보장한다.

이 프로토콜은 ISIS 시스템의 가장 비용이 큰 측면을 우회하면서도 virtual synchrony의 이점을 유지하도록 설계되었다. 제한된 오버헤드로 확장 가능하다는 점이 핵심이다.

**Dynamo에서의 영향**: vector clock을 사용한 인과적 순서 추적 기법이 Dynamo의 버전 관리에 간접적으로 영향을 미쳤다.

### 11.4 Understanding the Limitations of Causally and Totally Ordered Communication

- **저자**: David R. Cheriton, Dale Skeen
- **학회**: **SOSP 1993** (14th ACM Symposium on Operating Systems Principles), pp. 44-57, December 1993
- **DOI**: 10.1145/168619.168623

**핵심 기여 (상세)**:

Cheriton과 Skeen은 인과적/전체 순서 통신 지원(CATOCS -- Causally and Totally Ordered Communication Support)의 네 가지 근본적 한계를 지적하여, Birman의 virtual synchrony 접근법에 대한 강력한 비판을 제기했다:

1. **"확실히 말할 수 없다(Can't say for sure)"**: 대안적 통신 채널(out-of-band communication)이 존재하면 인과적 순서가 깨질 수 있다.
2. **"전체 이야기를 할 수 없다(Can't say the whole story)"**: 일부 의미적 제약은 happened-before 관계보다 강하다.
3. **"함께 말할 수 없다(Can't say together)"**: 여러 메시지에 대응하는 이벤트들의 직렬가능성을 보장할 수 없다.
4. **"효율적으로 말할 수 없다(Can't say efficiently)"**: CATOCS 구현에 사용되는 메커니즘을 더 상위 계층에서 더 효율적으로 구현할 수 있다.

핵심 주장은 CATOCS가 유명한 "end-to-end argument"를 위반한다는 것이다. 일관성 요구사항은 보통 응용의 상태와 연결되므로, 통신 수준이 아닌 상태 수준의 프레임워크에서 처리해야 한다.

이 논문은 큰 논쟁을 불러일으켰으며, Birman은 Cheriton과 Skeen이 진정한 논쟁을 왜곡했다고 반박하는 응답 논문을 발표했다.

**Dynamo에서의 영향**: 이 비판은 Dynamo의 설계 철학과 놀랍도록 일치한다. Dynamo는 통신 계층에서 강한 순서를 보장하는 대신, 응용 수준(클라이언트)에서 충돌을 해결하는 "end-to-end" 접근법을 택했다. Cheriton-Skeen의 비판이 실용적 시스템 설계에 반영된 대표적 사례이다.

---

## 12. Read Repair와 Anti-Entropy Protocols

### 12.1 Epidemic Algorithms (재참조: 섹션 3.1)

Demers 등(1987)의 epidemic algorithms 논문에서 정의된 **anti-entropy** 프로세스가 read repair의 이론적 기원이다. Anti-entropy는 두 복제본이 주기적으로 상태를 비교하여 차이를 해소하는 백그라운드 프로세스이다. 세 가지 변형이 있다:

- **Push**: 보유한 업데이트를 상대에게 전송
- **Pull**: 상대의 업데이트를 가져옴
- **Push-Pull**: 양방향 교환

**Dynamo에서의 활용**: Dynamo는 두 가지 anti-entropy 메커니즘을 사용한다:
1. **Read repair**: 읽기 연산 시 여러 복제본에서 데이터를 가져와 비교하고, 오래된 복제본을 최신으로 갱신 (pull 변형)
2. **Merkle tree 기반 anti-entropy**: 백그라운드에서 복제본 간 Merkle tree를 비교하여 차이를 감지하고 동기화 (push-pull 변형)

### 12.2 Efficient Reconciliation and Flow Control for Anti-Entropy Protocols

- **저자**: Robbert van Renesse, Dan Dumitriu, Valient Goel, Chris Thomas
- **학회**: *Proceedings of the 2nd Workshop on Large-Scale Distributed Systems and Middleware (LADIS '08)*, 2008

**핵심 기여**: 대규모 시스템에서 anti-entropy 프로토콜의 효율성을 높이기 위한 기법을 제안했다. 특히 재조정(reconciliation) 과정에서의 네트워크 대역폭 사용을 최소화하는 흐름 제어(flow control) 메커니즘을 제시했다.

---

## 13. Amazon 내부 시스템과 관련 연구

### 13.1 Cluster-Based Scalable Network Services

- **저자**: Armando Fox, Steven D. Gribble, Yatin Chawathe, Eric A. Brewer, Paul Gauthier
- **학회**: **SOSP 1997** (16th ACM Symposium on Operating Systems Principles), Saint-Malo, France, October 1997, pp. 78-91
- **DOI**: 10.1145/268998.266662

**핵심 기여 (상세)**:

이 논문은 클러스터 기반의 확장 가능한 네트워크 서비스를 위한 TACC(Transformation, Aggregation, Caching, Customization) 아키텍처를 제안했다. 핵심 통찰은 인터넷 서비스를 모듈화된 구성요소로 분해하고, 이를 상용 클러스터에서 실행하여 확장성과 가용성을 달성하는 것이다.

이 논문은 SEDA(Staged Event-Driven Architecture)의 전신이며, Amazon의 서비스 지향 아키텍처(SOA) 전환에 영향을 미쳤다.

**Dynamo에서의 영향**: Amazon의 전체 아키텍처가 수백 개의 마이크로서비스로 구성된 것은 이 연구의 직접적 영향이며, Dynamo는 이러한 서비스 아키텍처에서 상태 관리를 담당하는 핵심 컴포넌트로 설계되었다.

### 13.2 SEDA: An Architecture for Well-Conditioned, Scalable Internet Services

- **저자**: Matt Welsh, David Culler, Eric Brewer
- **학회**: **SOSP 2001** (18th ACM Symposium on Operating Systems Principles), pp. 230-243, October 2001
- **DOI**: 10.1145/502034.502057

**핵심 기여 (상세)**:

Welsh, Culler, Brewer는 인터넷 서비스를 위한 Staged Event-Driven Architecture(SEDA)를 제안했다. SEDA는 서비스를 여러 "단계(stage)"로 분해하고, 각 단계가 이벤트 큐와 스레드 풀로 구성되어 독립적으로 부하를 관리한다. 단계 간 연결은 이벤트 큐를 통해 이루어지며, 각 단계는 자신의 부하에 맞게 리소스를 동적으로 조절(resource controller)한다.

이를 통해 과부하 상황에서도 서비스가 우아하게 성능을 저하(graceful degradation)시킬 수 있다. 기존의 thread-per-request 모델이 과부하 시 급격히 성능이 저하되는 것과 대조적이다.

**Dynamo에서의 영향**: Dynamo 논문(참고문헌 [27])에서 직접 인용되었으며, Dynamo 노드의 요청 처리 파이프라인이 SEDA의 설계 원칙을 따른다.

### 13.3 Vogels의 Amazon 아키텍처 관련 저술

Werner Vogels(Amazon CTO)는 2006년 "All Things Distributed" 블로그에서 Amazon의 기술 플랫폼에 대한 중요한 통찰을 공유했다:

- **"A Word on Scalability"** (March 2006): 확장성을 "리소스 증가가 성능 향상에 비례하는 것"으로 정의
- **"The Amazon Technology Platform"** (May 2006): Amazon이 모놀리식에서 수백 개의 독립적 서비스로 전환한 과정을 설명

이 저술들은 Dynamo가 탄생한 조직적/기술적 맥락을 이해하는 데 중요하다. Amazon의 게이트웨이 페이지가 100개 이상의 서비스를 호출하여 데이터를 수집한다는 사실은, 각 서비스가 독립적으로 상태를 관리할 수 있는 Dynamo 같은 시스템의 필요성을 보여준다.

---

## 14. 추가 기반 논문들

### 14.1 The Google File System

- **저자**: Sanjay Ghemawat, Howard Gobioff, Shun-Tak Leung
- **학회**: **SOSP 2003** (19th ACM Symposium on Operating Systems Principles), pp. 29-43, October 2003
- **DOI**: 10.1145/945445.945450

**핵심 기여**: Google의 대규모 분산 파일 시스템 설계로, 상용 하드웨어에서의 장애를 정상적인 상황으로 간주하고 소프트웨어 수준에서 장애를 처리한다. 단일 마스터 노드, 대용량 청크(64MB), append 최적화 등의 설계 결정이 포함되어 있다.

**Dynamo에서의 영향**: GFS는 강한 일관성(단일 마스터)을 선택한 반면, Dynamo는 가용성을 위해 마스터 없는(masterless) 설계를 선택했다. 두 시스템의 대조는 CAP 트레이드오프의 서로 다른 선택을 보여준다.

### 14.2 Bigtable: A Distributed Storage System for Structured Data

- **저자**: Fay Chang, Jeffrey Dean, Sanjay Ghemawat, Wilson C. Hsieh, Deborah A. Wallach, Mike Burrows, Tushar Chandra, Andrew Fikes, Robert E. Gruber
- **학회**: **OSDI 2006** (7th Symposium on Operating Systems Design and Implementation), pp. 205-218, November 2006

**핵심 기여**: Google의 구조화된 데이터를 위한 분산 스토리지 시스템으로, 수천 대의 서버에서 페타바이트 규모의 데이터를 관리한다. 행 키, 열 키, 타임스탬프의 3차원 맵 구조를 사용하며, GFS 위에 구축된다.

**Dynamo에서의 영향**: Bigtable은 Dynamo와 동시대에 발표된 경쟁적 설계로, CP(Consistency + Partition tolerance) 선택을 대표한다. Cassandra는 Dynamo의 파티셔닝과 Bigtable의 데이터 모델을 결합한 하이브리드 시스템이다.

### 14.3 Impossibility of Distributed Consensus with One Faulty Process (FLP)

- **저자**: Michael J. Fischer, Nancy A. Lynch, Michael S. Paterson
- **학회**: *Journal of the ACM*, Vol. 32, No. 2, pp. 374-382, April 1985
- **DOI**: 10.1145/3149.214121

**핵심 기여 (상세)**:

FLP 불가능성 결과는 분산 컴퓨팅의 가장 중요한 이론적 결과 중 하나이다. 비동기 시스템에서 단 하나의 프로세스 장애만 발생할 수 있어도 결정론적(deterministic) 합의(consensus) 프로토콜이 존재하지 않음을 증명했다.

증명의 핵심: 비동기 시스템에서는 메시지 전달에 상한이 없으므로, 프로세스 장애와 단순한 지연을 구별할 수 없다. 시스템이 이진(bivalent) 상태에서 시작하여 한 메시지의 전달/지연에 의해 결정이 바뀔 수 있는데, 이 메시지가 무한히 지연되면 시스템은 영원히 결정에 도달하지 못한다.

**Dynamo에서의 영향**: FLP 결과는 Dynamo가 완전한 일관성을 포기하고 eventual consistency를 선택한 이론적 근거 중 하나이다. 비동기 네트워크에서의 합의 불가능성을 인정하고, 대신 확률적/최종적 수렴을 추구하는 것이 Dynamo의 설계 철학이다.

### 14.4 The Byzantine Generals Problem

- **저자**: Leslie Lamport, Robert Shostak, Marshall Pease
- **학회**: *ACM Transactions on Programming Languages and Systems (TOPLAS)*, Vol. 4, No. 3, pp. 382-401, July 1982
- **DOI**: 10.1145/357172.357176

**핵심 기여**: 비잔틴 장애(임의의 악의적 동작)가 있는 환경에서의 합의 문제를 형식화하고, 3f+1개 이상의 노드가 있을 때만 f개의 비잔틴 장애를 허용할 수 있음을 증명했다.

**Dynamo에서의 영향**: Dynamo는 비잔틴 장애를 가정하지 않고 fail-stop 장애만을 고려한다. 이는 Amazon의 통제된 데이터센터 환경에서는 합리적인 가정이며, 설계를 크게 단순화한다.

### 14.5 Farsite: Federated, Available, and Reliable Storage for an Incompletely Trusted Environment

- **저자**: Atul Adya, William J. Bolosky, Miguel Castro, Gerald Cermak, Ronnie Chaiken, John R. Douceur, Jon Howell, Jacob R. Lorch, Marvin Theimer, Roger P. Wattenhofer
- **학회**: **OSDI 2002** (5th Symposium on Operating Systems Design and Implementation), December 2002

**핵심 기여**: 불완전하게 신뢰할 수 있는 환경에서의 연합(federated) 스토리지 시스템 설계. 비잔틴 장애 허용과 분산 파일 시스템을 결합한다.

**Dynamo에서의 영향**: Dynamo 논문의 참고문헌에 포함되어 있으며, 신뢰할 수 없는 환경에서의 복제 전략에 대한 비교 대상을 제공한다.

---

## 15. Dynamo의 직접적 후속 시스템과 영향

### 15.1 Cassandra - A Decentralized Structured Storage System

- **저자**: Avinash Lakshman, Prashant Malik
- **학회**: *LADIS 2009* (3rd ACM SIGOPS International Workshop on Large Scale Distributed Systems and Middleware), 2009
- **DOI**: 10.1145/1773912.1773922

**핵심 기여**: Facebook에서 개발된 Cassandra는 Dynamo의 파티셔닝과 복제 기법(consistent hashing, gossip, eventual consistency)을 Bigtable의 데이터 모델(열 기반 스토리지)과 결합한 하이브리드 시스템이다. Avinash Lakshman은 Dynamo 논문의 공저자이기도 하다.

### 15.2 Amazon DynamoDB

- **저자**: Amazon Web Services
- **학회**: *USENIX ATC 2022*, "Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service"

**핵심 기여**: DynamoDB는 Dynamo의 원칙 위에 구축된 완전 관리형 서비스이지만, 중요한 설계 변경이 있다. 특히 Dynamo의 leaderless replication 대신 **single-leader replication**을 채택하여 강한 일관성 옵션을 제공한다. 이는 Dynamo의 eventual consistency가 많은 고객에게 충분하지 않았음을 시사한다.

---

## 종합 참고문헌

### Consistent Hashing
1. D. Karger, E. Lehman, F. T. Leighton, R. Panigrahy, M. Levine, D. Lewin, "Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web," *Proc. STOC*, pp. 654-663, 1997. DOI: 10.1145/258533.258660
2. M. Naor, U. Wieder, "Novel Architectures for P2P Applications: the Continuous-Discrete Approach," *Proc. SPAA*, pp. 50-59, 2003.
3. J. Byers, J. Considine, M. Mitzenmacher, "Simple Efficient Load Balancing Algorithms for Peer-to-Peer Systems," *Proc. SPAA*, pp. 36-43, 2003.

### Vector Clocks and Version Tracking
4. L. Lamport, "Time, Clocks, and the Ordering of Events in a Distributed System," *Communications of the ACM*, 21(7):558-565, 1978. DOI: 10.1145/359545.359563
5. C. J. Fidge, "Timestamps in Message-Passing Systems That Preserve the Partial Ordering," *Proc. 11th Australian Computer Science Conference*, 10(1):56-66, 1988.
6. F. Mattern, "Virtual Time and Global States of Distributed Systems," *Parallel and Distributed Algorithms*, pp. 215-226, 1989.
7. D. S. Parker et al., "Detection of Mutual Inconsistency in Distributed Systems," *IEEE Trans. Software Engineering*, SE-9(3):240-247, 1983. DOI: 10.1109/TSE.1983.236733
8. N. Preguica, C. Baquero, P. S. Almeida, V. Fonte, R. Goncalves, "Dotted Version Vectors: Logical Clocks for Optimistic Replication," *arXiv:1011.5808*, 2010.
9. P. S. Almeida, C. Baquero, V. Fonte, "Interval Tree Clocks: A Logical Clock for Dynamic Systems," *Proc. OPODIS*, LNCS 5401, pp. 259-274, 2008. DOI: 10.1007/978-3-540-92221-6_18

### Gossip Protocols and Failure Detection
10. A. Demers et al., "Epidemic Algorithms for Replicated Database Maintenance," *Proc. PODC*, 1987. DOI: 10.1145/41840.41841
11. A. Das, I. Gupta, A. Motivala, "SWIM: Scalable Weakly-consistent Infection-style Process Group Membership Protocol," *Proc. DSN*, pp. 303-312, 2002. DOI: 10.1109/DSN.2002.1028914
12. N. Hayashibara, X. Defago, R. Yared, T. Katayama, "The Phi Accrual Failure Detector," *Proc. SRDS*, pp. 66-78, 2004. DOI: 10.1109/RELDIS.2004.1353004
13. T. D. Chandra, S. Toueg, "Unreliable Failure Detectors for Reliable Distributed Systems," *J. ACM*, 43(2):225-267, 1996. DOI: 10.1145/226643.226647

### Distributed Hash Tables
14. I. Stoica, R. Morris, D. Karger, M. F. Kaashoek, H. Balakrishnan, "Chord: A Scalable Peer-to-Peer Lookup Service for Internet Applications," *Proc. SIGCOMM*, pp. 149-160, 2001. DOI: 10.1145/383059.383071
15. I. Stoica et al., "Chord: A Scalable Peer-to-Peer Lookup Protocol for Internet Applications," *IEEE/ACM Trans. Networking*, 11(1):17-32, 2003. DOI: 10.1109/TNET.2002.808407
16. A. Rowstron, P. Druschel, "Pastry: Scalable, Decentralized Object Location and Routing for Large-Scale Peer-to-Peer Systems," *Proc. Middleware*, pp. 329-350, 2001. DOI: 10.1007/3-540-45518-3_18
17. S. Ratnasamy, P. Francis, M. Handley, R. Karp, S. Shenker, "A Scalable Content-Addressable Network," *Proc. SIGCOMM*, pp. 161-172, 2001. DOI: 10.1145/383059.383072
18. P. Maymounkov, D. Mazieres, "Kademlia: A Peer-to-Peer Information System Based on the XOR Metric," *Proc. IPTPS*, LNCS 2429, pp. 53-65, 2002. DOI: 10.1007/3-540-45748-8_5
19. B. Y. Zhao, J. Kubiatowicz, A. D. Joseph, "Tapestry: An Infrastructure for Fault-tolerant Wide-area Location and Routing," UC Berkeley Technical Report UCB/CSD-01-1141, 2001.
20. C. G. Plaxton, R. Rajaraman, A. W. Richa, "Accessing Nearby Copies of Replicated Objects in a Distributed Environment," *Proc. SPAA*, pp. 311-320, 1997. DOI: 10.1145/258492.258523

### CAP Theorem
21. A. Fox, E. A. Brewer, "Harvest, Yield, and Scalable Tolerant Systems," *Proc. HotOS-VII*, pp. 174-178, 1999. DOI: 10.1109/HOTOS.1999.798396
22. E. A. Brewer, "Towards Robust Distributed Systems," *Keynote, ACM PODC*, July 2000.
23. S. Gilbert, N. Lynch, "Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services," *ACM SIGACT News*, 33(2):51-59, 2002. DOI: 10.1145/564585.564601
24. E. A. Brewer, "CAP Twelve Years Later: How the 'Rules' Have Changed," *IEEE Computer*, 45(2):23-29, 2012. DOI: 10.1109/MC.2012.37
25. D. J. Abadi, "Consistency Tradeoffs in Modern Distributed Database System Design: CAP is Only Part of the Story," *IEEE Computer*, 45(2):37-42, 2012. DOI: 10.1109/MC.2012.33
26. S. Gilbert, N. Lynch, "Perspectives on the CAP Theorem," *IEEE Computer*, 45(2):30-36, 2012. DOI: 10.1109/MC.2012.55

### Quorum Systems
27. D. K. Gifford, "Weighted Voting for Replicated Data," *Proc. SOSP*, pp. 150-162, 1979. DOI: 10.1145/800215.806583
28. R. H. Thomas, "A Majority Consensus Approach to Concurrency Control for Multiple Copy Databases," *ACM TODS*, 4(2):180-209, 1979. DOI: 10.1145/320071.320076
29. M. P. Herlihy, "A Quorum-Consensus Replication Method for Abstract Data Types," *ACM TOCS*, 4(1):32-53, 1986. DOI: 10.1145/6306.6308
30. D. Malkhi, M. Reiter, R. Wright, "Probabilistic Quorum Systems," *Proc. PODC*, pp. 267-273, 1997. DOI: 10.1145/259380.259458
31. D. Malkhi, M. Reiter, "Byzantine Quorum Systems," *Distributed Computing*, 11(4):203-213, 1998. DOI: 10.1007/s004460050050

### Merkle Trees
32. R. C. Merkle, "A Digital Signature Based on a Conventional Encryption Function," *Advances in Cryptology -- CRYPTO '87*, LNCS 293, pp. 369-378, 1987. DOI: 10.1007/3-540-48184-2_32

### Replication Strategies
33. J. Gray, P. Helland, P. O'Neil, D. Shasha, "The Dangers of Replication and a Solution," *Proc. SIGMOD*, pp. 173-182, 1996. DOI: 10.1145/233269.233330
34. R. van Renesse, F. B. Schneider, "Chain Replication for Supporting High Throughput and Availability," *Proc. OSDI*, pp. 91-104, 2004.
35. Y. Saito, M. Shapiro, "Optimistic Replication," *ACM Computing Surveys*, 37(1):42-81, 2005. DOI: 10.1145/1057977.1057980
36. M. Shapiro, N. Preguica, C. Baquero, M. Zawirski, "Conflict-free Replicated Data Types," *Proc. SSS*, LNCS 6976, pp. 386-400, 2011. DOI: 10.1007/978-3-642-24550-3_29

### Eventually Consistent Models
37. D. B. Terry et al., "Managing Update Conflicts in Bayou, a Weakly Connected Replicated Storage System," *Proc. SOSP*, pp. 172-182, 1995. DOI: 10.1145/224056.224070
38. D. B. Terry et al., "Session Guarantees for Weakly Consistent Replicated Data," *Proc. PDIS*, pp. 140-149, 1994. DOI: 10.1109/PDIS.1994.331722
39. W. Vogels, "Eventually Consistent," *Communications of the ACM*, 52(1):40-44, 2009. DOI: 10.1145/1435417.1435432
40. R. Ladin, B. Liskov, L. Shrira, S. Ghemawat, "Providing High Availability Using Lazy Replication," *ACM TOCS*, 10(4):360-391, 1992. DOI: 10.1145/138873.138877

### Virtual Synchrony and Process Groups
41. K. P. Birman, T. A. Joseph, "Exploiting Virtual Synchrony in Distributed Systems," *Proc. SOSP*, pp. 123-138, 1987. DOI: 10.1145/41457.37515
42. K. P. Birman, "The Process Group Approach to Reliable Distributed Computing," *Communications of the ACM*, 36(12):37-53, 1993. DOI: 10.1145/163298.163303
43. K. Birman, A. Schiper, P. Stephenson, "Lightweight Causal and Atomic Group Multicast," *ACM TOCS*, 9(3):272-314, 1991. DOI: 10.1145/128738.128742
44. D. R. Cheriton, D. Skeen, "Understanding the Limitations of Causally and Totally Ordered Communication," *Proc. SOSP*, pp. 44-57, 1993. DOI: 10.1145/168619.168623

### Foundational Distributed Systems Theory
45. M. J. Fischer, N. A. Lynch, M. S. Paterson, "Impossibility of Distributed Consensus with One Faulty Process," *J. ACM*, 32(2):374-382, 1985. DOI: 10.1145/3149.214121
46. L. Lamport, R. Shostak, M. Pease, "The Byzantine Generals Problem," *ACM TOPLAS*, 4(3):382-401, 1982. DOI: 10.1145/357172.357176

### Related Systems
47. S. Ghemawat, H. Gobioff, S.-T. Leung, "The Google File System," *Proc. SOSP*, pp. 29-43, 2003. DOI: 10.1145/945445.945450
48. F. Chang et al., "Bigtable: A Distributed Storage System for Structured Data," *Proc. OSDI*, pp. 205-218, 2006.
49. A. Fox et al., "Cluster-Based Scalable Network Services," *Proc. SOSP*, pp. 78-91, 1997. DOI: 10.1145/268998.266662
50. M. Welsh, D. Culler, E. Brewer, "SEDA: An Architecture for Well-Conditioned, Scalable Internet Services," *Proc. SOSP*, pp. 230-243, 2001. DOI: 10.1145/502034.502057
51. A. Lakshman, P. Malik, "Cassandra - A Decentralized Structured Storage System," *LADIS*, 2009. DOI: 10.1145/1773912.1773922
