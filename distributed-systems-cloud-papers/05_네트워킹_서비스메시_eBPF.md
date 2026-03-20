# Distributed Networking 핵심 논문 종합 조사

> 분산 네트워킹, 서비스 메시, SDN, eBPF 및 관련 분야의 주요 논문 30편 정리

---

## 1. Software-Defined Networking (SDN)

### 1.1 OpenFlow: Enabling Innovation in Campus Networks

- **저자:** Nick McKeown, Tom Anderson, Hari Balakrishnan, Guru Parulkar, Larry Peterson, Jennifer Rexford, Scott Shenker, Jonathan Turner
- **연도:** 2008
- **학회:** ACM SIGCOMM Computer Communication Review, Vol. 38, No. 2
- **요약:** OpenFlow는 이더넷 스위치의 내부 플로우 테이블과 표준화된 인터페이스를 기반으로 연구자들이 실제 네트워크에서 실험적 프로토콜을 실행할 수 있도록 제안한 논문이다. 네트워크 제어 평면의 프로그래밍 가능성을 열어 SDN 패러다임의 시작점이 되었다.
- **중요성:** Google Scholar 인용 10,000회 이상의 SDN 분야 창시 논문으로, 네트워크 혁신의 물꼬를 튼 가장 영향력 있는 논문 중 하나이다.

### 1.2 Software-Defined Networking: A Comprehensive Survey

- **저자:** Diego Kreutz, Fernando M. V. Ramos, Paulo Esteves Verissimo, Christian Esteve Rothenberg, Siamak Azodolmolky, Steve Uhlig
- **연도:** 2015
- **학회:** Proceedings of the IEEE, Vol. 103, No. 1
- **요약:** SDN의 역사적 발전 과정부터 제어 평면, 데이터 평면 기술, 응용 분야를 포괄적으로 조사한 서베이 논문이다. SDN 아키텍처의 각 계층(인프라, 제어, 응용)과 관련 보안 이슈까지 체계적으로 정리했다.
- **중요성:** SDN 분야의 가장 포괄적인 서베이로, SDN을 처음 접하는 연구자와 엔지니어에게 필수 레퍼런스이다.

### 1.3 A Survey of Software-Defined Networking: Past, Present, and Future

- **저자:** Bruno Nunes Astuto, Marc Mendonca, Xuan Nam Nguyen, Katia Obraczka, Thierry Turletti
- **연도:** 2014
- **학회:** IEEE Communications Surveys & Tutorials
- **요약:** 프로그래머블 네트워크의 초기 아이디어부터 최신 SDN 발전까지의 역사적 관점을 제공하는 서베이이다. 과거의 active networking, 프로그래머블 라우터 연구가 어떻게 현대 SDN으로 발전했는지를 추적한다.
- **중요성:** SDN의 역사적 맥락과 미래 연구 방향을 이해하는 데 핵심적인 논문이다.

---

## 2. Datacenter Network Architecture

### 2.1 Jupiter Rising: A Decade of Clos Topologies and Centralized Control in Google's Datacenter Network

- **저자:** Arjun Singh, Joon Ong, Amit Agarwal, Glen Anderson, Ashby Armistead, et al. (Google)
- **연도:** 2015
- **학회:** ACM SIGCOMM 2015
- **요약:** Google의 5세대에 걸친 데이터센터 네트워크 아키텍처(Firehose 1.0부터 Jupiter까지) 진화 과정을 상세히 기술한 논문이다. 최대 구성에서 1.3 Pbps의 이등분 대역폭을 지원하며, Clos 토폴로지와 중앙 집중식 제어의 효과를 실증했다.
- **중요성:** Google의 실제 프로덕션 데이터센터 네트워크를 공개한 획기적 논문으로, 현대 하이퍼스케일 데이터센터 설계의 기준이 되었다.

### 2.2 VL2: A Scalable and Flexible Data Center Network

- **저자:** Albert Greenberg, James R. Hamilton, Navendu Jain, Srikanth Kandula, Changhoon Kim, Parantap Lahiri, David A. Maltz, Parveen Patel, Sudipta Sengupta
- **연도:** 2009
- **학회:** ACM SIGCOMM 2009
- **요약:** 대규모 데이터센터에서 서버 간 균일한 고용량 연결, 서비스 간 성능 격리, 이더넷 L2 시맨틱스를 동시에 제공하는 네트워크 아키텍처를 제안했다. Flat addressing, Valiant Load Balancing, 엔드시스템 기반 주소 해석을 핵심 기법으로 사용한다.
- **중요성:** 클라우드 데이터센터 네트워크 설계의 초석이 된 논문으로, Microsoft Azure 네트워크의 기반이 되었다.

### 2.3 Andromeda: Performance, Isolation, and Velocity at Scale in Cloud Network Virtualization

- **저자:** Michael Dalton, David Schultz, Jacob Adriaens, et al. (Google)
- **연도:** 2018
- **학회:** USENIX NSDI 2018
- **요약:** Google Cloud Platform의 네트워크 가상화 스택인 Andromeda의 설계와 운영 경험을 기술한 논문이다. 성능 격리, 확장성, 빠른 프로비저닝, 하드웨어 수준에 가까운 대역폭/지연시간을 동시에 달성하는 플로우 처리 계층 구조를 제안했다.
- **중요성:** 하이퍼스케일 클라우드에서의 네트워크 가상화 실제 사례를 공개한 중요한 산업 논문이다.

---

## 3. Load Balancing

### 3.1 Maglev: A Fast and Reliable Software Network Load Balancer

- **저자:** Daniel E. Eisenbud, Cheng Yi, Carlo Contavalli, Cody Smith, Roman Kononov, Eric Mann-Hielscher, Ardas Cilingiroglu, Bin Cheyenne, Wentao Shang, Jinnah Dylan Hosein
- **연도:** 2016
- **학회:** USENIX NSDI 2016
- **요약:** Google의 소프트웨어 네트워크 로드 밸런서 Maglev를 소개하며, ECMP를 통한 패킷 분산과 consistent hashing 기반 연결 추적을 사용한다. 2008년부터 Google 트래픽을 처리해 왔으며 Google Cloud Platform의 네트워크 로드 밸런싱도 제공한다.
- **중요성:** 대규모 소프트웨어 로드 밸런서의 설계 원리를 제시한 핵심 논문으로, 업계 표준 참조 아키텍처가 되었다.

### 3.2 Ananta: Cloud Scale Load Balancing

- **저자:** Parveen Patel, Deepak Bansal, Lihua Yuan, Ashwin Murthy, Albert Greenberg, David A. Maltz, et al.
- **연도:** 2013
- **학회:** ACM SIGCOMM 2013
- **요약:** Microsoft Azure의 분산 소프트웨어 로드 밸런서로, 합의 기반 제어 평면과 분산 스케일아웃 데이터 평면으로 구성된다. 호스트 에이전트가 패킷 수정 기능을 담당하여 데이터센터 규모에 자연스럽게 확장되며, 1Tbps 이상의 통합 대역폭을 처리한다.
- **중요성:** 클라우드 규모 로드 밸런싱의 분산 아키텍처를 최초로 공개한 논문으로, Azure 인프라의 핵심 구성요소이다.

---

## 4. TCP Congestion Control (Datacenter)

### 4.1 Data Center TCP (DCTCP)

- **저자:** Mohammad Alizadeh, Albert Greenberg, David A. Maltz, Jitendra Padhye, Parveen Patel, Balaji Prabhakar, Sudipta Sengupta, Murari Sridharan
- **연도:** 2010
- **학회:** ACM SIGCOMM 2010
- **요약:** 데이터센터 환경에 최적화된 TCP 혼잡 제어 알고리즘으로, ECN(Explicit Congestion Notification)을 활용하여 일반 TCP 대비 90% 적은 버퍼를 사용하면서도 동등 이상의 처리량을 달성한다. 높은 버스트 내성과 짧은 플로우에 대한 낮은 지연시간을 동시에 제공한다.
- **중요성:** ACM SIGCOMM "test of time" 수상작으로, 데이터센터 전용 전송 프로토콜 연구의 시발점이 된 기념비적 논문이다.

### 4.2 pFabric: Minimal Near-Optimal Datacenter Transport

- **저자:** Mohammad Alizadeh, Shuang Yang, Milad Sharif, Sachin Katti, Nick McKeown, Balaji Prabhakar, Scott Shenker
- **연도:** 2013
- **학회:** ACM SIGCOMM 2013
- **요약:** 플로우 스케줄링과 레이트 제어를 분리하는 핵심 통찰에 기반한 미니멀리스틱 데이터센터 전송 설계이다. 패킷에 단일 우선순위 번호만 부여하고 스위치는 매우 단순한 우선순위 기반 스케줄링/드롭 메커니즘만 구현한다.
- **중요성:** 99퍼센타일에서도 이론적 최적에 가까운 플로우 완료 시간을 달성하여, 데이터센터 전송 설계의 새로운 패러다임을 제시했다.

### 4.3 HPCC: High Precision Congestion Control

- **저자:** Yuliang Li, Rui Miao, Hongqiang Harry Liu, Yan Zhuang, Fei Feng, Lingbo Tang, Zheng Cao, Ming Zhang, Frank Kelly, Mohammad Alizadeh, Minlan Yu
- **연도:** 2019
- **학회:** ACM SIGCOMM 2019
- **요약:** In-network Telemetry(INT)를 활용하여 정밀한 링크 부하 정보를 수집하고 트래픽을 정밀 제어하는 혼잡 제어 알고리즘이다. DCQCN, TIMELY 대비 플로우 완료 시간을 최대 95% 단축하며, 거의 0에 가까운 인-네트워크 큐를 유지한다.
- **중요성:** 프로그래머블 스위치의 INT 기능을 혼잡 제어에 활용한 선구적 연구로, 초저지연 데이터센터 네트워킹의 새로운 방향을 제시했다.

---

## 5. eBPF / XDP

### 5.1 The eXpress Data Path: Fast Programmable Packet Processing in the Operating System Kernel

- **저자:** Toke Hoiland-Jorgensen, Jesper Dangaard Brouer, Daniel Borkmann, John Fastabend, Tom Herbert, David Ahern, David Miller
- **연도:** 2018
- **학회:** ACM CoNEXT 2018
- **요약:** 리눅스 커널 내에서 NIC에 가까운 지점에서 eBPF 가상 머신을 통해 빠른 패킷 처리를 수행하는 XDP(eXpress Data Path)를 기술한다. 단일 코어에서 초당 2,400만 패킷 처리 성능을 달성하며, L3 라우팅, DDoS 방어, L4 로드 밸런싱 사례를 제시한다.
- **중요성:** 커널 바이패스 없이 커널 내에서 고성능 패킷 처리를 가능하게 한 핵심 논문으로, eBPF 네트워킹의 기반이 되었다.

### 5.2 Fast Packet Processing with eBPF and XDP: Concepts, Code, Challenges, and Applications

- **저자:** Marcos A. M. Vieira, Matheus S. Castanho, Racyus D. G. Pacini, Elerson R. S. Santos, Eduardo P. M. Camera Junior, Luiz F. M. Vieira
- **연도:** 2020
- **학회:** ACM Computing Surveys, Vol. 53, No. 1
- **요약:** eBPF와 XDP의 개념, 코드, 도전과제, 응용을 포괄적으로 다루는 서베이 논문이다. eBPF 가상 머신의 동작 원리부터 실제 코드 예제, 성능 벤치마크, DDoS 방어/로드 밸런싱/모니터링 등 실용적 응용까지 체계적으로 정리했다.
- **중요성:** eBPF/XDP 기술의 가장 완성도 높은 서베이로, 해당 분야 입문과 심화 연구 모두에 필수적이다.

---

## 6. Service Mesh

### 6.1 Performance Comparison of Service Mesh Frameworks: the MTLS Test Case

- **저자:** (Technical Report, Deepness Lab)
- **연도:** 2024
- **학회:** arXiv (Technical Report)
- **요약:** Istio, Istio Ambient, Linkerd, Cilium 서비스 메시에서 mTLS 프로토콜이 애플리케이션 성능에 미치는 영향을 실증 비교한 논문이다. mTLS 적용 시 Istio는 166%, Cilium은 99%, Linkerd는 33%, Istio Ambient는 8%의 지연시간 증가를 보였다.
- **중요성:** 사이드카리스(sidecarless) 아키텍처와 eBPF 활용의 성능 이점을 정량적으로 입증한 중요한 벤치마크 연구이다.

### 6.2 Towards a Lightweight Sidecar-based Service Mesh for Serverless

- **저자:** Lazar Cvetkovic, et al. (ETH Zurich)
- **연도:** 2025
- **학회:** ACM SoCC 2025
- **요약:** 서버리스 환경에서 Istio 서비스 메시 정책 적용 시 워크로드 성능이 심각하게 저하되는 문제(p50/p95 호출 지연 각각 7배/38.3배 증가)를 분석하고, 경량 사이드카 기반 서비스 메시를 제안한다.
- **중요성:** 서버리스 환경에서의 서비스 메시 오버헤드 문제를 학술적으로 분석한 최초의 논문 중 하나이다.

---

## 7. Network Function Virtualization (NFV)

### 7.1 Network Function Virtualization: State-of-the-Art and Research Challenges

- **저자:** Rashid Mijumbi, Joan Serrat, Juan-Luis Gorricho, Niels Bouten, Filip De Turck, Raouf Boutaba
- **연도:** 2016
- **학회:** IEEE Communications Surveys & Tutorials, Vol. 18, No. 1
- **요약:** NFV의 핵심 개념인 네트워크 기능의 물리 장비로부터의 분리를 다루며, OPEX/CAPEX 감소, 새로운 서비스의 민첩한 배포 등의 장점과 연구 과제를 체계적으로 정리했다. NFV 아키텍처, 자원 할당, 서비스 체이닝을 포괄적으로 다룬다.
- **중요성:** NFV 분야의 가장 많이 인용된 서베이 논문으로, NFV 연구의 표준 참조 문헌이다.

### 7.2 A Comprehensive Survey of Network Function Virtualization

- **저자:** Bo Yi, Xingwei Wang, Keqin Li, Sajal K. Das, Min Huang
- **연도:** 2018
- **학회:** Computer Networks (Elsevier)
- **요약:** NFV 아키텍처를 물리 인프라, 가상화 계층, 가상 인프라, 관리/오케스트레이션 계층, VNF 계층의 bottom-up 방식으로 체계적으로 제시한다. NFV의 동기부터 표준화 동향, SDN과의 통합까지 전 영역을 다룬다.
- **중요성:** NFV 아키텍처의 각 계층을 체계적으로 분석한 완결성 높은 서베이이다.

---

## 8. Programmable Switches / P4

### 8.1 P4: Programming Protocol-Independent Packet Processors

- **저자:** Pat Bosshart, Dan Daly, Glen Gibb, Martin Izzard, Nick McKeown, Jennifer Rexford, Cole Schlesinger, Dan Talayco, Amin Vahdat, George Varghese, David Walker
- **연도:** 2014
- **학회:** ACM SIGCOMM Computer Communication Review, Vol. 44, No. 3
- **요약:** 프로토콜에 독립적인 패킷 프로세서 프로그래밍을 위한 고수준 언어 P4를 제안했다. 현장 재구성 가능성(reconfigurability), 프로토콜 독립성, 타겟 독립성의 세 가지 목표를 통해 SDN의 데이터 평면 프로그래밍을 혁신했다.
- **중요성:** 데이터 평면 프로그래밍의 표준 언어가 된 P4의 창시 논문으로, 프로그래머블 네트워킹의 새로운 시대를 열었다.

### 8.2 An Exhaustive Survey on P4 Programmable Data Plane Switches: Taxonomy, Applications, Challenges, and Future Trends

- **저자:** Jorge Crichigno, et al.
- **연도:** 2021
- **학회:** IEEE Access
- **요약:** 레거시 네트워크에서 프로그래머블 네트워크로의 진화를 기술하고, P4 언어로 개발된 200편 이상의 응용을 분류/분석한 포괄적 서베이이다. INT, 측정, 성능, 미들박스, IoT, 사이버보안, 네트워크 테스팅의 7개 카테고리로 분류했다.
- **중요성:** P4 생태계의 전체 지형을 파악할 수 있는 가장 완성도 높은 서베이 논문이다.

---

## 9. Network Telemetry

### 9.1 In-band Network Telemetry: A Survey

- **저자:** Liang Tan, Wei Su, Wei Zhang, Jing Lv, Zhenyi Zhang, Jiayu Miao, Xiaoxi Liu, Na Li
- **연도:** 2021
- **학회:** Computer Networks (Elsevier)
- **요약:** 인밴드 네트워크 텔레메트리(INT) 기술의 최초 서베이로, INT, IOAM, AM-PM, ANT 등 대표적 솔루션을 시스템, 구현 아이디어, 기술적 특성, 표준화, 연구 결과 측면에서 상세히 분석한다. 데이터 생성, 내보내기, 저장, 분석까지의 핵심 기술을 다룬다.
- **중요성:** 프로그래머블 데이터 평면 기반 실시간 네트워크 상태 감시 기술의 표준 참조 문헌이다.

---

## 10. RDMA / High-Performance Datacenter Networking

### 10.1 RDMA over Commodity Ethernet at Scale

- **저자:** Chuanxiong Guo, Haitao Wu, Zhong Deng, Gaurav Soni, Jianxi Ye, Jitu Padhye, Marina Lipshteyn
- **연도:** 2016
- **학회:** ACM SIGCOMM 2016
- **요약:** Microsoft가 RoCEv2(RDMA over Converged Ethernet v2)를 대규모로 도입한 경험을 기술한다. DSCP 기반 PFC 메커니즘을 설계하고, PFC 유발 데드락, RDMA 전송 라이브락, NIC PFC pause frame storm 등의 안전성 문제를 해결한 방법을 상세히 다룬다.
- **중요성:** 상용 이더넷에서의 RDMA 대규모 배포 경험을 최초로 공개한 논문으로, 클라우드 RDMA 도입의 표준 참조가 되었다.

### 10.2 Datacenter Ethernet and RDMA: Issues at Hyperscale

- **저자:** Torsten Hoefler, et al.
- **연도:** 2023
- **학회:** arXiv (Preprint)
- **요약:** 하이퍼스케일 데이터센터에서의 이더넷 및 RDMA 기술의 이슈를 종합적으로 분석한다. InfiniBand와 RoCE의 비교, PFC(Priority Flow Control)의 관리 복잡성, head-of-line blocking, 혼잡 확산, 데드락 등의 문제를 다룬다.
- **중요성:** AI/ML 워크로드 증가에 따른 RDMA 네트워킹의 최신 도전과제를 정리한 시의적절한 논문이다.

---

## 11. SmartNIC / DPU

### 11.1 Azure Accelerated Networking: SmartNICs in the Public Cloud

- **저자:** Daniel Firestone, Andrew Putnam, Sambhrama Mundkur, Derek Chiou, et al. (Microsoft)
- **연도:** 2018
- **학회:** USENIX NSDI 2018
- **요약:** FPGA 기반 맞춤형 Azure SmartNIC을 사용하여 호스트 네트워킹을 하드웨어로 오프로딩하는 AccelNet 솔루션을 기술한다. 2015년 이후 100만 대 이상의 서버에 배포되었으며, VM 간 TCP 지연시간 15us 미만, 32Gbps 처리량을 일관되게 달성한다.
- **중요성:** 퍼블릭 클라우드에서의 SmartNIC 대규모 배포를 최초로 공개한 획기적 논문이다.

### 11.2 A Comprehensive Survey on SmartNICs: Architectures, Development Models, Applications, and Research Directions

- **저자:** (Multiple authors, University of South Carolina)
- **연도:** 2024
- **학회:** IEEE Access / arXiv
- **요약:** SmartNIC의 아키텍처, 개발 환경, 레거시 NIC 대비 장점을 기술하고, 네트워크, 보안, 스토리지, 머신러닝 기능의 SmartNIC 오프로딩 응용을 분류/분석한 포괄적 서베이이다.
- **중요성:** SmartNIC/DPU 분야의 전체 지형을 체계적으로 정리한 최신 서베이로, 이 분야 연구의 출발점이 된다.

### 11.3 Characterizing Off-path SmartNIC for Accelerating Distributed Systems

- **저자:** Xingda Wei, et al.
- **연도:** 2023
- **학회:** USENIX OSDI 2023
- **요약:** Off-path SmartNIC(예: NVIDIA BlueField DPU)이 분산 시스템 가속에 미치는 영향을 체계적으로 분석한다. SmartNIC의 컴퓨팅 및 네트워킹 기능을 벤치마킹하고, 분산 트랜잭션 시스템에서의 오프로딩 전략을 제시한다.
- **중요성:** DPU를 분산 시스템에 실제 적용할 때의 성능 특성과 설계 지침을 제공하는 실용적 연구이다.

---

## 12. Virtual Switching / Overlay Networks

### 12.1 The Design and Implementation of Open vSwitch

- **저자:** Ben Pfaff, Justin Pettit, Teemu Koponen, E. J. Jackson, Andy Zhou, Jarno Rajahalme, Jesse Gross, Alex Wang, Jonathan Stringer, Pravin Shelar, Keith Amidon, Martin Casado
- **연도:** 2015
- **학회:** USENIX NSDI 2015 (Best Paper)
- **요약:** 모든 주요 하이퍼바이저 플랫폼을 지원하는 오픈소스 다계층 가상 스위치 Open vSwitch의 설계와 구현을 기술한다. 가상화 환경에 최적화된 플로우 캐싱 메커니즘이 핵심이며, 패킷 분류기를 포함한 전체 설계에 광범위한 영향을 미친다.
- **중요성:** NSDI 2015 Best Paper 수상작으로, 현대 네트워크 가상화의 핵심 인프라인 OVS의 설계 원리를 정립한 논문이다.

### 12.2 VXLAN: A Framework for Overlaying Virtualized Layer 2 Networks over Layer 3 Networks (RFC 7348)

- **저자:** Mahalingam, Dutt, et al.
- **연도:** 2014
- **학회:** IETF RFC 7348
- **요약:** 가상화된 L2 네트워크를 L3 네트워크 위에 오버레이하기 위한 VXLAN 프로토콜을 정의하는 표준 문서이다. 멀티테넌트 데이터센터 환경에서 네트워크 세그먼트를 확장하고 유연한 VM 배치를 가능하게 한다.
- **중요성:** 현대 데이터센터 오버레이 네트워크의 사실상 표준 프로토콜을 정의한 핵심 RFC이다.

---

## 13. Container Networking (CNI)

### 13.1 A Comprehensive Performance Evaluation of Container Network Interface Plugins

- **저자:** (Vanderbilt University)
- **연도:** 2021
- **학회:** IEEE IC2E 2021
- **요약:** Cilium, Calico, WeaveNet, Kube-router, Antrea 등 주요 CNI 플러그인을 Kubernetes 환경에서 심층 분석한다. iptables 기반 CNI는 200개 Pod에서 60-70% 처리량 감소를 보이는 반면, Cilium은 10% 이내의 성능 유지를 달성한다.
- **중요성:** Kubernetes CNI 플러그인 선택에 필요한 정량적 성능 데이터를 최초로 체계적으로 제공한 연구이다.

### 13.2 Performance Evaluation of Kubernetes Networking Approaches across Constraint Edge Environments

- **저자:** (Multiple authors)
- **연도:** 2024
- **학회:** arXiv
- **요약:** 엣지 환경을 포함한 다양한 Kubernetes 환경에서 CNI 플러그인(Flannel, Calico, Cilium, Antrea, Kube-router)의 정성적/정량적 성능을 평가한다. 호스트 간 데이터 교환에서 Kube-Router가 TCP 전송 시 공칭 링크 대역폭의 90% 이상을 달성했다.
- **중요성:** 엣지 컴퓨팅 환경에서의 컨테이너 네트워킹 성능을 실증적으로 분석한 최신 연구이다.

---

## 14. Microsecond-Scale Networking / Kernel Bypass

### 14.1 The Demikernel Datapath OS Architecture for Microsecond-Scale Datacenter Systems

- **저자:** Irene Zhang, et al.
- **연도:** 2021
- **학회:** ACM SOSP 2021
- **요약:** 이종 커널 바이패스 디바이스를 위한 유연한 데이터패스 OS인 Demikernel을 제안한다. I/O당 나노초 수준의 오버헤드를 달성하며, eRPC, Shenango, Caladan에 근접한 지연시간을 보이면서도 더 넓은 범위의 디바이스와 프로토콜을 지원한다.
- **중요성:** 마이크로초 규모 시스템을 위한 범용 데이터패스 OS 추상화를 제시하여, 커널 바이패스 기술의 단편화 문제를 해결한 논문이다.

### 14.2 Shenango: Achieving High CPU Efficiency for Latency-sensitive Datacenter Workloads

- **저자:** Amy Ousterhout, Joshua Fried, Jonathan Behrens, Adam Belay, Hari Balakrishnan
- **연도:** 2019
- **학회:** USENIX NSDI 2019
- **요약:** 5us 단위로 코어를 애플리케이션 간 재할당하여, 지연시간 민감 애플리케이션의 미사용 사이클을 배치 처리 애플리케이션이 생산적으로 활용할 수 있게 한다. ZygOS 수준의 테일 지연시간과 처리량을 달성하면서 CPU 효율을 대폭 향상시킨다.
- **중요성:** 마이크로초 스케일 코어 재할당을 통해 데이터센터 CPU 효율과 지연시간을 동시에 최적화한 혁신적 시스템이다.

### 14.3 Caladan: Mitigating Interference at Microsecond Timescales

- **저자:** Joshua Fried, Zhenyuan Ruan, Amy Ousterhout, Adam Belay
- **연도:** 2020
- **학회:** USENIX OSDI 2020
- **요약:** 중앙 집중식 스케줄러 코어가 메모리 계층과 하이퍼스레드 간 자원 경합을 능동적으로 관리하는 시스템이다. memcached와 GC 워크로드 공존 시, 기존 최고 시스템 대비 테일 지연시간을 580ms에서 52us로 11,000배 개선한다.
- **중요성:** 마이크로초 스케일에서의 간섭 완화 문제를 해결하여, 데이터센터 워크로드 공존의 새로운 기준을 제시했다.

---

## 15. Zero Trust Networking

### 15.1 BeyondCorp: A New Approach to Enterprise Security

- **저자:** Rory Ward, Betsy Beyer (Google)
- **연도:** 2014
- **학회:** ;login: (USENIX)
- **요약:** 2009년 고도의 APT 공격을 겪은 Google이 기존 경계 기반 보안 모델을 폐기하고 개발한 제로 트러스트 네트워크 아키텍처이다. 내부 네트워크를 특별히 신뢰하지 않으며, 사용자와 디바이스 기반의 접근 제어를 서비스 단위로 적용한다.
- **중요성:** 제로 트러스트 네트워킹의 실제 대규모 구현 사례를 최초로 공개한 논문으로, 업계 전반의 보안 아키텍처 변화를 이끌었다.

### 15.2 BeyondCorp and the Long Tail of Zero Trust

- **저자:** Jeff Peck, Hunter King, Betsy Beyer (Google)
- **연도:** 2020
- **학회:** ;login: (USENIX) / Google Research
- **요약:** BeyondCorp의 초기 구현 이후 발생한 "긴 꼬리" 문제들, 즉 레거시 시스템 마이그레이션, 예외 처리, 비표준 워크로드 지원 등의 실질적 과제와 해결 방안을 기술한다. 제로 트러스트의 이상과 현실 사이의 간극을 솔직하게 다룬다.
- **중요성:** 제로 트러스트 아키텍처의 실제 운영 경험과 교훈을 공유하여, 다른 조직의 도입에 실질적 지침을 제공한다.

### 15.3 A Survey on Zero Trust Architecture: Challenges and Future Trends

- **저자:** Shuangmao He, et al.
- **연도:** 2022
- **학회:** Wireless Communications and Mobile Computing (Wiley)
- **요약:** 제로 트러스트 아키텍처(ZTA)의 핵심 원칙, 구현 기술, 도전과제, 미래 동향을 체계적으로 조사한 서베이이다. NIST ZTA 프레임워크, 마이크로세그먼테이션, 지속적 인증 등의 핵심 개념을 포괄적으로 다룬다.
- **중요성:** 제로 트러스트 분야의 학술적 서베이로, 연구 동향과 미래 방향을 파악하는 데 유용하다.

---

## 분류별 요약

| 분류 | 논문 수 | 핵심 키워드 |
|------|---------|------------|
| SDN | 3 | OpenFlow, 제어/데이터 평면 분리, 프로그래머블 네트워크 |
| Datacenter Architecture | 3 | Clos, Jupiter, VL2, Andromeda, 하이퍼스케일 |
| Load Balancing | 2 | Maglev, Ananta, ECMP, consistent hashing |
| TCP Congestion Control | 3 | DCTCP, pFabric, HPCC, ECN, INT |
| eBPF/XDP | 2 | 커널 내 패킷 처리, 프로그래머블 데이터 평면 |
| Service Mesh | 2 | Istio, Linkerd, Cilium, sidecar, mTLS |
| NFV | 2 | 네트워크 기능 가상화, VNF, 서비스 체이닝 |
| P4/Programmable Switches | 2 | 프로토콜 독립 패킷 처리, 데이터 평면 프로그래밍 |
| Network Telemetry | 1 | In-band INT, 실시간 네트워크 모니터링 |
| RDMA | 2 | RoCE, InfiniBand, PFC, 초저지연 |
| SmartNIC/DPU | 3 | FPGA, 하드웨어 오프로딩, AccelNet |
| Virtual Switching/Overlay | 2 | OVS, VXLAN, 네트워크 가상화 |
| Container Networking | 2 | CNI, Kubernetes, Cilium, Calico |
| Microsecond Networking | 3 | 커널 바이패스, Demikernel, Shenango, Caladan |
| Zero Trust | 3 | BeyondCorp, 경계 없는 보안, ZTA |

---

**총 논문 수: 33편**

> 이 목록은 분산 네트워킹 분야의 핵심 논문들을 15개 하위 카테고리로 분류하여 정리한 것이다. 각 논문은 해당 분야에서 근본적인 기여를 한 것으로 인정받고 있으며, 현대 클라우드 및 데이터센터 네트워킹 아키텍처의 이론적/실무적 기반을 형성한다.
