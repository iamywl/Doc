# 네트워킹, 서비스 메시 & eBPF - 논문 요약 및 링크

---

## 1. Software-Defined Networking (SDN)

### [OpenFlow: Enabling Innovation in Campus Networks](https://dl.acm.org/doi/10.1145/1355734.1355746)
- **저자**: Nick McKeown, Tom Anderson, Hari Balakrishnan, Guru Parulkar, Larry Peterson, Jennifer Rexford, Scott Shenker, Jonathan Turner
- **연도/학회**: 2008 / ACM SIGCOMM Computer Communication Review
- **핵심 요약**: 이더넷 스위치의 플로우 테이블과 표준화된 인터페이스를 통해 연구자들이 실제 네트워크에서 실험적 프로토콜을 실행할 수 있도록 제안한 SDN 창시 논문이다.

### [Software-Defined Networking: A Comprehensive Survey](https://ieeexplore.ieee.org/document/6994333/)
- **저자**: Diego Kreutz, Fernando M. V. Ramos, Paulo Esteves Verissimo, Christian Esteve Rothenberg, Siamak Azodolmolky, Steve Uhlig
- **연도/학회**: 2015 / Proceedings of the IEEE, Vol. 103, No. 1
- **핵심 요약**: SDN의 역사적 발전, 제어/데이터 평면 기술, 응용 분야 및 보안 이슈를 체계적으로 정리한 가장 포괄적인 SDN 서베이 논문이다.

### [A Survey of Software-Defined Networking: Past, Present, and Future of Programmable Networks](https://ieeexplore.ieee.org/document/6739370/)
- **저자**: Bruno Nunes Astuto, Marc Mendonca, Xuan Nam Nguyen, Katia Obraczka, Thierry Turletti
- **연도/학회**: 2014 / IEEE Communications Surveys & Tutorials
- **핵심 요약**: 프로그래머블 네트워크의 초기 아이디어부터 현대 SDN까지의 역사적 진화를 추적하며, SDN의 맥락과 미래 연구 방향을 제시하는 서베이이다.

---

## 2. Datacenter Network Architecture

### [Jupiter Rising: A Decade of Clos Topologies and Centralized Control in Google's Datacenter Network](https://dl.acm.org/doi/10.1145/2785956.2787508)
- **저자**: Arjun Singh, Joon Ong, Amit Agarwal, Glen Anderson, Ashby Armistead, et al. (Google)
- **연도/학회**: 2015 / ACM SIGCOMM 2015
- **핵심 요약**: Google의 5세대 데이터센터 네트워크 아키텍처 진화를 기술하며, 1.3 Pbps 이등분 대역폭을 지원하는 Clos 토폴로지와 중앙 집중식 제어의 효과를 실증했다.

### [VL2: A Scalable and Flexible Data Center Network](https://dl.acm.org/doi/10.1145/1592568.1592576)
- **저자**: Albert Greenberg, James R. Hamilton, Navendu Jain, Srikanth Kandula, Changhoon Kim, Parantap Lahiri, David A. Maltz, Parveen Patel, Sudipta Sengupta
- **연도/학회**: 2009 / ACM SIGCOMM 2009
- **핵심 요약**: Flat addressing, Valiant Load Balancing, 엔드시스템 기반 주소 해석을 사용하여 대규모 데이터센터에서 균일한 고용량 연결과 성능 격리를 제공하는 네트워크 아키텍처를 제안했다.

### [Andromeda: Performance, Isolation, and Velocity at Scale in Cloud Network Virtualization](https://www.usenix.org/conference/nsdi18/presentation/dalton)
- **저자**: Michael Dalton, David Schultz, Jacob Adriaens, et al. (Google)
- **연도/학회**: 2018 / USENIX NSDI 2018
- **핵심 요약**: Google Cloud Platform의 네트워크 가상화 스택 Andromeda의 설계를 기술하며, 성능 격리와 확장성, 하드웨어 수준에 가까운 대역폭/지연시간을 달성하는 플로우 처리 계층 구조를 제안했다.

---

## 3. Load Balancing

### [Maglev: A Fast and Reliable Software Network Load Balancer](https://www.usenix.org/conference/nsdi16/technical-sessions/presentation/eisenbud)
- **저자**: Daniel E. Eisenbud, Cheng Yi, Carlo Contavalli, Cody Smith, Roman Kononov, et al.
- **연도/학회**: 2016 / USENIX NSDI 2016
- **핵심 요약**: ECMP 기반 패킷 분산과 consistent hashing 연결 추적을 사용하는 Google의 소프트웨어 로드 밸런서로, 단일 머신이 10Gbps 링크를 포화시킬 수 있으며 2008년부터 Google 트래픽을 처리해왔다.

### [Ananta: Cloud Scale Load Balancing](https://dl.acm.org/doi/10.1145/2486001.2486026)
- **저자**: Parveen Patel, Deepak Bansal, Lihua Yuan, Ashwin Murthy, Albert Greenberg, David A. Maltz, et al.
- **연도/학회**: 2013 / ACM SIGCOMM 2013
- **핵심 요약**: Microsoft Azure의 분산 소프트웨어 로드 밸런서로, 합의 기반 제어 평면과 분산 스케일아웃 데이터 평면으로 1Tbps 이상의 통합 대역폭을 처리하는 클라우드 규모 로드 밸런싱 아키텍처를 제시했다.

---

## 4. TCP Congestion Control (Datacenter)

### [Data Center TCP (DCTCP)](https://dl.acm.org/doi/10.1145/1851182.1851192)
- **저자**: Mohammad Alizadeh, Albert Greenberg, David A. Maltz, Jitendra Padhye, Parveen Patel, Balaji Prabhakar, Sudipta Sengupta, Murari Sridharan
- **연도/학회**: 2010 / ACM SIGCOMM 2010
- **핵심 요약**: ECN을 활용하여 일반 TCP 대비 90% 적은 버퍼로 동등 이상의 처리량을 달성하는 데이터센터 전용 혼잡 제어 알고리즘으로, SIGCOMM Test of Time 수상작이다.

### [pFabric: Minimal Near-Optimal Datacenter Transport](https://dl.acm.org/doi/10.1145/2486001.2486031)
- **저자**: Mohammad Alizadeh, Shuang Yang, Milad Sharif, Sachin Katti, Nick McKeown, Balaji Prabhakar, Scott Shenker
- **연도/학회**: 2013 / ACM SIGCOMM 2013
- **핵심 요약**: 플로우 스케줄링과 레이트 제어를 분리하여, 99퍼센타일에서도 이론적 최적에 가까운 플로우 완료 시간을 달성하는 미니멀리스틱 데이터센터 전송 설계를 제안했다.

### [HPCC: High Precision Congestion Control](https://dl.acm.org/doi/10.1145/3341302.3342085)
- **저자**: Yuliang Li, Rui Miao, Hongqiang Harry Liu, Yan Zhuang, Fei Feng, Lingbo Tang, Zheng Cao, Ming Zhang, Frank Kelly, Mohammad Alizadeh, Minlan Yu
- **연도/학회**: 2019 / ACM SIGCOMM 2019
- **핵심 요약**: In-network Telemetry(INT)를 활용하여 정밀한 링크 부하 정보를 수집하고, DCQCN/TIMELY 대비 플로우 완료 시간을 최대 95% 단축하며 거의 0에 가까운 큐를 유지하는 혼잡 제어 알고리즘이다.

---

## 5. eBPF / XDP

### [The eXpress Data Path: Fast Programmable Packet Processing in the Operating System Kernel](https://dl.acm.org/doi/10.1145/3281411.3281443)
- **저자**: Toke Hoiland-Jorgensen, Jesper Dangaard Brouer, Daniel Borkmann, John Fastabend, Tom Herbert, David Ahern, David Miller
- **연도/학회**: 2018 / ACM CoNEXT 2018
- **핵심 요약**: 리눅스 커널 내 NIC 인접 지점에서 eBPF를 통해 단일 코어 초당 2,400만 패킷 처리 성능을 달성하는 XDP를 기술하며, 커널 바이패스 없이 고성능 패킷 처리를 가능하게 했다.

### [Fast Packet Processing with eBPF and XDP: Concepts, Code, Challenges, and Applications](https://dl.acm.org/doi/10.1145/3371038)
- **저자**: Marcos A. M. Vieira, Matheus S. Castanho, Racyus D. G. Pacini, Elerson R. S. Santos, Eduardo P. M. Camera Junior, Luiz F. M. Vieira
- **연도/학회**: 2020 / ACM Computing Surveys, Vol. 53, No. 1
- **핵심 요약**: eBPF 가상 머신의 동작 원리부터 실제 코드 예제, 성능 벤치마크, DDoS 방어/로드 밸런싱/모니터링 등 실용적 응용까지 체계적으로 정리한 가장 완성도 높은 eBPF/XDP 서베이이다.

---

## 6. Service Mesh

### [Performance Comparison of Service Mesh Frameworks: the MTLS Test Case](https://arxiv.org/abs/2411.02267)
- **저자**: Anat Bremler Barr et al. (Deepness Lab)
- **연도/학회**: 2024 / arXiv (Technical Report)
- **핵심 요약**: Istio, Istio Ambient, Linkerd, Cilium에서 mTLS 적용 시 지연시간 증가를 실증 비교하여(Istio 166%, Cilium 99%, Linkerd 33%, Istio Ambient 8%), 사이드카리스 아키텍처와 eBPF의 성능 이점을 정량적으로 입증했다.

### [Towards a Lightweight Sidecar-based Service Mesh for Serverless](https://dl.acm.org/doi/10.1145/3772052.3772210)
- **저자**: Lazar Cvetkovic, et al. (ETH Zurich)
- **연도/학회**: 2025 / ACM SoCC 2025
- **핵심 요약**: 서버리스 환경에서 Istio 서비스 메시 정책 적용 시 p95 지연이 38.3배 증가하는 문제를 분석하고, 경량 사이드카 기반 서비스 메시를 제안하여 서버리스 환경의 오버헤드 문제를 해결한다.

---

## 7. Network Function Virtualization (NFV)

### [Network Function Virtualization: State-of-the-Art and Research Challenges](https://ieeexplore.ieee.org/document/7243304/)
- **저자**: Rashid Mijumbi, Joan Serrat, Juan-Luis Gorricho, Niels Bouten, Filip De Turck, Raouf Boutaba
- **연도/학회**: 2016 / IEEE Communications Surveys & Tutorials, Vol. 18, No. 1
- **핵심 요약**: NFV의 핵심 개념, SDN/클라우드 컴퓨팅과의 관계, 자원 할당, 서비스 체이닝을 포괄적으로 다룬 NFV 분야의 가장 많이 인용된 표준 참조 서베이이다.

### [A Comprehensive Survey of Network Function Virtualization](https://www.sciencedirect.com/science/article/abs/pii/S1389128618300306)
- **저자**: Bo Yi, Xingwei Wang, Keqin Li, Sajal K. Das, Min Huang
- **연도/학회**: 2018 / Computer Networks (Elsevier)
- **핵심 요약**: NFV 아키텍처를 물리 인프라부터 VNF 계층까지 bottom-up 방식으로 체계적으로 제시하고, VNF 배치/스케줄링/마이그레이션/체이닝 알고리즘을 광범위하게 다룬 서베이이다.

---

## 8. Programmable Switches / P4

### [P4: Programming Protocol-Independent Packet Processors](https://dl.acm.org/doi/10.1145/2656877.2656890)
- **저자**: Pat Bosshart, Dan Daly, Glen Gibb, Martin Izzard, Nick McKeown, Jennifer Rexford, Cole Schlesinger, Dan Talayco, Amin Vahdat, George Varghese, David Walker
- **연도/학회**: 2014 / ACM SIGCOMM Computer Communication Review, Vol. 44, No. 3
- **핵심 요약**: 프로토콜 독립적 패킷 프로세서 프로그래밍을 위한 고수준 언어 P4를 제안하여, 현장 재구성 가능성과 프로토콜/타겟 독립성을 통해 데이터 평면 프로그래밍을 혁신한 창시 논문이다.

### [An Exhaustive Survey on P4 Programmable Data Plane Switches: Taxonomy, Applications, Challenges, and Future Trends](https://ieeexplore.ieee.org/document/9447791/)
- **저자**: Elie F. Kfoury, Jorge Crichigno, Elias Bou-Harb
- **연도/학회**: 2021 / IEEE Access
- **핵심 요약**: P4 언어로 개발된 200편 이상의 응용을 INT, 측정, 성능, 미들박스, IoT, 사이버보안, 네트워크 테스팅의 7개 카테고리로 분류하여 P4 생태계 전체를 조망한 포괄적 서베이이다.

---

## 9. Network Telemetry

### [In-band Network Telemetry: A Survey](https://www.sciencedirect.com/science/article/abs/pii/S1389128620313396)
- **저자**: Lizhuang Tan, Wei Su, Wei Zhang, Jianhui Lv, Zhenyi Zhang, Jingying Miao, Xiaoxi Liu, Na Li
- **연도/학회**: 2021 / Computer Networks (Elsevier)
- **핵심 요약**: INT, IOAM, AM-PM, ANT 등 인밴드 네트워크 텔레메트리 대표 솔루션의 시스템, 구현, 기술적 특성, 표준화를 상세 분석한 최초의 INT 서베이이다.

---

## 10. RDMA / High-Performance Datacenter Networking

### [RDMA over Commodity Ethernet at Scale](https://dl.acm.org/doi/10.1145/2934872.2934908)
- **저자**: Chuanxiong Guo, Haitao Wu, Zhong Deng, Gaurav Soni, Jianxi Ye, Jitu Padhye, Marina Lipshteyn
- **연도/학회**: 2016 / ACM SIGCOMM 2016
- **핵심 요약**: Microsoft가 RoCEv2를 대규모로 도입한 경험을 기술하며, DSCP 기반 PFC, 데드락/라이브락 해결 등 상용 이더넷에서의 RDMA 대규모 배포 방법론을 최초로 공개했다.

### [Datacenter Ethernet and RDMA: Issues at Hyperscale](https://arxiv.org/abs/2302.03337)
- **저자**: Torsten Hoefler, et al.
- **연도/학회**: 2023 / arXiv (Preprint)
- **핵심 요약**: 하이퍼스케일 데이터센터에서 InfiniBand와 RoCE를 비교하고, PFC 관리 복잡성, head-of-line blocking, 혼잡 확산, 데드락 등 AI/ML 워크로드 증가에 따른 RDMA 네트워킹의 최신 도전과제를 종합 분석했다.

---

## 11. SmartNIC / DPU

### [Azure Accelerated Networking: SmartNICs in the Public Cloud](https://www.usenix.org/conference/nsdi18/presentation/firestone)
- **저자**: Daniel Firestone, Andrew Putnam, Sambhrama Mundkur, Derek Chiou, et al. (Microsoft)
- **연도/학회**: 2018 / USENIX NSDI 2018
- **핵심 요약**: FPGA 기반 Azure SmartNIC으로 호스트 네트워킹을 오프로딩하여 100만 대 이상 서버에 배포하고, VM 간 TCP 지연시간 15us 미만, 32Gbps 처리량을 달성한 퍼블릭 클라우드 SmartNIC 대규모 배포 사례이다.

### [A Comprehensive Survey on SmartNICs: Architectures, Development Models, Applications, and Research Directions](https://arxiv.org/abs/2405.09499)
- **저자**: Elie F. Kfoury, et al. (University of South Carolina)
- **연도/학회**: 2024 / IEEE Access
- **핵심 요약**: SmartNIC의 아키텍처, 개발 환경, 네트워크/보안/스토리지/머신러닝 오프로딩 응용을 분류하고 레거시 NIC 대비 장점을 체계적으로 정리한 최신 포괄적 서베이이다.

### [Characterizing Off-path SmartNIC for Accelerating Distributed Systems](https://www.usenix.org/conference/osdi23/presentation/wei-smartnic)
- **저자**: Xingda Wei, Rongxin Cheng, Yuhan Yang, Rong Chen, Haibo Chen
- **연도/학회**: 2023 / USENIX OSDI 2023
- **핵심 요약**: NVIDIA BlueField-2 DPU의 통신 경로별 성능 특성을 체계적으로 벤치마킹하고, 분산 파일 시스템과 KV 스토어에서 최대 30% 성능 향상을 보이는 오프로딩 전략을 제시했다.

---

## 12. Virtual Switching / Overlay Networks

### [The Design and Implementation of Open vSwitch](https://www.usenix.org/conference/nsdi15/technical-sessions/presentation/pfaff)
- **저자**: Ben Pfaff, Justin Pettit, Teemu Koponen, E. J. Jackson, Andy Zhou, Jarno Rajahalme, Jesse Gross, Alex Wang, Jonathan Stringer, Pravin Shelar, Keith Amidon, Martin Casado
- **연도/학회**: 2015 / USENIX NSDI 2015 (Best Paper)
- **핵심 요약**: 모든 주요 하이퍼바이저 플랫폼을 지원하는 오픈소스 다계층 가상 스위치 OVS의 설계와 구현을 기술하며, 가상화 환경에 최적화된 플로우 캐싱 메커니즘이 핵심이다.

### [VXLAN: A Framework for Overlaying Virtualized Layer 2 Networks over Layer 3 Networks (RFC 7348)](https://datatracker.ietf.org/doc/rfc7348/)
- **저자**: M. Mahalingam, D. Dutt, et al.
- **연도/학회**: 2014 / IETF RFC 7348
- **핵심 요약**: 멀티테넌트 데이터센터 환경에서 가상화된 L2 네트워크를 L3 네트워크 위에 오버레이하기 위한 VXLAN 프로토콜을 정의하는 현대 데이터센터 오버레이 네트워크의 사실상 표준 RFC이다.

---

## 13. Container Networking (CNI)

### [Assessing Container Network Interface Plugins: Functionality, Performance, and Scalability](https://ieeexplore.ieee.org/document/9309003/)
- **저자**: Shixiong Qi, Sameer G. Kulkarni, K. K. Ramakrishnan
- **연도/학회**: 2021 / IEEE Transactions on Network and Service Management
- **핵심 요약**: Cilium, Calico, WeaveNet, Kube-router, Antrea 등 주요 CNI 플러그인을 Kubernetes 환경에서 정성적/정량적으로 비교하여, CNI 선택에 필요한 체계적 성능 데이터를 제공한 연구이다.

### [Performance Evaluation of Kubernetes Networking Approaches across Constraint Edge Environments](https://arxiv.org/abs/2401.07674)
- **저자**: Multiple authors
- **연도/학회**: 2024 / arXiv
- **핵심 요약**: Flannel, Calico, Cilium, Antrea, Kube-router를 포함한 CNI 플러그인의 성능을 엣지 환경(K3s, K0s, MicroK8s)에서 실증 평가하여, 경량 K8s 배포의 네트워킹 성능을 분석한 최신 연구이다.

---

## 14. Microsecond-Scale Networking / Kernel Bypass

### [The Demikernel Datapath OS Architecture for Microsecond-Scale Datacenter Systems](https://dl.acm.org/doi/10.1145/3477132.3483569)
- **저자**: Irene Zhang, et al.
- **연도/학회**: 2021 / ACM SOSP 2021
- **핵심 요약**: 이종 커널 바이패스 디바이스를 위한 유연한 데이터패스 OS로, I/O당 나노초 수준의 오버헤드를 달성하면서 다양한 디바이스와 프로토콜을 지원하여 커널 바이패스 기술의 단편화 문제를 해결했다.

### [Shenango: Achieving High CPU Efficiency for Latency-sensitive Datacenter Workloads](https://www.usenix.org/conference/nsdi19/presentation/ousterhout)
- **저자**: Amy Ousterhout, Joshua Fried, Jonathan Behrens, Adam Belay, Hari Balakrishnan
- **연도/학회**: 2019 / USENIX NSDI 2019
- **핵심 요약**: 5us 단위 코어 재할당으로 지연시간 민감 애플리케이션의 미사용 사이클을 배치 처리가 활용하게 하여, ZygOS 수준 성능을 유지하면서 CPU 효율을 대폭 향상시킨 시스템이다.

### [Caladan: Mitigating Interference at Microsecond Timescales](https://www.usenix.org/conference/osdi20/presentation/fried)
- **저자**: Joshua Fried, Zhenyuan Ruan, Amy Ousterhout, Adam Belay
- **연도/학회**: 2020 / USENIX OSDI 2020
- **핵심 요약**: 중앙 집중식 스케줄러가 메모리 계층과 하이퍼스레드 간 자원 경합을 관리하여, memcached+GC 워크로드 공존 시 테일 지연시간을 580ms에서 52us로 11,000배 개선한 마이크로초 스케일 간섭 완화 시스템이다.

---

## 15. Zero Trust Networking

### [BeyondCorp: A New Approach to Enterprise Security](https://www.usenix.org/publications/login/dec14/ward)
- **저자**: Rory Ward, Betsy Beyer (Google)
- **연도/학회**: 2014 / ;login: (USENIX)
- **핵심 요약**: Google이 APT 공격 이후 경계 기반 보안 모델을 폐기하고 개발한 제로 트러스트 네트워크 아키텍처로, 사용자와 디바이스 기반의 서비스 단위 접근 제어를 실현한 최초의 대규모 구현 사례이다.

### [BeyondCorp and the Long Tail of Zero Trust](https://www.usenix.org/publications/loginonline/beyondcorp-and-long-tail-zero-trust)
- **저자**: Jeff Peck, Hunter King, Betsy Beyer (Google)
- **연도/학회**: 2020 / ;login: (USENIX) / Google Research
- **핵심 요약**: BeyondCorp 초기 구현 이후 레거시 시스템 마이그레이션, 예외 처리, 비표준 워크로드 지원 등 제로 트러스트의 이상과 현실 간 "긴 꼬리" 문제들의 해결 방안을 기술한다.

### [A Survey on Zero Trust Architecture: Challenges and Future Trends](https://onlinelibrary.wiley.com/doi/10.1155/2022/6476274)
- **저자**: Yuanhang He, et al.
- **연도/학회**: 2022 / Wireless Communications and Mobile Computing (Wiley)
- **핵심 요약**: NIST ZTA 프레임워크, 마이크로세그먼테이션, 지속적 인증 등 제로 트러스트 아키텍처의 핵심 원칙, 구현 기술, 도전과제, 미래 동향을 체계적으로 조사한 학술 서베이이다.
