# Firecracker 논문 관련 연구 서베이

> **기준 논문:** Agache, A. et al. "Firecracker: Lightweight Virtualization for Serverless Applications," NSDI 2020.

Firecracker는 AWS에서 개발한 경량 VMM(Virtual Machine Monitor)으로, Linux KVM 위에 Rust로 작성된 최소한의 VMM을 통해 MicroVM을 생성한다. 메모리 오버헤드 <5MB, 부팅 시간 <125ms를 달성하며, AWS Lambda와 Fargate에서 수백만 워크로드를 처리하고 있다. 핵심 철학은 **하드웨어 가상화의 강력한 격리**와 **컨테이너 수준의 경량성**을 동시에 달성하는 것이다.

이 문서는 Firecracker와 **유사한 접근**의 논문, **반대/대안적 접근**의 논문, **확장 연구** 논문, 그리고 **컨테이너 런타임 및 클러스터 오케스트레이션** 논문을 종합적으로 정리한다. 총 **120편 이상**의 논문을 다룬다.

---

## 목차

1. [유사한 접근의 논문](#1-유사한-접근의-논문)
   - [1.1 경량 VMM / MicroVM](#11-경량-vmm--microvm)
   - [1.2 샌드박스 기반 격리](#12-샌드박스-기반-격리)
   - [1.3 유니커널 (Unikernel)](#13-유니커널-unikernel)
   - [1.4 서버리스 콜드 스타트 최적화](#14-서버리스-콜드-스타트-최적화)
   - [1.5 스냅샷/복원 기반 빠른 시작](#15-스냅샷복원-기반-빠른-시작)
   - [1.6 하드웨어 기반 기밀 컴퓨팅](#16-하드웨어-기반-기밀-컴퓨팅)
   - [1.7 Rust 기반 시스템 소프트웨어](#17-rust-기반-시스템-소프트웨어)
   - [1.8 컨테이너/VM 최적화](#18-컨테이너vm-최적화)
   - [1.9 컨테이너 이미지 최적화](#19-컨테이너-이미지-최적화)
2. [반대/대안적 접근의 논문](#2-반대대안적-접근의-논문)
   - [2.1 컨테이너 격리만으로 충분하다](#21-컨테이너-격리만으로-충분하다)
   - [2.2 언어 수준 격리 (WebAssembly / V8 Isolates)](#22-언어-수준-격리-webassembly--v8-isolates)
   - [2.3 Library OS / OS 리팩토링](#23-library-os--os-리팩토링)
   - [2.4 순수 소프트웨어 격리 (SFI / 하드웨어 확장)](#24-순수-소프트웨어-격리-sfi--하드웨어-확장)
   - [2.5 서버리스 모델 자체에 대한 비판](#25-서버리스-모델-자체에-대한-비판)
   - [2.6 엣지/IoT 환경의 대안](#26-엣지iot-환경의-대안)
3. [확장 연구](#3-확장-연구)
   - [3.1 서버리스 스케줄링/오케스트레이션](#31-서버리스-스케줄링오케스트레이션)
   - [3.2 서버리스 워크플로/함수 합성](#32-서버리스-워크플로함수-합성)
   - [3.3 서버리스 상태 관리](#33-서버리스-상태-관리)
   - [3.4 서버리스 보안](#34-서버리스-보안)
   - [3.5 GPU/가속기 서버리스](#35-gpu가속기-서버리스)
   - [3.6 메모리 중복 제거/공유](#36-메모리-중복-제거공유)
   - [3.7 보안 컨테이너 (Kata Containers 등)](#37-보안-컨테이너-kata-containers-등)
   - [3.8 eBPF 기반 보안/격리](#38-ebpf-기반-보안격리)
   - [3.9 서버리스 네트워킹/NFV](#39-서버리스-네트워킹nfv)
   - [3.10 마이크로커널/하이퍼바이저 비교](#310-마이크로커널하이퍼바이저-비교)
4. [컨테이너 런타임 및 클러스터 오케스트레이션](#4-컨테이너-런타임-및-클러스터-오케스트레이션)
   - [4.1 클러스터 관리 시스템 (Borg, Omega, Mesos, YARN, Twine)](#41-클러스터-관리-시스템-borg-omega-mesos-yarn-twine)
   - [4.2 클러스터 스케줄링 알고리즘](#42-클러스터-스케줄링-알고리즘)
   - [4.3 Kubernetes 연구](#43-kubernetes-연구)
   - [4.4 오토스케일링](#44-오토스케일링)
   - [4.5 컨테이너 vs VM 비교 연구](#45-컨테이너-vs-vm-비교-연구)
   - [4.6 컨테이너 설계 패턴 및 역사](#46-컨테이너-설계-패턴-및-역사)
   - [4.7 자원 관리 및 성능 격리](#47-자원-관리-및-성능-격리)
   - [4.8 마이크로서비스 관측/디버깅](#48-마이크로서비스-관측디버깅)
   - [4.9 컨테이너 마이그레이션](#49-컨테이너-마이그레이션)
   - [4.10 멀티 클러스터/페더레이션](#410-멀티-클러스터페더레이션)
   - [4.11 서비스 메시](#411-서비스-메시)
5. [비교 요약 테이블](#5-비교-요약-테이블)
6. [참고문헌](#6-참고문헌)

---

## 1. 유사한 접근의 논문

### 1.1 경량 VMM / MicroVM

#### LightVM: My VM is Lighter (and Safer) than your Container

- **저자:** Filipe Manco, Costin Lupu, Florian Schmidt, Jose Mendes, Simon Kuenzer, Sumit Sati, Kenichi Yasukata, Costin Raiciu, Felipe Huici
- **학회:** SOSP 2017
- **핵심 기여:** Xen의 제어 평면을 중앙집중형에서 분산형으로 재설계하고, XenStore를 경량 드라이버(noxs)로 대체하여 VM 부팅 시간을 2.3ms까지 단축했다. Docker보다 두 자릿수 빠른 부팅을 달성하며, 수천 개의 VM을 하나의 호스트에서 실행할 수 있음을 보였다.
- **Firecracker와의 관계:** Firecracker의 직접적인 선행 연구로, "VM도 컨테이너만큼 가벼울 수 있다"는 동일한 테제를 탐구한다. LightVM은 Xen 기반, Firecracker는 KVM 기반이라는 차이가 있다.

#### X-Containers: Breaking Down Barriers to Improve Performance and Isolation of Cloud-Native Containers

- **저자:** Zhiming Shen, Zhen Sun, Gur-Eyal Sela, Eugene Bagdasaryan, Christina Delimitrou, Robbert Van Renesse, Hakim Weatherspoon
- **학회:** ASPLOS 2019
- **핵심 기여:** Exokernel에서 영감을 받은 컨테이너 아키텍처로, 각 컨테이너가 Library OS와 함께 실행되고 최소한의 exokernel이 격리를 보장한다. Docker 대비 최대 27배 높은 syscall 처리량을 달성하면서도 강력한 격리를 유지한다.
- **Firecracker와의 관계:** 경량 격리라는 동일한 목표를 추구하지만, 최소 VMM 대신 exokernel + LibOS 설계를 채택한 대안적 아키텍처이다.

#### OSv: Optimizing the Operating System for Virtual Machines

- **저자:** Avi Kivity, Dor Laor, Glauber Costa, Pekka Enberg, Nadav Har'El, Don Marti, Vlad Zolotarov
- **학회:** USENIX ATC 2014
- **핵심 기여:** 클라우드 VM 위에서 단일 애플리케이션을 실행하기 위해 설계된 게스트 OS이다. 하이퍼바이저가 이미 제공하는 기능(격리, 하드웨어 추상화)의 OS 수준 중복을 제거하고, 수정 없는 Linux 실행 파일을 지원하는 Library OS 방식을 사용한다.
- **Firecracker와의 관계:** OSv는 Firecracker의 게스트 OS로 지원된다. Firecracker가 VMM 계층을 최적화한다면, OSv는 게스트 OS 계층을 최적화하는 상호 보완적 접근이다.

#### Intel Cloud Hypervisor

- **출처:** Intel / Cloud Hypervisor 오픈소스 프로젝트, 2019~현재
- **핵심 기여:** Rust로 작성된 VMM으로, Firecracker와 rust-vmm 크레이트를 공유한다. Firecracker보다 더 넓은 워크로드(VFIO, vhost-user, hotplug 등)를 지원하면서도 경량성을 유지한다.
- **Firecracker와의 관계:** rust-vmm 커뮤니티를 통해 핵심 코드를 공유하는 형제 프로젝트이다. Firecracker가 서버리스에 특화되었다면, Cloud Hypervisor는 범용 클라우드 워크로드를 타겟한다.

#### Intel NEMU

- **출처:** Intel, 2018~2019 (아카이브됨)
- **핵심 기여:** QEMU에서 불필요한 레거시 기능(ISA 버스, floppy, 사운드 등)을 제거하여 경량화한 VMM이다. 코드 크기와 공격 표면을 줄이는 접근을 취했다.
- **Firecracker와의 관계:** Firecracker가 QEMU를 완전히 대체한 반면, NEMU는 QEMU를 줄여나가는 접근을 취했다. NEMU는 현재 비활성 상태이며, Cloud Hypervisor로 대체되었다.

---

### 1.2 샌드박스 기반 격리

#### gVisor: Container Runtime Sandbox

- **저자:** Google (James Garbutt et al.)
- **출처:** Google 2018 (오픈소스 공개)
- **핵심 기여:** Go로 작성된 사용자 공간 커널로, Linux syscall의 ~70% (319개 중 200개 이상)를 재구현한다. 애플리케이션의 syscall을 인터셉트하여 사용자 공간(Sentry)에서 처리하고, 호스트에는 ~20개의 syscall만 사용하여 커널 공격 표면을 대폭 축소한다.
- **Firecracker와의 관계:** 둘 다 멀티테넌트 서버리스 격리를 제공하지만, Firecracker는 하드웨어 가상화(KVM)를, gVisor는 소프트웨어 기반 사용자 공간 커널을 사용한다.

#### Nabla Containers: A New Approach to Container Isolation

- **저자:** Dan Williams, Ricardo Koller (IBM Research)
- **학회:** HotCloud 2018
- **핵심 기여:** Solo5 프로젝트의 Library OS 기법을 활용하여 호스트 커널 공격 표면을 최소화한다. 컨테이너가 단 7개의 시스템 콜만 사용하며 나머지는 seccomp으로 차단한다.
- **Firecracker와의 관계:** 멀티테넌트 격리를 위한 공격 표면 축소라는 동일한 목표를 가지나, VMM 대신 Library OS를 사이에 두어 syscall 자체를 회피하는 방식을 취한다.

---

### 1.3 유니커널 (Unikernel)

#### Unikernels: Library Operating Systems for the Cloud (MirageOS)

- **저자:** Anil Madhavapeddy, Richard Mortier, Charalampos Rotsos, David Scott, Balraj Singh, Thomas Gazagnaire, Steven Smith, Steven Hand, Jon Crowcroft
- **학회:** ASPLOS 2013 (ASPLOS 2025 Influential Paper Award 수상)
- **핵심 기여:** 단일 목적의 컴파일 타임 특화 어플라이언스인 유니커널 개념을 도입했다. OCaml로 작성되며, 코드 크기를 한 자릿수 이상 축소하고 정적 타입 안전성과 단일 주소 공간 레이아웃을 결합한다.
- **Firecracker와의 관계:** 클라우드 워크로드에 범용 OS가 불필요하다는 근본적 통찰의 기초 연구이다.

#### Unikraft: Fast, Specialized Unikernels the Easy Way

- **저자:** Simon Kuenzer, Vlad-Andrei Badoiu, Hugo Lefeuvre, Sharan Santhanam, Alexander Jung, Gaulthier Gain, Cyril Soldani, Costin Lupu, Stefan Teodorescu, Costi Raciu, Felipe Huici 외
- **학회:** EuroSys 2021 (Best Paper Award)
- **핵심 기여:** OS 프리미티브를 완전히 모듈화한 마이크로 라이브러리 OS이다. NGINX/SQLite/Redis를 Unikraft에서 실행하면 Linux 게스트 대비 1.7~2.7배 성능 향상, ~1MB 이미지, <10MB RAM, ~1ms 부팅 시간을 보인다.
- **Firecracker와의 관계:** Firecracker가 VMM을 최소화한다면, Unikraft는 게스트 OS를 최소화한다. 두 접근은 결합 가능하다.

#### Unikernels as Processes

- **저자:** Dan Williams, Ricardo Koller, Martin Lucina, Nikhil Prakash
- **학회:** SoCC 2018
- **핵심 기여:** 유니커널이 가상 하드웨어 추상화 없이도 OS 프로세스로 실행될 수 있음을 증명했다. seccomp을 격리에 사용하여 하드웨어 요구사항을 줄이고, 더 높은 처리량과 낮은 시작 시간을 달성한다.
- **Firecracker와의 관계:** Firecracker가 사용하는 하드웨어 가상화가 강력한 격리를 위해 반드시 필요한 것은 아님을 보여준다.

#### Are Unikernels Ready for Serverless on the Edge?

- **학회:** IEEE IC2E 2024
- **핵심 기여:** 유니커널을 엣지 서버리스 환경에서 Firecracker, Docker 등과 체계적으로 비교한다. 유니커널이 콜드 스타트에서 Firecracker를 능가할 수 있으며, 자원 제약 환경에서 우위가 있음을 보인다.
- **Firecracker와의 관계:** Firecracker의 Linux 게스트 + 최소 VMM 접근이 엣지 환경에서는 여전히 과도할 수 있음을 실증한다.

---

### 1.4 서버리스 콜드 스타트 최적화

#### SOCK: Rapid Task Provisioning with Serverless-Optimized Containers

- **저자:** Edward Oakes, Leon Yang, Dennis Zhou, Kevin Houck, Tyler Harter, Andrea C. Arpaci-Dusseau, Remzi H. Arpaci-Dusseau
- **학회:** USENIX ATC 2018
- **핵심 기여:** Linux 컨테이너 프리미티브를 분석하여 스토리지/네트워크 격리의 확장성 병목을 식별했다. 경량 격리 프리미티브(Docker 대비 18배), Zygote 프로비저닝(추가 3배), 3계층 패키지 캐싱(총 45배 속도 향상)을 도입했다.
- **Firecracker와의 관계:** VM이 아닌 컨테이너 수준에서 서버리스 시작 시간을 최적화하는 보완적 연구이다.

#### Nightcore: Efficient and Scalable Serverless Computing for Latency-Sensitive, Interactive Microservices

- **저자:** Zhipeng Jia, Emmett Witchel
- **학회:** ASPLOS 2021
- **핵심 기여:** <100μs 호출 오버헤드와 100K/s 호출률을 달성하는 서버리스 런타임이다. 1.36~2.93배 처리량 향상과 최대 69% 꼬리 지연 감소를 달성한다.
- **Firecracker와의 관계:** Firecracker가 격리 경계를 최적화한다면, Nightcore는 그 경계 안의 런타임을 최적화하는 보완적 연구이다.

#### SEUSS: Skip Redundant Paths to Make Serverless Fast

- **저자:** James Cadden, Thomas Unger, Yara Awad, Han Dong, Orran Krieger, Jonathan Appavoo
- **학회:** EuroSys 2020
- **핵심 기여:** 함수 로직을 유니커널로 패킹하고, x86 더티 비트 스냅샷으로 OS 부팅/런타임 초기화/코드 로딩을 건너뛰는 빠른 배포를 달성한다.
- **Firecracker와의 관계:** 둘 다 경량 VM을 격리에 사용하지만, SEUSS는 유니커널 + 스냅샷으로 콜드 스타트를 수 자릿수 단축한다.

#### Prebaking Functions to Warm the Serverless Cold Start

- **저자:** Paulo Silva, Daniel Fireman, Thiago Emmanuel Pereira
- **학회:** Middleware 2020
- **핵심 기여:** 준비 완료된 서버리스 인스턴스(JVM 상태 포함)의 프로세스 상태를 영속화하고 복원하여 콜드 스타트 오버헤드를 제거한다. 표준 함수 40~71%, JIT 제거 시 최대 1932% 개선을 달성한다.
- **Firecracker와의 관계:** Firecracker의 스냅샷/복원과 유사한 아이디어를 VM이 아닌 애플리케이션/JVM 수준에서 적용한다.

#### Photons: Lambdas on a Diet

- **저자:** Vojislav Dukic, Rodrigo Bruno, Ankit Singla, Gustavo Alonso
- **학회:** SoCC 2020
- **핵심 기여:** 동일 함수의 여러 인스턴스를 단일 런타임 내에 공동 배치하여 언어 런타임과 애플리케이션 상태를 투명하게 공유한다. Java 바이트코드 계측을 통해 인스턴스당 메모리를 25~98% 절감한다.
- **Firecracker와의 관계:** VM이 아닌 애플리케이션 수준에서 메모리 오버헤드를 해결하며, Firecracker와 결합하여 각 MicroVM 내 밀도를 더 높일 수 있다.

#### AQUATOPE: QoS-and-Uncertainty-Aware Resource Management for Multi-stage Serverless Workflows

- **저자:** Zhuangzhuang Zhou, Yanqi Zhang, Christina Delimitrou
- **학회:** ASPLOS 2023
- **핵심 기여:** 베이지안 모델로 함수 호출을 예측하고 사전에 컨테이너를 웜업하며, 함수 단위로 자원을 할당한다. QoS 위반 5배 감소, 비용 34% 절감을 달성한다.
- **Firecracker와의 관계:** Firecracker MicroVM 풀의 지능적 사전 웜업 전략에 직접 적용 가능한 연구이다.

---

### 1.5 스냅샷/복원 기반 빠른 시작

#### Catalyzer: Sub-millisecond Startup for Serverless Computing with Initialization-less Booting

- **저자:** Dong Du, Tianyi Yu, Yubin Xia, Binyu Zang, Guanglu Yan, Chenggang Qin, Qixuan Wu, Haibo Chen
- **학회:** ASPLOS 2020
- **핵심 기여:** gVisor 기반 체크포인트 이미지에서 함수를 복원하여 초기화를 건너뛰는 "init-less booting"을 구현한다. sfork(sandbox fork) 프리미티브로 <1ms 시작을 달성한다.
- **Firecracker와의 관계:** Firecracker의 주요 약점(부팅 시간)을 스냅샷/복원으로 해결한다.

#### REAP: Benchmarking, Analysis, and Optimization of Serverless Function Snapshots (vHive)

- **저자:** Dmitrii Ustiugov, Plamen Petrov, Marios Kogias, Edouard Bugnion, Boris Grot
- **학회:** ASPLOS 2021
- **핵심 기여:** 스냅샷으로 시작된 함수가 페이지 폴트로 95% 느려짐을 발견하고, REAP(Record-and-Prefetch)로 워킹 셋을 사전 프리페치하여 콜드 스타트를 3.7배 감소시킨다.
- **Firecracker와의 관계:** Firecracker와 Containerd 위에 직접 구축. MicroVM 스냅샷의 핵심 한계를 해결한다.

#### Fireworks: A Fast, Efficient, and Safe Serverless Framework using VM-level post-JIT Snapshot

- **저자:** Wonseok Shin, Wook-Hee Kim, Changwoo Min
- **학회:** EuroSys 2022
- **핵심 기여:** VM 스냅샷과 JIT 컴파일을 결합. JIT 이후 스냅샷으로 복원된 함수가 이미 컴파일된 상태가 되며, copy-on-write 공유로 20.6배 속도, 7.3배 메모리 효율 개선을 달성한다.
- **Firecracker와의 관계:** Firecracker 스타일 VM 격리를 JIT 인식 스냅샷으로 확장한다.

#### Slacker: Fast Distribution with Lazy Docker Containers

- **저자:** Tyler Harter, Brandon Salmon, Rose Liu, Andrea C. Arpaci-Dusseau, Remzi H. Arpaci-Dusseau
- **학회:** FAST 2016
- **핵심 기여:** 이미지 풀링이 시작 시간의 76%를 차지하지만 실제 데이터 사용은 6.4%. 지연 로딩으로 배포 5배, 개발 주기 20배 가속한다.
- **Firecracker와의 관계:** VM 부팅 앞 단계인 이미지 배포를 최적화하는 보완적 연구이다.

---

### 1.6 하드웨어 기반 기밀 컴퓨팅

#### Microarchitectural Security of AWS Firecracker VMM for Serverless Cloud Platforms

- **저자:** Zane Weissman, Thore Tiemann, Thomas Eisenbarth, Berk Sunar
- **학회:** ICISS 2024
- **핵심 기여:** Firecracker의 격리를 마이크로아키텍처 공격(Spectre, MDS)에 대해 테스트. 권장 대책 하에서도 악용 가능한 Spectre-PHT 변종을 발견하여 AWS의 보안 주장에 의문을 제기한다.
- **Firecracker와의 관계:** Firecracker의 보안 주장을 직접 평가하며, SEV/TDX 등 추가 보호의 필요성을 제기한다.

#### Occlum: Secure and Efficient Multitasking Inside a Single Enclave of Intel SGX

- **저자:** Youren Shen, Hongliang Tian, Yu Chen, Kang Chen, Runji Wang, Yi Xu, Yubin Xia, Shoumeng Yan
- **학회:** ASPLOS 2020
- **핵심 기여:** 단일 SGX 엔클레이브 내에서 SFI-Isolated Processes(SIP)를 사용한 안전한 멀티태스킹을 구현한다.
- **Firecracker와의 관계:** VM 대신 Intel SGX 하드웨어 엔클레이브를 사용하는 대안적 격리 모델이다.

#### Wallet: Confidential Serverless Computing

- **저자:** Patrick Sabanic, Masanori Misono, Teofil Bodea, Julian Pritzi, Michael Hackl, Dimitrios Stavrakakis, Pramod Bhatotia
- **학회:** NSDI 2026
- **핵심 기여:** CVM(Confidential VM) 내 중첩된 기밀 실행으로 각 함수가 최소 TCB의 "trustlet"에서 실행된다. AMD SEV-SNP/Intel TDX를 활용한다.
- **Firecracker와의 관계:** Firecracker MicroVM을 기밀 컴퓨팅으로 확장. 손상된 하이퍼바이저로부터도 보호한다.

#### Confidential VMs Explained: An Empirical Analysis of AMD SEV-SNP and Intel TDX

- **저자:** Masanori Misono et al. (TU Munich)
- **학회:** ACM SIGMETRICS 2025
- **핵심 기여:** AMD SEV-SNP와 Intel TDX의 성능을 상세히 비교 분석하여 하드웨어 암호화 메모리와 원격 증명의 오버헤드를 정량화한다.
- **Firecracker와의 관계:** MicroVM 위에 기밀 컴퓨팅을 계층화할 때의 성능 비용을 파악하는 데 필수적인 데이터를 제공한다.

#### HECKLER: Breaking Confidential VMs with Malicious Interrupt Injection

- **저자:** Benedict Schluter et al.
- **학회:** USENIX Security 2024
- **핵심 기여:** 악의적 인터럽트 주입으로 기밀 VM(AMD SEV-SNP)을 공격하는 기법을 제시한다. 하이퍼바이저가 인터럽트를 주입하여 CVM 내부 실행 흐름을 조작할 수 있음을 보인다.
- **Firecracker와의 관계:** CVM으로 Firecracker를 보호해도 인터럽트 기반 공격에 취약할 수 있음을 경고한다.

---

### 1.7 Rust 기반 시스템 소프트웨어

#### Theseus: An Experiment in Operating System Structure and State Management

- **저자:** Kevin Boos, Namitha Liyanage, Ramla Ijaz, Lin Zhong
- **학회:** OSDI 2020
- **핵심 기여:** Rust의 소유권 모델을 OS 구조에 적용하여, 모든 OS 컴포넌트를 독립적으로 교체/언로드 가능한 셀(cell)로 구성한다. 컴파일 타임 타입 안전성으로 런타임 오류를 방지한다.
- **Firecracker와의 관계:** Firecracker가 Rust로 작성된 이유(메모리 안전성)의 학문적 근거를 제공한다. Rust의 시스템 소프트웨어 적용 가능성을 OS 수준에서 증명한다.

#### RedLeaf: Isolation and Communication in a Safe Operating System

- **저자:** Vikram Narayanan, Tianjiao Huang, David Detweiler, Dan Appel, Zhaofeng Li, Gerd Zellweger, Anton Burtsev
- **학회:** OSDI 2020
- **핵심 기여:** Rust의 언어 수준 안전성만으로(하드웨어 격리 없이) OS 서브시스템 간 격리를 달성한다. 도메인 간 제로카피 통신을 지원하면서 하드웨어 격리와 유사한 안전성을 제공한다.
- **Firecracker와의 관계:** "하드웨어 가상화 대신 언어 안전성으로 격리가 가능한가?"라는 질문에 대한 실증적 답변이다. Rust의 소유권 모델이 KVM의 역할을 일부 대체할 수 있음을 보인다.

#### RustBelt: Securing the Foundations of the Rust Programming Language

- **저자:** Ralf Jung, Jacques-Henri Jourdan, Robbert Krebbers, Derek Dreyer
- **학회:** POPL 2018
- **핵심 기여:** Rust의 타입 시스템과 안전성 보장을 수학적으로 형식 검증한다. unsafe 코드가 포함되더라도 전체 프로그램의 안전성이 유지됨을 증명한다.
- **Firecracker와의 관계:** Firecracker의 unsafe 코드 사용에 대한 이론적 정당성을 제공한다. Rust VMM의 안전성 주장에 형식적 근거를 부여한다.

#### System Programming in Rust: Beyond Safety

- **저자:** Abhiram Balasubramanian, Marek S. Baranowski, Anton Burtsev, Aurojit Panda, Zvonimir Rakamaric, Leonid Ryzhyk
- **학회:** HotOS 2017
- **핵심 기여:** Rust를 시스템 프로그래밍에 사용할 때 메모리 안전성 외에도 동시성 안전성, 성능 최적화 등의 장점이 있음을 분석한다.
- **Firecracker와의 관계:** Firecracker가 Rust를 선택한 배경과 그 결정의 타당성을 학문적으로 뒷받침한다.

#### Understanding Real-World Concurrency Bugs in Go and Rust

- **저자:** Boqin Qin, Yilun Chen, Zeming Yu, Linhai Song, Yiying Zhang
- **학회:** PLDI 2020 (Rust 부분)
- **핵심 기여:** Rust의 메모리/스레드 안전성이 실제 프로젝트에서 어떻게 작동하는지 경험적으로 연구한다. unsafe 블록이 안전성을 우회하는 패턴을 분석한다.
- **Firecracker와의 관계:** Firecracker와 같은 Rust 기반 시스템의 실제 안전성 수준과 한계를 이해하는 데 필수적이다.

---

### 1.8 컨테이너/VM 최적화

#### CNTR: Lightweight OS Containers

- **저자:** Jorg Thalheim, Pramod Bhatotia, Pedro Fonseca, Baris Kasikci
- **학회:** USENIX ATC 2018
- **핵심 기여:** 컨테이너 이미지를 "slim"(애플리케이션)과 "fat"(디버깅 도구)으로 분리, 필요 시에만 fat 이미지를 동적 연결한다. Top-50 Docker Hub 이미지의 평균 크기를 66.6% 축소한다.
- **Firecracker와의 관계:** Firecracker가 VM을 축소한다면, CNTR은 컨테이너 이미지 자체를 축소하는 보완적 접근이다.

#### Security Namespace: Making Linux Security Frameworks Available to Containers

- **저자:** Yuqiong Sun, David Safford, Mimi Zohar, Dimitrios Pendarakis, Zhongshu Gu, Trent Jaeger
- **학회:** USENIX Security 2018
- **핵심 기여:** 컨테이너가 독립적으로 보안 정책(IMA, AppArmor)을 정의/시행할 수 있는 커널 추상화를 도입한다.
- **Firecracker와의 관계:** VM 격리 없이 컨테이너 내에서 더 세밀한 보안 제어를 제공. VM 격리의 필요성을 줄일 수 있는 보완적 메커니즘이다.

---

### 1.9 컨테이너 이미지 최적화

#### DADI: Block-Level Image Service for Agile and Elastic Application Deployment

- **저자:** Erci Xu, Mohit Kulkarni, Song Wu, Hai Jin et al.
- **학회:** USENIX ATC 2020
- **핵심 기여:** 블록 수준의 지연 로딩을 통해 컨테이너 이미지를 다운로드하지 않고도 즉시 실행할 수 있는 이미지 서비스이다. 파일 시스템 대신 블록 디바이스 수준에서 작동하여 4배 빠른 배포를 달성한다.
- **Firecracker와의 관계:** MicroVM의 rootfs 이미지에도 적용 가능한 지연 로딩 기법. VM 부팅 시간 중 이미지 로딩 비용을 줄인다.

#### Starlight: Fast Container Provisioning on the Edge and over the WAN

- **저자:** Jun Lin Chen et al.
- **학회:** USENIX NSDI 2022
- **핵심 기여:** 엣지/WAN 환경에서 컨테이너 프로비저닝을 최적화한다. 델타 전송과 지연 로딩을 결합하여 원격 레지스트리에서도 빠른 컨테이너 시작을 달성한다.
- **Firecracker와의 관계:** MicroVM 이미지의 엣지 배포에 적용 가능한 최적화 기법을 제공한다.

---

## 2. 반대/대안적 접근의 논문

### 2.1 컨테이너 격리만으로 충분하다

#### RunD: A Lightweight Secure Container Runtime for High-density Deployment and High-concurrency Startup in Serverless Computing

- **저자:** Zijun Li, Jiagan He, Chao Zheng, Yue Zhang 외 다수
- **학회:** USENIX ATC 2022
- **핵심 기여:** Alibaba Cloud의 경량 보안 컨테이너 런타임. 게스트 커널 공유, 호스트 cgroup 기반 자원 관리로 Kata Containers 대비 4배 빠른 시작과 68% 메모리 절감을 달성한다.
- **Firecracker와의 관계:** 가상화를 사용하되 Firecracker보다 더 공격적으로 VM 오버헤드를 제거하여 컨테이너에 가까운 경량성을 보인다.

#### Blending Containers and Virtual Machines: A Study of Firecracker and gVisor

- **저자:** Anjali, Tyler Caraza-Harter, Michael M. Swift
- **학회:** VEE 2020
- **핵심 기여:** Firecracker와 gVisor의 세밀한 코드 커버리지 및 성능 분석. 두 시스템 모두 네이티브 Linux보다 상당히 많은 커널 코드를 실행함을 발견. 가상화 오버헤드가 모든 워크로드에 정당화되지 않음을 보인다.
- **Firecracker와의 관계:** Firecracker의 가정에 직접 도전하며, 워크로드별 최적 격리 기술이 다를 수 있음을 실증한다.

#### SAND: Towards High-Performance Serverless Computing

- **저자:** Istemi Ekin Akkus, Ruichuan Chen, Ivica Rimac, Manuel Stein, Klaus Satzke, Andre Beck, Paarijaat Aditya, Volker Hilt
- **학회:** USENIX ATC 2018
- **핵심 기여:** 애플리케이션 수준 샌드박싱(컨테이너 내 프로세스 격리)과 계층적 메시지 버스로 함수 체이닝을 최적화한다. 같은 애플리케이션 함수들이 컨테이너를 공유하여 OpenWhisk 대비 43% 속도 향상을 달성한다.
- **Firecracker와의 관계:** Firecracker의 반대 철학 -- 격리를 완화(VM→프로세스)하여 성능을 얻는다. 완전한 MicroVM 격리가 불필요한 시나리오를 보여준다.

---

### 2.2 언어 수준 격리 (WebAssembly / V8 Isolates)

#### FAASM: Lightweight Isolation for Efficient Stateful Serverless Computing

- **저자:** Simon Shillaker, Peter Pietzuch
- **학회:** USENIX ATC 2020
- **핵심 기여:** WebAssembly SFI를 사용하여 서버리스 함수를 격리한다. Faaslets로 함수 간 상태 공유를 제공하면서도 격리를 유지, Docker 대비 200배 빠른 초기화를 달성한다.
- **Firecracker와의 관계:** 하드웨어 가상화를 거부하고, 컴파일러 기반 격리만으로 충분하다고 주장한다.

#### Sledge: A Serverless-first, Light-weight Wasm Runtime for Edge Computing

- **저자:** Phani Kishore Gadepalli, Sean McBride, Gregor Peach, Ludmila Cherkasova, Gabriel Parmer
- **학회:** Middleware 2020
- **핵심 기여:** 엣지 컴퓨팅에 최적화된 Wasm 서버리스 런타임. 단일 프로세스 내에서 마이크로초 단위 스케줄링과 초경량 메모리 사용을 달성한다.
- **Firecracker와의 관계:** MicroVM이 엣지에서는 과도하다는 입장에서 Wasm 기반 초경량 격리를 제안한다.

#### Cloudflare Workers: Cloud Computing without Containers (V8 Isolates)

- **저자:** Zack Bloom (Cloudflare)
- **출처:** Cloudflare Blog / Industry Practice, 2018
- **핵심 기여:** V8 Isolate로 VM도 컨테이너도 아닌 언어 런타임 수준 격리를 구현. 하나의 프로세스에서 수만 개의 Isolate를 실행, 콜드 스타트 거의 제로, 메모리 오버헤드 수 KB를 달성한다.
- **Firecracker와의 관계:** "강력한 격리를 위해 하드웨어 가상화가 필요하다"는 전제를 정면 반박한다.

#### Not So Fast: Analyzing the Performance of WebAssembly vs. Native Code

- **저자:** Abhinav Jangda, Bobby Powers, Emery D. Berger, Arjun Guha
- **학회:** USENIX ATC 2019
- **핵심 기여:** Wasm 코드가 네이티브 대비 평균 45% 느림(최대 2.5배)을 발견. 레지스터 할당, 명령어 선택, 메모리 모델 등에서 원인을 분석한다.
- **Firecracker와의 관계:** Wasm 격리의 한계를 정량화하여, VM 격리가 여전히 성능 우위인 영역을 보여준다.

#### Isolation Without Taxation: Near-Zero-Cost Transitions for WebAssembly and SFI

- **저자:** Matthew Kolosick, Shravan Narayan, Evan Johnson, Conrad Watt, Michael LeMay, Deepak Garg, Ranjit Jhala, Deian Stefan
- **학회:** POPL 2022
- **핵심 기여:** Wasm/SFI의 격리 전환 비용이 거의 제로에 가까울 수 있음을 증명. VM 진입/탈출의 고정 비용과 대비된다.
- **Firecracker와의 관계:** vmexit/vmenter 비용이 불가피한 Firecracker와 달리 SFI 전환은 거의 무비용이 가능함을 보여준다.

---

### 2.3 Library OS / OS 리팩토링

#### Drawbridge: A New Form of Virtualization for Application Sandboxing

- **저자:** Donald E. Porter, Silas Boyd-Wickizer, Jon Howell, Rolf Neugebauer, Galen Hunt
- **학회:** ASPLOS 2011
- **핵심 기여:** picoprocess + Library OS로 하드웨어 가상화 없이 애플리케이션을 샌드박싱. 완전한 Windows 앱을 ~16MB 환경에서 실행한다.
- **Firecracker와의 관계:** "OS를 리팩토링하면 가상화가 불필요하다"는 주장의 대표적 연구이다.

#### Graphene: A Library OS for Multi-Process Applications / Graphene-SGX

- **저자:** Chia-Che Tsai, Kumar Saurabh Arora 외 다수 / Chia-Che Tsai, Donald E. Porter, Mona Vij
- **학회:** EuroSys 2014 / USENIX ATC 2017
- **핵심 기여:** Linux 호환 Library OS로 수정 없는 Linux 앱을 다양한 호스트에서 실행. Graphene-SGX는 Intel SGX로 확장하여 기밀 컴퓨팅을 지원한다.
- **Firecracker와의 관계:** VM이 아닌 Library OS로 격리를 달성. Graphene-SGX는 클라우드 제공자로부터의 보호까지 제공한다.

#### Bascule: Composing OS Extensions Safely and Efficiently

- **저자:** Andrew Baumann, Dongyoon Lee, Pedro Fonseca, Lisa Glendenning, Jacob R. Lorch, Barry Bond, Reuben Olinsky, Galen C. Hunt
- **학회:** EuroSys 2013
- **핵심 기여:** OS 확장으로서의 실행 환경을 제안. 계층화된 LibOS 아키텍처로 하드웨어 가상화 없이 격리를 달성한다.
- **Firecracker와의 관계:** 가상화 계층 추가 없이 OS 자체를 확장하여 격리하는 접근이다.

---

### 2.4 순수 소프트웨어 격리 (SFI / 하드웨어 확장)

#### LFI: Lightweight Fault Isolation for Software-based Sandboxing

- **저자:** Adrien Ghosn et al.
- **학회:** ASPLOS 2024
- **핵심 기여:** 단일 프로세스 내에서 수만 개의 샌드박스를 7% 오버헤드로 실행. 컴파일러 수준 계측만으로 메모리 안전성과 제어 흐름 무결성을 보장한다.
- **Firecracker와의 관계:** "VM당 하나의 워크로드" 모델과 정반대로, 단일 프로세스에서 수만 개의 격리된 워크로드를 실행한다.

#### HFI: Hardware-enforced Function Isolation

- **저자:** Shravan Narayan, Tal Garfinkel, Mohammadkazem Taram, Joey Rudek 외 다수
- **학회:** ASPLOS 2023
- **핵심 기여:** 함수 수준 격리를 CPU 확장으로 직접 지원. VM 진입/탈출 없이 하드웨어 수준 메모리/제어 흐름 격리를 제공한다.
- **Firecracker와의 관계:** KVM/VMM 스택 전체를 하드웨어 단일 명령어로 대체할 수 있다면, Firecracker는 과도기적 해결책이 될 수 있다.

#### CAP-VMs: Capability-Based Isolation and Sharing in the Cloud

- **저자:** Vasily A. Sartakov et al.
- **학회:** OSDI 2022
- **핵심 기여:** 능력 기반 하드웨어(ARM CHERI)로 VM 간 제로카피 데이터 공유를 가능하게 하면서 격리를 유지한다.
- **Firecracker와의 관계:** MicroVM 모델의 근본적 한계인 비효율적 VM 간 통신을 해결한다.

---

### 2.5 서버리스 모델 자체에 대한 비판

#### Cloud Programming Simplified: A Berkeley View on Serverless Computing

- **저자:** Eric Jonas, Johann Schleier-Smith, Vikram Sreekanti, Chia-Che Tsai 외 다수
- **학회:** UC Berkeley Technical Report, 2019
- **핵심 기여:** 서버리스의 현재와 미래를 포괄적으로 분석. 격리 오버헤드를 한계의 근본 원인 중 하나로 식별하며, 상태 관리 부재, 통신 비용 등 구조적 한계를 지적한다.
- **Firecracker와의 관계:** 격리를 아무리 최적화해도 서버리스의 근본적 문제는 해결되지 않음을 지적한다.

#### Amazon Prime Video: Monolith Migration

- **저자:** Marcin Kolny (Amazon Prime Video)
- **출처:** Amazon Prime Video Tech Blog, 2023
- **핵심 기여:** 마이크로서비스/서버리스에서 모놀리스로 전환하여 90% 비용 절감을 달성한 실제 사례이다.
- **Firecracker와의 관계:** 서버리스 모델 자체가 특정 워크로드에서 과도한 아키텍처일 수 있다는 실무적 반증이다.

---

### 2.6 엣지/IoT 환경의 대안

#### Pushing Serverless to the Edge with WebAssembly Runtimes

- **학회:** CCGrid 2022
- **핵심 기여:** 엣지에서 Wasm 런타임으로 Docker 대비 99.5% 콜드 스타트 감소를 달성한다. MicroVM이 불가능한 자원 제약 환경에서 Wasm 기반 격리가 유일한 실용적 대안임을 보인다.
- **Firecracker와의 관계:** MicroVM은 최소 수 MB 메모리와 KVM 지원을 필요로 하여 엣지/IoT에서 근본적으로 부적합하다.

---

## 3. 확장 연구

### 3.1 서버리스 스케줄링/오케스트레이션

#### XFaaS: Hyperscale and Low Cost Serverless Functions at Meta

- **저자:** Alireza Sahraei, Soteris Demetriou, Amirali Sobhgol 외 다수
- **학회:** SOSP 2023
- **핵심 기여:** Meta의 프로덕션 서버리스 플랫폼. 하루 수조 건의 함수 호출을 10만+ 서버에서 처리. 지연 허용 함수의 비피크 시간 이연, 리전 간 디스패치, TCP 유사 혼잡 제어로 66% 일 평균 CPU 사용률을 달성한다.
- **Firecracker와의 관계:** AWS Lambda/Firecracker의 대안적 산업 규모 아키텍처. MicroVM 대신 컨테이너 기반 격리로 다른 트레이드오프를 보여준다.

#### Dirigent: Lightweight Serverless Orchestration

- **저자:** Lazar Cvetkovic, Francois Costa, Mihajlo Djokic, Michal Friedman, Ana Klimovic
- **학회:** SOSP 2024
- **핵심 기여:** 클린슬레이트 FaaS 오케스트레이션. 영속 상태 업데이트를 크리티컬 패스에서 제거하고 모놀리식 제어/데이터 플레인을 실행한다. 99th 스케줄링 지연을 AWS Lambda 대비 2.79배 개선, 초당 2500 샌드박스 생성(Knative 대비 1250배)을 달성한다.
- **Firecracker와의 관계:** Firecracker 위의 오케스트레이션 계층이 주요 병목임을 보여주며, 제어 플레인 최적화가 VMM 최적화만큼 중요함을 증명한다.

#### Hermod: Principled and Practical Scheduling for Serverless Functions

- **저자:** Kostis Kaffes, Neeraja J. Yadwadkar, Christos Kozyrakis
- **학회:** SoCC 2022
- **핵심 기여:** 서버리스 스케줄링의 체계적 분류법을 제시하고, 비용/부하/지역성 인식 스케줄링으로 85% 낮은 슬로우다운과 60% 높은 부하 용량을 달성한다.
- **Firecracker와의 관계:** MicroVM 격리 계층 위의 스케줄링 최적화에 직접 적용 가능하다.

#### Fifer: Tackling Resource Underutilization in the Serverless Era

- **저자:** Jashwant Raj Gunasekaran, Prashanth Thinakaran, Nachiappan Chidambaram Nachiappan, Mahmut T. Kandemir, Chita R. Das
- **학회:** Middleware 2020
- **핵심 기여:** 함수 인식 컨테이너 스케일링, 지능적 요청 배칭(빈 패킹), 선제적 컨테이너 생성으로 컨테이너 활용률 4배, 클러스터 에너지 소비 31% 개선을 달성한다.
- **Firecracker와의 관계:** 과다 프로비저닝된 MicroVM의 자원 낭비 문제를 해결하는 빈 패킹 전략을 제공한다.

#### Palette Load Balancing: Locality Hints for Serverless Functions

- **저자:** Mania Abdi, Samuel Ginzburg, Charles Lin, Jose M. Faleiro 외 다수
- **학회:** EuroSys 2023
- **핵심 기여:** "색상"이라는 지역성 힌트로 FaaS 앱이 데이터 친화성을 플랫폼에 표현할 수 있게 한다. 서버리스 탄력성을 유지하면서 데이터 지역성 이점을 달성한다.
- **Firecracker와의 관계:** MicroVM 배치가 사용자에게 불투명한 한계를 해결. 서버리스 추상화를 깨지 않고 지역성 인식 스케줄링을 가능하게 한다.

#### Iluvatar: A Fast Control Plane for Serverless Computing

- **저자:** Alexander Fuerst, Abdul Rehman, Prateek Sharma
- **학회:** HPDC 2023
- **핵심 기여:** Rust로 구현된 빠른 모듈러 FaaS 제어 플레인(~13K LOC). 3ms 지연만 추가하며 OpenWhisk 대비 100배 이상 낮은 지연을 제공한다. Firecracker를 백엔드로 지원한다.
- **Firecracker와의 관계:** 제어 플레인 오버헤드가 VM 부팅보다 더 큰 병목이 될 수 있음을 보여주는 연구 플랫폼이다.

#### Jolteon: Unleashing the Promise of Serverless for Serverless Workflows

- **저자:** Zili Zhang, Chao Jin, Xin Jin
- **학회:** NSDI 2024
- **핵심 기여:** 확률적 성능 모델(화이트박스 + 블랙박스)을 결합한 서버리스 워크플로 오케스트레이터. Orion 대비 비용 2.3배, 지연 2.1배 개선을 달성한다.
- **Firecracker와의 관계:** Firecracker 기반 AWS Lambda에서 워크플로 오케스트레이션 오버헤드를 최적화한다.

---

### 3.2 서버리스 워크플로/함수 합성

#### Faastlane: Accelerating Function-as-a-Service Workflows

- **저자:** Swaroop Kotni, Ajay Nayak, Vinod Ganapathy, Arkaprava Basu
- **학회:** USENIX ATC 2021
- **핵심 기여:** 같은 워크플로의 함수들을 동일 프로세스 내 스레드로 실행하고, 프로세스 격리가 필요한 경우에만 분리한다. 워크플로 실행 시간을 최대 15배 개선한다.
- **Firecracker와의 관계:** Firecracker의 "함수당 하나의 VM" 모델이 워크플로 실행에서 과도한 오버헤드를 초래함을 보여준다.

#### SONIC: Application-aware Data Passing for Chained Serverless Applications

- **저자:** Ashraf Mahgoub, Karthick Shankar, Subrata Mitra, Ana Klimovic, Somali Chaterji, Saurabh Bagchi
- **학회:** USENIX ATC 2021
- **핵심 기여:** 서버리스 함수 체인에서 데이터 전달 방식(직접 전달 vs. 스토리지 경유)을 데이터 크기, 함수 지연, 팬아웃에 따라 자동 최적화한다.
- **Firecracker와의 관계:** MicroVM 간 데이터 전달이 반드시 외부 스토리지를 경유해야 하는 Firecracker의 한계를 우회하는 전략을 제공한다.

#### WISEFUSE: Workload Characterization and DAG Transformation for Serverless Workflows

- **저자:** Ashraf Mahgoub, Edgardo Barsallo Yi, Karthick Shankar, Sameh Elnikety, Somali Chaterji, Saurabh Bagchi
- **학회:** ACM SIGMETRICS 2022 (Best Paper Award)
- **핵심 기여:** 서버리스 워크플로의 DAG를 자동 변환하여 함수 병합(latency-critical 경로), 자원 할당, 병합 최적화를 수행한다. 지연을 67% 개선하면서 비용을 유지한다.
- **Firecracker와의 관계:** MicroVM 격리의 함수 간 통신 오버헤드를 워크플로 수준 변환으로 완화한다.

#### Pheromone: Application-Aware Serverless Function Scheduling

- **저자:** Haoyuan Yu et al.
- **학회:** USENIX NSDI 2023
- **핵심 기여:** 애플리케이션 인식 서버리스 함수 스케줄링. 함수 간 데이터 흐름을 고려하여 관련 함수를 같은 노드에 배치한다.
- **Firecracker와의 관계:** MicroVM 배치에서 데이터 지역성을 고려하여 VM 간 통신 오버헤드를 줄인다.

#### Unum: Cloud Serverless Application Coordination

- **저자:** Dong Liu et al.
- **학회:** USENIX NSDI 2023
- **핵심 기여:** 중앙 오케스트레이터 없이 서버리스 함수가 자체 조율하는 탈중앙화 워크플로 조율 시스템이다.
- **Firecracker와의 관계:** AWS Step Functions 같은 중앙 오케스트레이터의 병목을 제거하여 Firecracker 기반 Lambda 워크플로를 개선한다.

---

### 3.3 서버리스 상태 관리

#### Boki: Stateful Serverless Computing with Shared Logs

- **저자:** Zhipeng Jia, Emmett Witchel
- **학회:** SOSP 2021
- **핵심 기여:** 공유 로그 API를 서버리스 함수에 제공하여 강한 일관성, 내구성, 장애 허용을 달성하는 FaaS 런타임. "metalog"로 순서, 일관성, 장애 허용을 분리. 워크로드를 최대 4.7배 가속한다.
- **Firecracker와의 관계:** MicroVM의 무상태 제약을 효율적 공유 로그로 해결한다.

#### Halfmoon: Log-Optimal Fault-Tolerant Stateful Serverless Computing

- **저자:** Sheng Qi, Xuanzhe Liu, Xin Jin
- **학회:** SOSP 2023
- **핵심 기여:** 읽기와 쓰기의 대칭적 로깅이 exactly-once 시맨틱에 불필요함을 증명하고, 로그 최적 프로토콜(로그 프리 읽기/쓰기)을 제공한다. Boki 대비 지연 20~40% 감소, 로깅 오버헤드 1.5~4배 감소를 달성한다.
- **Firecracker와의 관계:** 임시 MicroVM에서 함수가 언제든 종료될 수 있을 때 필수적인 exactly-once 보장을 개선한다.

#### Beldi: Fault-tolerant and Transactional Stateful Serverless Workflows

- **저자:** Haoran Zhang, Adney Cardoza, Peter Baile Chen, Sebastian Angel, Vincent Liu
- **학회:** OSDI 2020
- **핵심 기여:** 로그 기반 exactly-once 실행 시맨틱으로 장애 허용, 트랜잭션 서버리스 함수를 작성하는 라이브러리. 1,000개의 AWS Lambda에서 평가되었다.
- **Firecracker와의 관계:** Firecracker 기반 AWS Lambda에서 직접 실행되며, MicroVM의 무상태 특성을 보완하는 트랜잭션 보장을 제공한다.

#### Durable Functions: Semantics for Stateful Serverless

- **저자:** Sebastian Burckhardt, Chris Gillum, David Justo, Konstantinos Kallas, Connor McMahon, Christopher S. Meiklejohn
- **학회:** OOPSLA 2021
- **핵심 기여:** Azure Durable Functions의 프로그래밍 모델(액터, 워크플로, 임계 구역)을 형식화하고 record-replay 시맨틱으로 상태 영속화를 정의한다.
- **Firecracker와의 관계:** 임시 MicroVM 인스턴스 간 상태를 올바르게 영속화하는 이론적 기반을 제공한다.

#### Cloudburst: Stateful Functions-as-a-Service

- **저자:** Vikram Sreekanti, Chenggang Wu, Xiayue Charles Lin, Johann Schleier-Smith, Joseph E. Gonzalez, Joseph M. Hellerstein, Alexey Tumanov
- **학회:** VLDB 2020
- **핵심 기여:** Anna KVS 위에 구축된 상태 유지 FaaS 플랫폼. 변경 가능한 캐시를 함수 실행기와 공동 배치하여 데이터 지역성을 달성하고, 격자 자료구조로 공유 상태의 충돌을 해결한다.
- **Firecracker와의 관계:** MicroVM 기반 서버리스의 데이터 접근 지연 문제를 캐시 공동 배치로 해결한다.

---

### 3.4 서버리스 보안

#### Guarding Serverless Applications with Kalium

- **저자:** Deepak Sirone Jegan, Liang Wang, Siddhant Bhagat, Michael Swift
- **학회:** USENIX Security 2023
- **핵심 기여:** 제어 흐름 무결성(CFI)을 서버리스 앱에 시행하는 보안 프레임워크. 가드 함수가 syscall을 모니터링하고 NFA 기반 플로 그래프로 상태 테이블을 유지한다. gVisor 위에 구축되어 ~2.4ms 최악 오버헤드를 보인다.
- **Firecracker와의 관계:** VM 수준 격리를 보완하는 애플리케이션 수준 보안. Firecracker가 테넌트 간 격리를 제공한다면, Kalium은 함수 내부의 시맨틱 공격을 방어한다.

#### Secure Serverless Computing Using Dynamic Information Flow Control (Trapeze)

- **저자:** Kalev Alpernas, Cormac Flanagan, Sadjad Fouladi, Leonid Ryzhyk, Mooly Sagiv, Thomas Schmitz, Keith Winstein
- **학회:** OOPSLA 2018
- **핵심 기여:** 동적 정보 흐름 제어(IFC) 레이블로 서버리스 함수 호출 간 데이터 기밀성/무결성을 추적 시행하여 함수 체인을 통한 데이터 유출을 방지한다.
- **Firecracker와의 관계:** 완벽한 VM 격리가 있어도 함수가 출력을 통해 데이터를 유출할 수 있다는 보안 갭을 해결한다.

---

### 3.5 GPU/가속기 서버리스

#### Molecule: Serverless Computing on Heterogeneous Computers

- **저자:** Dong Du, Qingyuan Liu, Xueqiang Jiang, Yubin Xia, Binyu Zang, Haibo Chen
- **학회:** ASPLOS 2022
- **핵심 기여:** 이기종 컴퓨터를 위한 최초의 서버리스 시스템. XPU-Shim(분산 shim)과 Vectorized Sandbox(가속기 추상화)로 50% 높은 함수 밀도와 최대 34.6배 성능 개선을 달성한다.
- **Firecracker와의 관계:** CPU 전용 MicroVM인 Firecracker를 이기종 하드웨어(GPU, FPGA, DPU)로 확장한다.

#### StreamBox: A Lightweight GPU Sandbox for Serverless Inference Workflow

- **저자:** Hao Wu, Yue Yu, Junxiao Deng, Shadi Ibrahim, Song Wu, Hao Fan, Ziyue Cheng, Hai Jin
- **학회:** USENIX ATC 2024
- **핵심 기여:** CUDA 스트림을 경량 GPU 샌드박스로 사용(별도 CUDA 컨텍스트 대신). GPU 메모리 82% 절감, 시작 지연 98% 감소(<5ms), 처리량 6.7배 개선을 달성한다.
- **Firecracker와의 관계:** Firecracker의 CPU 경량 VM 접근의 GPU 등가물 -- GPU 런타임을 최소화하여 GPU 함수 격리를 달성한다.

#### INFless: A Native Serverless System for Low-Latency, High-Throughput Inference

- **저자:** Yanan Yang, Laiping Zhao, Yiming Li 외 다수
- **학회:** ASPLOS 2022
- **핵심 기여:** ML 추론 전용 서버리스 플랫폼. 통합 CPU/가속기 자원 추상화, 내장 배칭, 비균일 스케일링을 제공한다.
- **Firecracker와의 관계:** 범용 서버리스 플랫폼(Firecracker 포함)이 ML 추론에 부적합함을 보여주며 도메인 특화 최적화가 필요함을 주장한다.

#### ServerlessLLM: Locality-Enhanced Serverless Inference for Large Language Models

- **저자:** Yao Fu, Leyang Xue, Yeqi Huang, Andrei-Octavian Brabete, Dmitrii Ustiugov, Yuvraj Patel, Luo Mai
- **학회:** OSDI 2024
- **핵심 기여:** 로딩 최적화 체크포인트, 다계층 로딩, 지역성 기반 할당/라이브 마이그레이션으로 LLM 서빙의 지연을 기존 대비 10~200배 개선한다.
- **Firecracker와의 관계:** LLM 모델 로딩(수십 GB)이 Firecracker 부팅 시간을 압도하며, AI 워크로드의 "콜드 스타트"는 VM 부팅이 아닌 모델 로딩에 있음을 보여준다.

---

### 3.6 메모리 중복 제거/공유

#### User-guided Page Merging (UPM) for Memory Deduplication in Serverless Systems

- **저자:** Wei Qiu, Marcin Copik, Yun Wang, Alexandru Calotoiu, Torsten Hoefler
- **학회:** IEEE BigData 2023
- **핵심 기여:** KSM의 느린 배경 스캐닝을 madvise 기반 애플리케이션 힌트로 대체하여 빠른 페이지 공유를 달성한다. 16개 동시 컨테이너에서 메모리 소비를 최대 55% 절감, 컨테이너 밀도를 2배 이상 높인다.
- **Firecracker와의 관계:** MicroVM의 메모리 밀도 한계를 직접 해결한다. KSM은 임시 MicroVM에 너무 느리지만, UPM의 힌트 기반 접근은 Firecracker의 빠른 생명주기에서 작동한다.

---

### 3.7 보안 컨테이너 (Kata Containers 등)

#### A Comparative Study of RunC, gVisor, Kata Containers

- **저자:** Wang et al.
- **학회:** Cluster Computing 2022
- **핵심 기여:** RunC, gVisor, Kata Containers의 성능을 체계적으로 비교. 시작 시간, 메모리 오버헤드, I/O 성능, 네트워크 성능 등 다차원적 벤치마크를 제공한다.
- **Firecracker와의 관계:** Firecracker와 유사한 VM 기반 격리를 제공하는 Kata Containers의 성능 특성을 이해하는 데 필수적이다.

#### Network Startup Bottleneck in Secure Container Runtimes

- **저자:** Liu et al.
- **학회:** ACM IMC 2024
- **핵심 기여:** 보안 컨테이너 런타임의 네트워크 시작 병목을 식별하고 분석한다. VM 기반 격리에서 네트워크 스택 초기화가 전체 시작 시간의 상당 부분을 차지함을 보인다.
- **Firecracker와의 관계:** Firecracker MicroVM의 네트워크 설정 시간이 전체 부팅 시간에 미치는 영향을 정량화한다.

#### CKI: Container Kernel Isolation using PKS

- **저자:** Shi et al.
- **학회:** EuroSys 2025
- **핵심 기여:** Intel PKS(Protection Keys for Supervisor)를 사용하여 컨테이너 커널 격리를 달성한다. VM 오버헤드 없이 커널 수준 격리를 제공한다.
- **Firecracker와의 관계:** VM 없이도 PKS 하드웨어로 커널 수준 격리가 가능함을 보여 Firecracker의 필요성에 도전한다.

#### PVM: Shadow Paging for Nested Secure Containers

- **저자:** Huang et al.
- **학회:** SOSP 2023
- **핵심 기여:** 중첩된 보안 컨테이너를 위한 섀도 페이징 기법. 중첩 가상화의 성능 오버헤드를 줄인다.
- **Firecracker와의 관계:** MicroVM의 중첩 배포 시나리오(VM 안의 MicroVM)의 성능을 개선한다.

#### RContainer: ARM CCA-based Secure Containers

- **저자:** Zhou et al.
- **학회:** NDSS 2025
- **핵심 기여:** ARM의 Confidential Compute Architecture(CCA)를 활용한 보안 컨테이너. x86 기반 Firecracker/KVM과 다른 아키텍처에서의 기밀 컴퓨팅 접근을 제시한다.
- **Firecracker와의 관계:** ARM 플랫폼에서의 기밀 컨테이너 격리 대안을 제공한다.

---

### 3.8 eBPF 기반 보안/격리

#### Programmable System Call Security with eBPF

- **저자:** Jia et al.
- **출처:** arXiv 2023
- **핵심 기여:** eBPF로 프로그래밍 가능한 시스템 콜 보안 정책을 구현한다. seccomp-bpf보다 유연하고 표현력 있는 보안 정책을 낮은 오버헤드로 시행한다.
- **Firecracker와의 관계:** Firecracker의 jailer가 사용하는 seccomp 프로파일을 더 동적이고 정교하게 대체할 수 있는 메커니즘을 제안한다.

#### Cross-container eBPF Attacks

- **저자:** He et al.
- **학회:** USENIX Security 2023
- **핵심 기여:** eBPF를 악용한 크로스 컨테이너 공격을 발견하고 분석한다. 컨테이너 격리가 eBPF를 통해 우회될 수 있음을 보인다.
- **Firecracker와의 관계:** 컨테이너 격리의 eBPF 기반 취약점이 VM 격리(Firecracker)에서는 발생하지 않음을 보여, VM 격리의 추가적 가치를 입증한다.

#### BPFContain: eBPF-based Container Security

- **저자:** Findlay et al.
- **출처:** 2021
- **핵심 기여:** eBPF를 활용한 컨테이너 보안 프레임워크. 런타임 보안 정책을 커널에 주입하여 컨테이너 격리를 강화한다.
- **Firecracker와의 관계:** VM 격리 없이 eBPF로 컨테이너 보안을 강화하는 대안적 접근이다.

#### Rex: Rust Kernel Extensions Replacing eBPF

- **저자:** Jia et al.
- **학회:** USENIX ATC 2025
- **핵심 기여:** eBPF 대신 Rust로 커널 확장을 작성할 수 있게 한다. Rust의 타입 안전성으로 eBPF의 검증기 한계를 극복하면서 동등한 성능을 제공한다.
- **Firecracker와의 관계:** Firecracker와 동일한 언어(Rust)를 커널 확장에 적용하여 eBPF 기반 보안의 안전성과 표현력을 개선한다.

---

### 3.9 서버리스 네트워킹/NFV

#### Serverless for Network Function Virtualization

- **저자:** Aditya et al.
- **학회:** Proc. IEEE 2019
- **핵심 기여:** 서버리스 모델을 네트워크 함수 가상화(NFV)에 적용하는 방안을 조사한다. 네트워크 함수의 서버리스화가 가져올 이점과 과제를 분석한다.
- **Firecracker와의 관계:** MicroVM 기반 격리를 네트워크 함수에 적용할 때의 요구사항과 한계를 분석한다.

#### Serpens: NFV Platform with Serverless

- **저자:** Shen et al.
- **학회:** IWQoS 2020
- **핵심 기여:** 서버리스 아키텍처를 NFV 플랫폼에 실제 적용한 시스템. 네트워크 함수의 동적 스케일링과 격리를 서버리스 모델로 구현한다.
- **Firecracker와의 관계:** Firecracker MicroVM이 네트워크 함수 격리에 적합한지 실증적으로 평가한다.

---

### 3.10 마이크로커널/하이퍼바이저 비교

#### seL4 as VMM: Microkernel-based Virtual Machine Monitor

- **저자:** de Matos, Ahvenjarvi
- **학회:** Electronics/MDPI 2022
- **핵심 기여:** 형식 검증된 마이크로커널 seL4을 VMM으로 사용하여 가상 머신을 관리한다. 형식 검증의 보안 보장과 마이크로커널의 최소 TCB를 VM 격리에 적용한다.
- **Firecracker와의 관계:** Firecracker가 Linux/KVM 위의 최소 VMM이라면, seL4 VMM은 형식 검증된 마이크로커널 위의 VMM으로, TCB를 더욱 줄이고 수학적 보안 보장을 제공한다.

#### Quantitative Comparison of Xen and KVM

- **저자:** Deshane et al.
- **학회:** Xen Summit 2008
- **핵심 기여:** Xen과 KVM의 성능을 정량적으로 비교. CPU, 메모리, I/O, 네트워크 등 다차원적 벤치마크를 제공한다.
- **Firecracker와의 관계:** Firecracker가 KVM을 선택한 배경을 이해하는 데 필수적인 하이퍼바이저 비교 연구이다.

#### Scalability Comparison of Xen and KVM

- **저자:** Soriga, Barbulescu
- **학회:** IEEE ECAI 2013
- **핵심 기여:** 확장성 관점에서 Xen과 KVM을 비교. VM 수 증가에 따른 성능 변화를 분석한다.
- **Firecracker와의 관계:** 수천 개의 MicroVM을 호스팅하는 Firecracker에서 KVM의 확장성이 핵심 요소임을 뒷받침한다.

---

## 4. 컨테이너 런타임 및 클러스터 오케스트레이션

### 4.1 클러스터 관리 시스템 (Borg, Omega, Mesos, YARN, Twine)

#### Large-scale Cluster Management at Google with Borg

- **저자:** Abhishek Verma, Luis Pedrosa, Madhukar R. Korupolu, David Oppenheimer, Eric Tune, John Wilkes
- **학회:** EuroSys 2015
- **핵심 기여:** Google의 Borg 클러스터 관리자를 기술한다. 수만 대의 머신 클러스터에서 수십만 개의 작업을 실행한다. 승인 제어, 효율적 태스크 패킹, 오버커밋, 프로세스 수준 성능 격리를 통해 높은 자원 활용률을 달성한다. Kubernetes 설계에 직접적 영향을 준 기초 논문이다.
- **Firecracker와의 관계:** Firecracker가 실행되는 AWS Lambda의 상위 계층인 클러스터 스케줄링의 핵심 참조 모델이다.

#### Omega: Flexible, Scalable Schedulers for Large Compute Clusters

- **저자:** Malte Schwarzkopf, Andy Konwinski, Michael Abd-El-Malek, John Wilkes
- **학회:** EuroSys 2013 (Best Student Paper Award)
- **핵심 기여:** 공유 상태 스케줄링 아키텍처를 제안. 병렬성과 잠금 없는 낙관적 동시성 제어를 사용한다. 모놀리식, 2단계, 공유 상태 스케줄러 설계를 비교하여 공유 상태 접근이 더 나은 확장성과 특화된 스케줄링 정책을 허용함을 보인다.
- **Firecracker와의 관계:** 대규모 서버리스 플랫폼에서 MicroVM 배치 결정을 내리는 스케줄러 설계의 핵심 참조이다.

#### Borg, Omega, and Kubernetes: Lessons Learned

- **저자:** Brendan Burns, Brian Grant, David Oppenheimer, Eric Brewer, John Wilkes
- **학회:** Communications of the ACM, Vol. 59, No. 5, 2016
- **핵심 기여:** Google이 10년간 세 개의 컨테이너 관리 시스템(Borg, Omega, Kubernetes)을 구축하면서 얻은 교훈을 정리한다. 모놀리스에서 마이크로서비스로의 전환, 선언적 API의 가치 등 핵심 아키텍처 통찰을 제공한다.
- **Firecracker와의 관계:** 컨테이너/VM 오케스트레이션의 진화 과정을 이해하는 필수 문헌이다.

#### Borg: the Next Generation

- **저자:** Muhammad Tirmazi, Adam Barker, Nan Deng, Md E. Haque, Zhijing Gene Qin, Steven Hand, Mor Harchol-Balter, John Wilkes
- **학회:** EuroSys 2020
- **핵심 기여:** 2019년 5월의 8개 Borg 클러스터 트레이스를 분석하고 2011년 트레이스와 종단적 비교를 수행한다. Borg가 어떻게 진화했는지, 워크로드가 어떻게 변화했는지에 대한 통찰을 제공한다.
- **Firecracker와의 관계:** 대규모 클러스터 워크로드의 변화 추세를 이해하는 데 필수적이다.

#### Mesos: A Platform for Fine-Grained Resource Sharing in the Data Center

- **저자:** Benjamin Hindman, Andy Konwinski, Matei Zaharia, Ali Ghodsi, Anthony D. Joseph, Randy Katz, Scott Shenker, Ion Stoica
- **학회:** NSDI 2011
- **핵심 기여:** 리소스 오퍼 기반의 분산 2단계 스케줄링으로 다양한 프레임워크(Hadoop, MPI 등) 간 클러스터를 공유하는 플랫폼이다. 5만 노드 에뮬레이션에서 확장성과 거의 최적의 데이터 지역성을 달성한다.
- **Firecracker와의 관계:** 2단계 스케줄링 모델은 AWS Lambda의 배치 서비스와 유사한 리소스 할당 패턴을 보인다.

#### Dominant Resource Fairness: Fair Allocation of Multiple Resource Types

- **저자:** Ali Ghodsi, Matei Zaharia, Benjamin Hindman, Andy Konwinski, Scott Shenker, Ion Stoica
- **학회:** NSDI 2011
- **핵심 기여:** 다중 리소스 유형(CPU, 메모리 등)에 대한 max-min 공정성을 지배적 자원 개념으로 일반화한다. 전략 방지, 질투 무관, 파레토 효율적이며, Mesos에 구현되어 슬롯 기반 공유보다 우수한 처리량과 공정성을 달성한다.
- **Firecracker와의 관계:** 멀티테넌트 서버리스 플랫폼에서 MicroVM에 CPU/메모리를 공정하게 할당하는 이론적 기반이다.

#### YARN: Yet Another Resource Negotiator

- **저자:** Vinod Kumar Vavilapalli et al.
- **학회:** SoCC 2013
- **핵심 기여:** Apache Hadoop의 차세대 리소스 관리 플랫폼이다. 리소스 관리와 작업 스케줄링/모니터링을 분리하여 다양한 분산 컴퓨팅 패러다임을 지원한다.
- **Firecracker와의 관계:** 리소스 관리와 워크로드 실행의 분리라는 아키텍처 패턴은 Lambda의 배치 서비스와 워커 구조에도 반영되어 있다.

#### Twine: A Unified Cluster Management System for Shared Infrastructure

- **저자:** Chunqiang Tang, Kenny Yu, Kaushik Veeraraghavan, Jonathan Kaldor 외 다수
- **학회:** OSDI 2020
- **핵심 기여:** Meta(Facebook)의 프로덕션 클러스터 관리 시스템. 사일로화된 커스텀 머신 풀을 대규모 공유 인프라로 전환했다. 단일 제어 플레인이 하나의 지리적 지역 내 100만 대의 머신을 관리한다.
- **Firecracker와의 관계:** AWS Lambda와는 다른 클러스터 관리 접근을 보여주며, 컨테이너 기반 대규모 공유 인프라의 실제 운영을 기술한다.

---

### 4.2 클러스터 스케줄링 알고리즘

#### Sparrow: Distributed, Low Latency Scheduling

- **저자:** Kay Ousterhout, Patrick Wendell, Matei Zaharia, Ion Stoica
- **학회:** SOSP 2013
- **핵심 기여:** 랜덤 샘플링 기반 분산 스케줄링으로, 중앙화 설계의 처리량/가용성 한계를 피하면서 거의 최적의 성능을 제공한다. 110대 클러스터에서 이상적 스케줄러 대비 12% 내의 성능을 달성한다.
- **Firecracker와의 관계:** 대규모 MicroVM 배치에서 중앙 스케줄러의 병목을 우회하는 분산 스케줄링 대안을 제시한다.

#### Firmament: Fast, Centralized Cluster Scheduling at Scale

- **저자:** Ionel Gog, Malte Schwarzkopf, Adam Gleave, Robert N. M. Watson, Steven Hand
- **학회:** OSDI 2016
- **핵심 기여:** 최소 비용 최대 유량(MCMF) 최적화로 1만 대 이상의 머신에 서브초 배치 지연을 달성하는 중앙 스케줄러이다. Quincy 대비 12,500대 Google 클러스터 트레이스에서 배치 지연을 20배 개선한다.
- **Firecracker와의 관계:** MicroVM 배치 최적화에 적용 가능한 효율적 스케줄링 알고리즘을 제공한다.

#### Tetris: Multi-Resource Packing for Cluster Schedulers

- **저자:** Robert Grandl, Ganesh Ananthanarayanan, Srikanth Kandula, Sriram Rao, Aditya Akella
- **학회:** SIGCOMM 2014
- **핵심 기여:** CPU, 메모리, 디스크, 네트워크 등 다중 리소스 요구사항에 따라 태스크를 머신에 패킹하여 리소스 단편화와 과다 할당을 방지한다. Facebook 트레이스에서 완료 시간 30~40% 개선을 달성한다.
- **Firecracker와의 관계:** MicroVM의 다중 리소스(CPU, 메모리, 네트워크) 패킹에 직접 적용 가능한 알고리즘이다.

#### Quincy: Fair Scheduling for Distributed Computing Clusters

- **저자:** Michael Isard, Vijayan Prabhakaran, Jon Currey, Udi Wieder, Kunal Talwar, Andrew V. Goldberg
- **학회:** SOSP 2009
- **핵심 기여:** 스케줄링 문제를 그래프 구조로 매핑하여, 에지 가중치로 데이터 지역성/공정성/기아 방지 요구를 인코딩한다. 최소 비용 최대 유량 솔버로 전역 비용 모델에 따른 최적 스케줄을 계산한다.
- **Firecracker와의 관계:** 그래프 기반 스케줄링 최적화는 MicroVM 배치의 지역성/공정성 요구에 적용 가능하다.

#### Delay Scheduling: A Simple Technique for Achieving Locality and Fairness

- **저자:** Matei Zaharia, Dhruba Borthakur, Joydeep Sen Sarma, Khaled Elmeleegy, Scott Shenker, Ion Stoica
- **학회:** EuroSys 2010
- **핵심 기여:** 로컬 태스크를 실행할 수 없는 작업이 잠시 대기하여 다른 작업이 먼저 실행되게 하는 간단한 알고리즘이다. 다양한 워크로드에서 거의 최적의 데이터 지역성과 최대 2배 처리량 향상을 공정성을 유지하면서 달성한다.
- **Firecracker와의 관계:** Lambda의 스티키 라우팅과 유사한 지역성 최적화 원칙을 제공한다.

---

### 4.3 Kubernetes 연구

#### Kubernetes Scheduling: Taxonomy, Ongoing Issues and Challenges

- **저자:** Carmen Carrion
- **학회:** ACM Computing Surveys, Vol. 55, No. 7, 2022
- **핵심 기여:** 2016~2022년 Kubernetes 오케스트레이터 제안을 분석하여 계층적 분류법을 제시한다. 96개 소스를 다루며 확장성, 리소스 가상화, 분산 제어 플레인을 핵심 미래 연구 이슈로 식별한다.

#### A Survey of Kubernetes Scheduling Algorithms

- **저자:** Khaldoun Senjab, Sohail Abbas, Naveed Ahmed, Atta ur Rehman Khan
- **학회:** Journal of Cloud Computing, Vol. 12, 2023
- **핵심 기여:** 47개 최근 연구를 범용 스케줄링, 다목적 최적화, AI 기반 스케줄링, 오토스케일링 카테고리로 분류하여 포괄적으로 리뷰한다.

#### Acto: Automatic End-to-End Testing for Operation Correctness of Cloud System Management

- **저자:** (SOSP 2023 연구 그룹)
- **학회:** SOSP 2023
- **핵심 기여:** Kubernetes 오퍼레이터의 자동 종단 테스트 프레임워크. 오퍼레이터의 운영 정확성을 검증하여 프로덕션 장애를 예방한다.

---

### 4.4 오토스케일링

#### Autopilot: Workload Autoscaling at Google

- **저자:** Krzysztof Rzadca, Pawel Findeisen, Jacek Swiderski, Przemyslaw Zych 외 다수
- **학회:** EuroSys 2020
- **핵심 기여:** Google의 프로덕션 오토스케일링 시스템. 수평(태스크 수)과 수직(CPU/메모리 제한) 모두를 자동 조정한다. 자동 관리 작업은 23% 여유분(수동 46% 대비), OOM 영향 작업을 10배 감소시킨다. Google 플릿 자원의 48% 이상을 관리한다.
- **Firecracker와의 관계:** MicroVM 풀의 자동 크기 조정에 적용 가능한 프로덕션 검증된 오토스케일링 전략을 제공한다.

#### The Power of Prediction: Microservice Auto Scaling via Workload Learning (Madu)

- **저자:** Shutian Luo, Huanle Xu, Kejiang Ye, Guoyao Xu 외 다수
- **학회:** SoCC 2022
- **핵심 기여:** 복잡한 호출 체인의 개별 마이크로서비스 수요를 예측하는 선제적 오토스케일러. 할당 자원 40% 절감, 엔드투엔드 지연 36% 감소를 달성한다.
- **Firecracker와의 관계:** 서버리스 함수 체인에서의 선제적 MicroVM 프로비저닝에 활용 가능하다.

#### DeepScaling: Microservices AutoScaling for Stable CPU Utilization in Large Scale Cloud Systems

- **학회:** SoCC 2022
- **핵심 기여:** 딥러닝 기반 오토스케일링으로 대규모 마이크로서비스 배포에서 안정적 CPU 사용률을 유지한다. 반응형 및 기존 선제적 스케일링 대비 개선을 보인다.

---

### 4.5 컨테이너 vs VM 비교 연구

#### An Updated Performance Comparison of Virtual Machines and Linux Containers

- **저자:** Wes Felter, Alexandre Ferreira, Ram Rajamony, Juan Rubio
- **학회:** IEEE ISPASS 2015
- **핵심 기여:** KVM(하이퍼바이저)과 Docker(컨테이너)의 성능을 체계적으로 비교. 거의 모든 경우에서 컨테이너가 VM과 동등하거나 더 나은 성능을 달성하며, 둘 다 I/O 집약적 워크로드에서 튜닝이 필요함을 보인다.
- **Firecracker와의 관계:** Firecracker가 해결하려는 VM vs 컨테이너 성능 격차의 정량적 기준선을 제공한다.

#### Containers and Virtual Machines at Scale: A Comparative Study

- **저자:** Prateek Sharma, Lucas Chaufournier, Prashant Shenoy, Y. C. Tay
- **학회:** Middleware 2016
- **핵심 기여:** 대규모 데이터센터 환경에서 컨테이너와 VM을 성능, 관리 용이성, 소프트웨어 개발 측면에서 비교한다. 컨테이너는 5MB RAM으로 충분하지만 VM은 최소 250MB가 필요하며, 특정 워크로드에서 컨테이너의 공동 배치 간섭이 더 크다.
- **Firecracker와의 관계:** Firecracker의 ~3MB 메모리 오버헤드가 이 격차를 어떻게 줄이는지 이해하는 맥락을 제공한다.

#### Peeking Behind the Curtains of Serverless Platforms

- **저자:** Liang Wang, Mengyuan Li, Yinqian Zhang, Thomas Ristenpart, Michael Swift
- **학회:** USENIX ATC 2018
- **핵심 기여:** AWS Lambda, Azure Functions, Google Cloud Functions에서 5만+ 함수 인스턴스를 실행한 최대 규모 측정 연구. 아키텍처, 콜드 스타트 지연, 리소스 관리를 분석하고, AWS가 빈 패킹 유사 VM 할당을 사용하며 함수 간 심각한 경합이 발생함을 발견한다.
- **Firecracker와의 관계:** Firecracker 이전의 AWS Lambda 아키텍처를 분석하여, Firecracker 도입의 동기를 이해하는 데 핵심적이다.

#### Performance Evaluation of Container-based Virtualization for HPC

- **저자:** Miguel Gomes Xavier, Marcelo Veiga Neves, Fabio Diniz Rossi, Tiago C. Ferreto, Timoteo Lange, Cesar A. F. De Rose
- **학회:** IEEE PDP 2013
- **핵심 기여:** Linux VServer, OpenVZ, LXC와 Xen을 HPC 워크로드에 대해 비교. 컨테이너는 거의 네이티브 성능을 달성하는 반면 Xen은 하이퍼바이저 메모리 접근 변환으로 평균 31% 오버헤드를 보인다.

---

### 4.6 컨테이너 설계 패턴 및 역사

#### Design Patterns for Container-based Distributed Systems

- **저자:** Brendan Burns, David Oppenheimer
- **학회:** HotCloud 2016
- **핵심 기여:** 컨테이너 기반 분산 시스템의 설계 패턴을 식별/형식화한다. 단일 컨테이너(관리), 단일 노드(sidecar, ambassador, adapter), 다중 노드(분산 알고리즘) 패턴으로 분류한다. 클라우드 네이티브 아키텍처의 기초 어휘를 확립한다.
- **Firecracker와의 관계:** MicroVM 기반 아키텍처에서 sidecar 패턴(모니터링, 로깅)의 적용 방식을 이해하는 데 핵심이다.

#### The Ideal Versus the Real: Revisiting the History of Virtual Machines and Containers

- **저자:** Allison Randal
- **학회:** ACM Computing Surveys, Vol. 53, No. 1, 2020
- **핵심 기여:** 1950년대부터 현대 컨테이너까지의 포괄적 역사적 조사. Solaris Zones, FreeBSD Jails, Linux VServer, OpenVZ 및 현대 기술을 다룬다. "VM은 더 안전하고 컨테이너는 더 빠르다"는 일반적 인식에 도전한다.
- **Firecracker와의 관계:** VM과 컨테이너의 격리 모델이 어떻게 수렴해왔는지 역사적 맥락을 제공하며, Firecracker가 이 수렴의 산물임을 보여준다.

#### Namespace-Cgroup Desynchronization in Containers

- **학회:** NDSS Symposium
- **핵심 기여:** 네임스페이스-cgroup 비동기화(NCD)라는 컨테이너의 새로운 보안 위험을 발견한다. 네임스페이스 공유가 격리 경계를 확장하는 반면 cgroup 보호는 인스턴스별로 유지되는 불일치를 식별한다. 4개의 새로운 취약점을 발견한다.
- **Firecracker와의 관계:** 컨테이너 격리의 구조적 취약점을 보여주며, VM 기반 격리(Firecracker)의 필요성을 뒷받침한다.

---

### 4.7 자원 관리 및 성능 격리

#### Heracles: Improving Resource Efficiency at Scale

- **저자:** David Lo, Liqun Cheng, Rama Govindaraju, Parthasarathy Ranganathan, Christos Kozyrakis
- **학회:** ISCA 2015
- **핵심 기여:** 지연 민감 워크로드(LC)와 배치 워크로드(BE)를 동일 서버에서 안전하게 공동 실행하는 리소스 관리자이다. LLC, 메모리 대역폭, 네트워크, 전력을 동적으로 파티셔닝하여 LC의 QoS를 유지하면서 서버 활용률을 90%까지 높인다.
- **Firecracker와의 관계:** Lambda 워커에서 여러 MicroVM을 공동 배치할 때의 성능 간섭 관리에 직접 적용 가능하다.

#### Quasar: Resource-Efficient and QoS-Aware Cluster Management

- **저자:** Christina Delimitrou, Christos Kozyrakis
- **학회:** ASPLOS 2014
- **핵심 기여:** ML 기반 리소스 할당으로, 협업 필터링을 사용하여 워크로드의 리소스 요구사항을 자동 분류한다. 수동 할당 대비 서버 수를 47% 줄이면서 성능 제약을 충족한다.
- **Firecracker와의 관계:** MicroVM의 자동 리소스 할당에 활용 가능한 ML 기반 접근이다.

#### Shenango: Achieving High CPU Efficiency for Latency-sensitive Datacenter Workloads

- **저자:** Amy Ousterhout, Joshua Fried, Jonathan Behrens, Adam Belay, Hari Balakrishnan
- **학회:** NSDI 2019
- **핵심 기여:** 마이크로초 단위로 코어를 재할당하여 지연 민감 워크로드의 CPU 효율을 극대화한다. 꼬리 지연을 낮게 유지하면서 유휴 코어를 배치 작업에 할당한다.
- **Firecracker와의 관계:** MicroVM 간 CPU 코어의 마이크로초 단위 재할당은 서버리스 워크로드의 빈번한 유휴/활성 전환에 이상적이다.

---

### 4.8 마이크로서비스 관측/디버깅

#### DeathStarBench: An Open-Source Benchmark Suite for Cloud Microservices

- **저자:** Yu Gan, Yanqi Zhang, Dailun Cheng, Ankitha Shetty, Priber Rathi, Nayan Katarki, Arber Bruno, Justin Hu, Brian Ritchken, Brendon Jackson, Kelvin Hu, Meghna Pancholi, Yuan He, Brett Clancy, Chris Colen, Fukang Wen, Catherine Leung, Siyuan Wang, Leon Zaruvinsky, Mateo Espinosa, Rick Lin, Zhongling Liu, Jake Padilla, Christina Delimitrou
- **학회:** ASPLOS 2019
- **핵심 기여:** 클라우드 마이크로서비스를 위한 오픈소스 벤치마크 스위트. 소셜 네트워크, 미디어 서비스, 호텔 예약 등 실제 워크로드를 포함하며, 마이크로서비스 아키텍처의 성능 특성을 연구하는 표준 플랫폼을 제공한다.
- **Firecracker와의 관계:** MicroVM 기반 서버리스/컨테이너 아키텍처의 성능을 평가하는 표준 벤치마크로 활용된다.

#### Seer: Leveraging Big Data to Navigate the Complexity of Performance Debugging in Cloud Microservices

- **저자:** Yu Gan, Yanqi Zhang, Kelvin Hu, Dailun Cheng, Yuan He, Meghna Pancholi, Christina Delimitrou
- **학회:** ASPLOS 2019
- **핵심 기여:** 딥러닝으로 마이크로서비스의 성능 이슈를 진단하는 시스템. QoS 위반을 예측하고 근본 원인을 식별한다.
- **Firecracker와의 관계:** MicroVM 위 마이크로서비스의 성능 문제를 진단하는 방법론을 제공한다.

#### Sage: Practical and Scalable ML-Driven Performance Debugging in Microservices

- **저자:** Yu Gan, Mingyu Liang, Sundar Dev, David Lo, Christina Delimitrou
- **학회:** ASPLOS 2021
- **핵심 기여:** ML 기반의 확장 가능한 마이크로서비스 성능 디버깅 시스템. Seer를 확장하여 더 큰 규모의 시스템에서 근본 원인 분석을 자동화한다.
- **Firecracker와의 관계:** 대규모 MicroVM 배포에서의 성능 이상 탐지에 활용 가능하다.

---

### 4.9 컨테이너 마이그레이션

#### MigrOS: Transparent Live Migration Support for Containerised RDMA Applications

- **학회:** USENIX ATC 2021
- **핵심 기여:** CRIU를 활용한 컨테이너 실시간 마이그레이션에 RDMA 지원을 추가한다. 네트워크 연결을 유지하면서 투명한 컨테이너 이동을 달성한다.
- **Firecracker와의 관계:** MicroVM의 실시간 마이그레이션에 활용 가능한 체크포인트/복원 기법을 제공한다. Firecracker는 VM 마이그레이션을 지원하지 않으므로 CRIU 기반 접근이 대안이 될 수 있다.

---

### 4.10 멀티 클러스터/페더레이션

#### mck8s: An Orchestration Platform for Geo-Distributed Multi-Cluster Environments

- **저자:** Mulugeta Tamiru, Guillaume Pierre, Johan Tordsson, Erik Elmroth
- **학회:** ICCCN 2021
- **핵심 기여:** 지리적으로 분산된 Kubernetes 클러스터의 다중 클러스터 오케스트레이션. 정책 기반 스케줄링, 오토스케일링, 클러스터 프로비저닝을 제공한다. 보류 파드를 Kubernetes Federation의 65%에서 6%로 감소시킨다.
- **Firecracker와의 관계:** 글로벌 서버리스 플랫폼의 리전 간 MicroVM 배포 전략에 적용 가능하다.

#### Geo-distributed Efficient Deployment of Containers with Kubernetes (ge-kube)

- **저자:** Fabiana Rossi, Valeria Cardellini, Francesco Lo Presti, Matteo Nardelli
- **학회:** Computer Communications, Vol. 159, 2020
- **핵심 기여:** 모델 기반 강화학습과 네트워크 인식 배치 휴리스틱으로 Kubernetes를 확장한다. 분산 노드 간 비무시적 네트워크 지연을 고려한 배치를 달성한다.

---

### 4.11 서비스 메시

#### Lightweight Sidecar for Serverless Service Mesh

- **학회:** SoCC 2025
- **핵심 기여:** 서버리스 환경에서 서비스 메시의 사이드카 프록시를 경량화한다. 기존 Envoy 사이드카의 과도한 오버헤드를 줄여 서버리스 함수 호출에 적합한 경량 프록시를 제공한다.
- **Firecracker와의 관계:** MicroVM 내 사이드카 패턴의 오버헤드를 줄여 서버리스 환경에서의 서비스 메시 적용 가능성을 높인다.

#### Service Mesh mTLS Performance Comparison

- **출처:** arXiv 2024
- **핵심 기여:** Istio, Linkerd 등 주요 서비스 메시의 mTLS 성능을 체계적으로 비교 분석한다. 각 서비스 메시의 암호화 오버헤드와 지연 특성을 정량화한다.

#### eBPF Kernel Extensions for Packet Processing (hXDP)

- **학회:** SIGCOMM 2021
- **핵심 기여:** eBPF 기반 고성능 패킷 처리 프레임워크. 커널 우회 없이 고성능 네트워킹을 달성한다.
- **Firecracker와의 관계:** MicroVM의 네트워크 성능 개선에 eBPF 기반 패킷 처리를 적용할 수 있다.

#### Container Networking Performance (HotConNet)

- **학회:** SIGCOMM HotConNet 2017
- **핵심 기여:** 컨테이너 네트워킹 성능을 분석하고 오버헤드의 원인을 식별한다. veth, bridge, overlay 네트워크의 성능 특성을 비교한다.
- **Firecracker와의 관계:** Firecracker의 tap 인터페이스 기반 네트워킹과 컨테이너 네트워킹의 성능 차이를 이해하는 맥락을 제공한다.

---

## 5. 비교 요약 테이블

### 격리 기술별 비교

| 격리 기술 | 대표 시스템 | 부팅 시간 | 메모리 오버헤드 | 보안 수준 | 호환성 |
|-----------|-----------|----------|---------------|----------|--------|
| MicroVM (KVM) | Firecracker | ~125ms | ~3MB | 높음 (HW 격리) | 높음 (Linux) |
| MicroVM (Xen) | LightVM | ~2.3ms | ~수 MB | 높음 | 중간 |
| 사용자 공간 커널 | gVisor | ~수백ms | ~15MB | 중상 | 중상 |
| Library OS + seccomp | Nabla | ~수십ms | ~수 MB | 중상 | 중간 |
| 유니커널 | Unikraft | ~1ms | ~1MB | 높음 | 낮음 |
| Wasm SFI | FAASM/Sledge | ~수 μs | ~수 KB | 중간 | 낮음 (Wasm) |
| V8 Isolate | Cloudflare Workers | ~0ms | ~수 KB | 중간 | 낮음 (JS/Wasm) |
| SFI (소프트웨어) | LFI | ~수 μs | ~수 KB | 중간 | 중간 |
| HW 함수 격리 | HFI | ~수 ns | ~0 | 높음 | 높음 (제안) |
| 컨테이너 | Docker/RunC | ~수백ms | ~수 MB | 낮음 | 높음 |
| SGX 엔클레이브 | Occlum | ~수백ms | 제한적 | 매우 높음 | 중간 |
| CVM | Wallet/SEV-SNP | ~수백ms | ~수 MB | 매우 높음 | 높음 |

### 논문 분류별 요약

| # | 논문 | 학회 | 분류 | 접근 방식 |
|---|------|------|------|----------|
| 1 | LightVM | SOSP'17 | 유사 | Xen 경량 VM |
| 2 | X-Containers | ASPLOS'19 | 유사 | Exokernel + LibOS |
| 3 | OSv | ATC'14 | 유사 | VM 최적화 게스트 OS |
| 4 | Cloud Hypervisor | Industry | 유사 | Rust VMM (범용) |
| 5 | NEMU | Industry | 유사 | QEMU 경량화 |
| 6 | gVisor | Google'18 | 유사 | 사용자 공간 커널 |
| 7 | Nabla Containers | HotCloud'18 | 유사 | LibOS + 7 syscalls |
| 8 | MirageOS | ASPLOS'13 | 유사 | OCaml 유니커널 |
| 9 | Unikraft | EuroSys'21 | 유사 | 모듈러 유니커널 |
| 10 | Unikernels as Processes | SoCC'18 | 유사 | seccomp 유니커널 |
| 11 | Unikernels Ready? | IC2E'24 | 유사 | 엣지 유니커널 비교 |
| 12 | SOCK | ATC'18 | 유사 | 서버리스 컨테이너 최적화 |
| 13 | Nightcore | ASPLOS'21 | 유사 | 마이크로초 서버리스 |
| 14 | SEUSS | EuroSys'20 | 유사 | 유니커널 스냅샷 |
| 15 | Prebaking | Middleware'20 | 유사 | JVM 상태 영속화 |
| 16 | Photons | SoCC'20 | 유사 | 함수 공동 배치 |
| 17 | AQUATOPE | ASPLOS'23 | 유사 | 베이지안 사전 웜업 |
| 18 | Catalyzer | ASPLOS'20 | 확장 | Init-less 부팅 |
| 19 | REAP/vHive | ASPLOS'21 | 확장 | 스냅샷 프리페치 |
| 20 | Fireworks | EuroSys'22 | 확장 | Post-JIT 스냅샷 |
| 21 | Slacker | FAST'16 | 보완 | 지연 이미지 로딩 |
| 22 | FC uArch Security | ICISS'24 | 보완 | 사이드 채널 분석 |
| 23 | Occlum | ASPLOS'20 | 대안 | SGX 멀티태스킹 |
| 24 | Wallet | NSDI'26 | 확장 | 기밀 서버리스 |
| 25 | CVMs Explained | SIGMETRICS'25 | 보완 | SEV/TDX 분석 |
| 26 | HECKLER | Security'24 | 보완 | CVM 공격 |
| 27 | Theseus | OSDI'20 | 유사 | Rust OS |
| 28 | RedLeaf | OSDI'20 | 대안 | Rust 언어 격리 |
| 29 | RustBelt | POPL'18 | 기반 | Rust 형식 검증 |
| 30 | CNTR | ATC'18 | 보완 | 컨테이너 축소 |
| 31 | Security Namespace | Security'18 | 보완 | 컨테이너 보안 정책 |
| 32 | DADI | ATC'20 | 보완 | 블록 수준 지연 로딩 |
| 33 | Starlight | NSDI'22 | 보완 | 엣지 프로비저닝 |
| 34 | RunD | ATC'22 | 반대 | 초경량 보안 컨테이너 |
| 35 | Blending VMs/Containers | VEE'20 | 반대 | FC/gVisor 비교 |
| 36 | SAND | ATC'18 | 반대 | 프로세스 수준 격리 |
| 37 | FAASM | ATC'20 | 반대 | Wasm SFI |
| 38 | Sledge | Middleware'20 | 반대 | 엣지 Wasm |
| 39 | Cloudflare Workers | Industry'18 | 반대 | V8 Isolates |
| 40 | Not So Fast | ATC'19 | 균형 | Wasm 성능 한계 |
| 41 | Isolation w/o Taxation | POPL'22 | 반대 | SFI 제로 비용 |
| 42 | Drawbridge | ASPLOS'11 | 반대 | picoprocess + LibOS |
| 43 | Graphene(-SGX) | EuroSys'14/ATC'17 | 대안 | Library OS + SGX |
| 44 | Bascule | EuroSys'13 | 반대 | OS 확장 |
| 45 | LFI | ASPLOS'24 | 반대 | SW 결함 격리 |
| 46 | HFI | ASPLOS'23 | 반대 | HW 함수 격리 |
| 47 | CAP-VMs | OSDI'22 | 대안 | 능력 기반 격리 |
| 48 | Berkeley View | UCB'19 | 비판 | 서버리스 한계 |
| 49 | Prime Video | Industry'23 | 비판 | 모놀리스 전환 |
| 50 | Edge Wasm | CCGrid'22 | 반대 | 엣지 Wasm |
| 51 | XFaaS | SOSP'23 | 대안 | Meta 서버리스 |
| 52 | Dirigent | SOSP'24 | 확장 | 경량 오케스트레이션 |
| 53 | Hermod | SoCC'22 | 확장 | 서버리스 스케줄링 |
| 54 | Fifer | Middleware'20 | 확장 | 자원 활용 최적화 |
| 55 | Palette | EuroSys'23 | 확장 | 지역성 힌트 |
| 56 | Iluvatar | HPDC'23 | 확장 | 빠른 제어 플레인 |
| 57 | Jolteon | NSDI'24 | 확장 | 워크플로 오케스트레이션 |
| 58 | Faastlane | ATC'21 | 반대 | 프로세스 내 워크플로 |
| 59 | SONIC | ATC'21 | 확장 | 데이터 전달 최적화 |
| 60 | WISEFUSE | SIGMETRICS'22 | 확장 | DAG 변환 (Best Paper) |
| 61 | Pheromone | NSDI'23 | 확장 | 앱 인식 스케줄링 |
| 62 | Unum | NSDI'23 | 확장 | 탈중앙 워크플로 |
| 63 | Boki | SOSP'21 | 확장 | 상태 유지 서버리스 |
| 64 | Halfmoon | SOSP'23 | 확장 | 로그 최적 상태 |
| 65 | Beldi | OSDI'20 | 확장 | 트랜잭션 서버리스 |
| 66 | Durable Functions | OOPSLA'21 | 확장 | 상태 시맨틱 형식화 |
| 67 | Cloudburst | VLDB'20 | 확장 | 상태 유지 FaaS |
| 68 | Kalium | Security'23 | 보완 | 서버리스 CFI |
| 69 | Trapeze | OOPSLA'18 | 보완 | 정보 흐름 제어 |
| 70 | Molecule | ASPLOS'22 | 확장 | 이기종 서버리스 |
| 71 | StreamBox | ATC'24 | 확장 | GPU 샌드박스 |
| 72 | INFless | ASPLOS'22 | 확장 | ML 추론 서버리스 |
| 73 | ServerlessLLM | OSDI'24 | 확장 | LLM 서빙 |
| 74 | UPM | BigData'23 | 확장 | 메모리 중복 제거 |
| 75 | Kata Comparison | CC'22 | 보완 | 격리 기술 비교 |
| 76 | Network Startup | IMC'24 | 보완 | 네트워크 병목 |
| 77 | CKI | EuroSys'25 | 반대 | PKS 기반 커널 격리 |
| 78 | PVM | SOSP'23 | 확장 | 중첩 보안 컨테이너 |
| 79 | RContainer | NDSS'25 | 대안 | ARM CCA 컨테이너 |
| 80 | eBPF Syscall Security | arXiv'23 | 보완 | eBPF 보안 정책 |
| 81 | eBPF Attacks | Security'23 | 보완 | 크로스 컨테이너 공격 |
| 82 | BPFContain | 2021 | 보완 | eBPF 컨테이너 보안 |
| 83 | Rex | ATC'25 | 보완 | Rust 커널 확장 |
| 84 | seL4 VMM | MDPI'22 | 대안 | 마이크로커널 VMM |
| 85 | Xen vs KVM | Xen Summit'08 | 기반 | 하이퍼바이저 비교 |

---

## 6. 참고문헌

### 기준 논문
[1] A. Agache, M. Brooker, A. Florescu, A. Iordache, A. Liguori, R. Neugebauer, P. Piwonka, and D.-M. Popa, "Firecracker: Lightweight Virtualization for Serverless Applications," in *Proc. 17th USENIX NSDI*, 2020, pp. 419–434.

### 경량 VMM / MicroVM
[2] F. Manco et al., "My VM is Lighter (and Safer) than your Container," in *Proc. 26th ACM SOSP*, 2017, pp. 218–233.
[3] Z. Shen et al., "X-Containers: Breaking Down Barriers to Improve Performance and Isolation of Cloud-Native Containers," in *Proc. 24th ACM ASPLOS*, 2019.
[4] A. Kivity et al., "OSv—Optimizing the Operating System for Virtual Machines," in *Proc. USENIX ATC*, 2014.
[5] Intel, "Cloud Hypervisor," 2019. https://github.com/cloud-hypervisor/cloud-hypervisor
[6] Intel, "NEMU: Modern Hypervisor for the Cloud," 2018. https://github.com/intel/nemu (archived)

### 샌드박스 기반 격리
[7] Google, "gVisor: Container Runtime Sandbox," 2018. https://gvisor.dev/
[8] D. Williams and R. Koller, "Unikernel Monitors: Extending Minimalism Outside of the Box," in *Proc. USENIX HotCloud*, 2018.

### 유니커널
[9] A. Madhavapeddy et al., "Unikernels: Library Operating Systems for the Cloud," in *Proc. 18th ACM ASPLOS*, 2013, pp. 461–472.
[10] S. Kuenzer et al., "Unikraft: Fast, Specialized Unikernels the Easy Way," in *Proc. 16th ACM EuroSys*, 2021. **(Best Paper)**
[11] D. Williams et al., "Unikernels as Processes," in *Proc. ACM SoCC*, 2018.
[12] "Are Unikernels Ready for Serverless on the Edge?," in *Proc. IEEE IC2E*, 2024.

### 서버리스 콜드 스타트 최적화
[13] E. Oakes et al., "SOCK: Rapid Task Provisioning with Serverless-Optimized Containers," in *Proc. USENIX ATC*, 2018.
[14] Z. Jia and E. Witchel, "Nightcore: Efficient and Scalable Serverless Computing for Latency-Sensitive, Interactive Microservices," in *Proc. 26th ACM ASPLOS*, 2021.
[15] J. Cadden et al., "SEUSS: Skip Redundant Paths to Make Serverless Fast," in *Proc. 15th ACM EuroSys*, 2020.
[16] P. Silva, D. Fireman, and T. E. Pereira, "Prebaking Functions to Warm the Serverless Cold Start," in *Proc. ACM Middleware*, 2020.
[17] V. Dukic et al., "Photons: Lambdas on a Diet," in *Proc. ACM SoCC*, 2020.
[18] Z. Zhou, Y. Zhang, and C. Delimitrou, "AQUATOPE: QoS-and-Uncertainty-Aware Resource Management for Multi-stage Serverless Workflows," in *Proc. 28th ACM ASPLOS*, 2023.

### 스냅샷/복원
[19] D. Du et al., "Catalyzer: Sub-millisecond Startup for Serverless Computing with Initialization-less Booting," in *Proc. 25th ACM ASPLOS*, 2020.
[20] D. Ustiugov et al., "Benchmarking, Analysis, and Optimization of Serverless Function Snapshots," in *Proc. 26th ACM ASPLOS*, 2021.
[21] W. Shin, W.-H. Kim, and C. Min, "Fireworks: A Fast, Efficient, and Safe Serverless Framework using VM-level post-JIT Snapshot," in *Proc. 17th ACM EuroSys*, 2022.
[22] T. Harter et al., "Slacker: Fast Distribution with Lazy Docker Containers," in *Proc. 14th USENIX FAST*, 2016.

### 기밀 컴퓨팅
[23] Z. Weissman et al., "Microarchitectural Security of AWS Firecracker VMM for Serverless Cloud Platforms," in *Proc. ICISS*, 2024.
[24] Y. Shen et al., "Occlum: Secure and Efficient Multitasking Inside a Single Enclave of Intel SGX," in *Proc. 25th ACM ASPLOS*, 2020.
[25] P. Sabanic et al., "Wallet: Confidential Serverless Computing," in *Proc. USENIX NSDI*, 2026.
[26] M. Misono et al., "Confidential VMs Explained: An Empirical Analysis of AMD SEV-SNP and Intel TDX," in *ACM SIGMETRICS*, 2025.
[27] B. Schluter et al., "HECKLER: Breaking Confidential VMs with Malicious Interrupt Injection," in *Proc. USENIX Security*, 2024.

### Rust 기반 시스템
[28] K. Boos et al., "Theseus: An Experiment in Operating System Structure and State Management," in *Proc. 14th USENIX OSDI*, 2020.
[29] V. Narayanan et al., "RedLeaf: Isolation and Communication in a Safe Operating System," in *Proc. 14th USENIX OSDI*, 2020.
[30] R. Jung et al., "RustBelt: Securing the Foundations of the Rust Programming Language," in *Proc. ACM POPL*, 2018.
[31] A. Balasubramanian et al., "System Programming in Rust: Beyond Safety," in *Proc. HotOS*, 2017.
[32] B. Qin et al., "Understanding Real-World Concurrency Bugs in Go and Rust," in *Proc. ACM PLDI*, 2020.

### 컨테이너/VM 최적화
[33] J. Thalheim et al., "CNTR: Lightweight OS Containers," in *Proc. USENIX ATC*, 2018.
[34] Y. Sun et al., "Security Namespace: Making Linux Security Frameworks Available to Containers," in *Proc. USENIX Security*, 2018.

### 컨테이너 이미지 최적화
[35] E. Xu et al., "DADI: Block-Level Image Service for Agile and Elastic Application Deployment," in *Proc. USENIX ATC*, 2020.
[36] J. L. Chen et al., "Starlight: Fast Container Provisioning on the Edge and over the WAN," in *Proc. USENIX NSDI*, 2022.

### 반대: 컨테이너 격리
[37] Z. Li et al., "RunD: A Lightweight Secure Container Runtime for High-density Deployment and High-concurrency Startup in Serverless Computing," in *Proc. USENIX ATC*, 2022.
[38] Anjali et al., "Blending Containers and Virtual Machines: A Study of Firecracker and gVisor," in *Proc. 16th ACM VEE*, 2020.
[39] I. E. Akkus et al., "SAND: Towards High-Performance Serverless Computing," in *Proc. USENIX ATC*, 2018.

### 반대: 언어 수준 격리
[40] S. Shillaker and P. Pietzuch, "FAASM: Lightweight Isolation for Efficient Stateful Serverless Computing," in *Proc. USENIX ATC*, 2020.
[41] P. K. Gadepalli et al., "Sledge: A Serverless-first, Light-weight Wasm Runtime for Edge Computing," in *Proc. ACM/IFIP Middleware*, 2020.
[42] Z. Bloom, "Cloud Computing without Containers," Cloudflare Blog, 2018.
[43] A. Jangda et al., "Not So Fast: Analyzing the Performance of WebAssembly vs. Native Code," in *Proc. USENIX ATC*, 2019.
[44] M. Kolosick et al., "Isolation Without Taxation: Near-Zero-Cost Transitions for WebAssembly and SFI," in *Proc. ACM POPL*, 2022.

### 반대: Library OS
[45] D. E. Porter et al., "Rethinking the Library OS from the Top Down," in *Proc. 16th ACM ASPLOS*, 2011. (Drawbridge)
[46] C.-C. Tsai et al., "Cooperation and Security Isolation of Library OSes for Multi-Process Applications," in *Proc. 9th ACM EuroSys*, 2014. (Graphene)
[47] C.-C. Tsai, D. E. Porter, and M. Vij, "Graphene-SGX: A Practical Library OS for Unmodified Applications on SGX," in *Proc. USENIX ATC*, 2017.
[48] A. Baumann et al., "Composing OS Extensions Safely and Efficiently with Bascule," in *Proc. 8th ACM EuroSys*, 2013.

### 반대: 순수 소프트웨어/하드웨어 격리
[49] A. Ghosn et al., "Lightweight Fault Isolation: Practical, Efficient, and Secure Software Sandboxing," in *Proc. 29th ACM ASPLOS*, 2024.
[50] S. Narayan et al., "Going Beyond the Limits of SFI: Flexible and Secure Hardware-Assisted In-Process Isolation with HFI," in *Proc. 28th ACM ASPLOS*, 2023.
[51] V. A. Sartakov et al., "CAP-VMs: Capability-Based Isolation and Sharing in the Cloud," in *Proc. 16th USENIX OSDI*, 2022.

### 서버리스 비판
[52] E. Jonas et al., "Cloud Programming Simplified: A Berkeley View on Serverless Computing," Technical Report UCB/EECS-2019-3, UC Berkeley, 2019.
[53] M. Kolny, "Scaling up the Prime Video audio/video monitoring service and reducing costs by 90%," Amazon Prime Video Tech Blog, 2023.

### 엣지/IoT
[54] "Pushing Serverless to the Edge with WebAssembly Runtimes," in *Proc. IEEE/ACM CCGrid*, 2022.

### 서버리스 스케줄링/오케스트레이션
[55] A. Sahraei et al., "XFaaS: Hyperscale and Low Cost Serverless Functions at Meta," in *Proc. 29th ACM SOSP*, 2023.
[56] L. Cvetkovic et al., "Dirigent: Lightweight Serverless Orchestration," in *Proc. 30th ACM SOSP*, 2024.
[57] K. Kaffes et al., "Hermod: Principled and Practical Scheduling for Serverless Functions," in *Proc. ACM SoCC*, 2022.
[58] J. R. Gunasekaran et al., "Fifer: Tackling Resource Underutilization in the Serverless Era," in *Proc. ACM Middleware*, 2020.
[59] M. Abdi et al., "Palette Load Balancing: Locality Hints for Serverless Functions," in *Proc. 18th ACM EuroSys*, 2023.
[60] A. Fuerst et al., "Iluvatar: A Fast Control Plane for Serverless Computing," in *Proc. ACM HPDC*, 2023.
[61] Z. Zhang, C. Jin, and X. Jin, "Jolteon: Unleashing the Promise of Serverless for Serverless Workflows," in *Proc. USENIX NSDI*, 2024.

### 서버리스 워크플로/함수 합성
[62] S. Kotni et al., "Faastlane: Accelerating Function-as-a-Service Workflows," in *Proc. USENIX ATC*, 2021.
[63] A. Mahgoub et al., "SONIC: Application-aware Data Passing for Chained Serverless Applications," in *Proc. USENIX ATC*, 2021.
[64] A. Mahgoub et al., "WISEFUSE: Workload Characterization and DAG Transformation for Serverless Workflows," in *ACM SIGMETRICS*, 2022. **(Best Paper)**
[65] H. Yu et al., "Pheromone: Application-Aware Serverless Function Scheduling," in *Proc. USENIX NSDI*, 2023.
[66] D. Liu et al., "Unum: Cloud Serverless Application Coordination," in *Proc. USENIX NSDI*, 2023.

### 서버리스 상태 관리
[67] Z. Jia and E. Witchel, "Boki: Stateful Serverless Computing with Shared Logs," in *Proc. 28th ACM SOSP*, 2021.
[68] S. Qi, X. Liu, and X. Jin, "Halfmoon: Log-Optimal Fault-Tolerant Stateful Serverless Computing," in *Proc. 29th ACM SOSP*, 2023.
[69] H. Zhang et al., "Beldi: Fault-tolerant and Transactional Stateful Serverless Workflows," in *Proc. 14th USENIX OSDI*, 2020.
[70] S. Burckhardt et al., "Durable Functions: Semantics for Stateful Serverless," in *Proc. ACM OOPSLA*, 2021.
[71] V. Sreekanti et al., "Cloudburst: Stateful Functions-as-a-Service," in *Proc. VLDB*, 2020.

### 서버리스 보안
[72] D. S. Jegan et al., "Guarding Serverless Applications with Kalium," in *Proc. USENIX Security*, 2023.
[73] K. Alpernas et al., "Secure Serverless Computing Using Dynamic Information Flow Control," in *Proc. ACM OOPSLA*, 2018.

### GPU/가속기 서버리스
[74] D. Du et al., "Molecule: Serverless Computing on Heterogeneous Computers," in *Proc. 27th ACM ASPLOS*, 2022.
[75] H. Wu et al., "StreamBox: A Lightweight GPU Sandbox for Serverless Inference Workflow," in *Proc. USENIX ATC*, 2024.
[76] Y. Yang et al., "INFless: A Native Serverless System for Low-Latency, High-Throughput Inference," in *Proc. 27th ACM ASPLOS*, 2022.
[77] Y. Fu et al., "ServerlessLLM: Locality-Enhanced Serverless Inference for Large Language Models," in *Proc. 18th USENIX OSDI*, 2024.

### 메모리 최적화
[78] W. Qiu et al., "User-guided Page Merging for Memory Deduplication in Serverless Systems," in *Proc. IEEE BigData*, 2023.

### 보안 컨테이너
[79] Wang et al., "A Comparative Study of RunC, gVisor, Kata Containers," *Cluster Computing*, 2022.
[80] Liu et al., "Network Startup Bottleneck in Secure Container Runtimes," in *Proc. ACM IMC*, 2024.
[81] Shi et al., "CKI: Container Kernel Isolation using PKS," in *Proc. ACM EuroSys*, 2025.
[82] Huang et al., "PVM: Shadow Paging for Nested Secure Containers," in *Proc. 29th ACM SOSP*, 2023.
[83] Zhou et al., "RContainer: ARM CCA-based Secure Containers," in *Proc. NDSS*, 2025.

### eBPF 기반 보안
[84] Jia et al., "Programmable System Call Security with eBPF," arXiv, 2023.
[85] He et al., "Cross-container eBPF Attacks," in *Proc. USENIX Security*, 2023.
[86] Findlay et al., "BPFContain: eBPF-based Container Security," 2021.
[87] Jia et al., "Rex: Rust Kernel Extensions Replacing eBPF," in *Proc. USENIX ATC*, 2025.

### 서버리스 네트워킹
[88] Aditya et al., "Serverless for Network Function Virtualization," *Proc. IEEE*, 2019.
[89] Shen et al., "Serpens: NFV Platform with Serverless," in *Proc. IWQoS*, 2020.

### 마이크로커널/하이퍼바이저
[90] de Matos and Ahvenjarvi, "seL4 as VMM," *Electronics/MDPI*, 2022.
[91] Deshane et al., "Quantitative Comparison of Xen and KVM," *Xen Summit*, 2008.
[92] Soriga and Barbulescu, "Scalability Comparison of Xen and KVM," *IEEE ECAI*, 2013.

### 클러스터 관리/오케스트레이션
[93] A. Verma, L. Pedrosa, M. R. Korupolu, D. Oppenheimer, E. Tune, and J. Wilkes, "Large-scale Cluster Management at Google with Borg," in *Proc. 10th ACM EuroSys*, 2015.
[94] M. Schwarzkopf, A. Konwinski, M. Abd-El-Malek, and J. Wilkes, "Omega: Flexible, Scalable Schedulers for Large Compute Clusters," in *Proc. 8th ACM EuroSys*, 2013. **(Best Student Paper)**
[95] B. Burns, B. Grant, D. Oppenheimer, E. Brewer, and J. Wilkes, "Borg, Omega, and Kubernetes," *Communications of the ACM*, Vol. 59, No. 5, 2016.
[96] M. Tirmazi et al., "Borg: the Next Generation," in *Proc. 15th ACM EuroSys*, 2020.
[97] B. Hindman et al., "Mesos: A Platform for Fine-Grained Resource Sharing in the Data Center," in *Proc. 8th USENIX NSDI*, 2011.
[98] A. Ghodsi et al., "Dominant Resource Fairness: Fair Allocation of Multiple Resource Types," in *Proc. 8th USENIX NSDI*, 2011.
[99] V. K. Vavilapalli et al., "Apache Hadoop YARN: Yet Another Resource Negotiator," in *Proc. ACM SoCC*, 2013.
[100] C. Tang et al., "Twine: A Unified Cluster Management System for Shared Infrastructure," in *Proc. 14th USENIX OSDI*, 2020.

### 클러스터 스케줄링
[101] K. Ousterhout, P. Wendell, M. Zaharia, and I. Stoica, "Sparrow: Distributed, Low Latency Scheduling," in *Proc. 24th ACM SOSP*, 2013.
[102] I. Gog, M. Schwarzkopf, A. Gleave, R. N. M. Watson, and S. Hand, "Firmament: Fast, Centralized Cluster Scheduling at Scale," in *Proc. 12th USENIX OSDI*, 2016.
[103] R. Grandl, G. Ananthanarayanan, S. Kandula, S. Rao, and A. Akella, "Multi-Resource Packing for Cluster Schedulers (Tetris)," in *Proc. ACM SIGCOMM*, 2014.
[104] M. Isard et al., "Quincy: Fair Scheduling for Distributed Computing Clusters," in *Proc. 22nd ACM SOSP*, 2009.
[105] M. Zaharia et al., "Delay Scheduling: A Simple Technique for Achieving Locality and Fairness in Cluster Scheduling," in *Proc. 5th ACM EuroSys*, 2010.

### Kubernetes 연구
[106] C. Carrion, "Kubernetes Scheduling: Taxonomy, Ongoing Issues and Challenges," *ACM Computing Surveys*, Vol. 55, No. 7, 2022.
[107] K. Senjab et al., "A Survey of Kubernetes Scheduling Algorithms," *Journal of Cloud Computing*, Vol. 12, 2023.
[108] "Acto: Automatic End-to-End Testing for Operation Correctness of Cloud System Management," in *Proc. 29th ACM SOSP*, 2023.

### 오토스케일링
[109] K. Rzadca et al., "Autopilot: Workload Autoscaling at Google," in *Proc. 15th ACM EuroSys*, 2020.
[110] S. Luo et al., "The Power of Prediction: Microservice Auto Scaling via Workload Learning," in *Proc. ACM SoCC*, 2022.
[111] "DeepScaling: Microservices AutoScaling for Stable CPU Utilization in Large Scale Cloud Systems," in *Proc. ACM SoCC*, 2022.

### 컨테이너 vs VM 비교
[112] W. Felter et al., "An Updated Performance Comparison of Virtual Machines and Linux Containers," in *Proc. IEEE ISPASS*, 2015.
[113] P. Sharma et al., "Containers and Virtual Machines at Scale: A Comparative Study," in *Proc. 17th ACM/IFIP Middleware*, 2016.
[114] L. Wang et al., "Peeking Behind the Curtains of Serverless Platforms," in *Proc. USENIX ATC*, 2018.
[115] M. G. Xavier et al., "Performance Evaluation of Container-based Virtualization for HPC Environments," in *Proc. IEEE PDP*, 2013.

### 설계 패턴/역사
[116] B. Burns and D. Oppenheimer, "Design Patterns for Container-based Distributed Systems," in *Proc. USENIX HotCloud*, 2016.
[117] A. Randal, "The Ideal Versus the Real: Revisiting the History of Virtual Machines and Containers," *ACM Computing Surveys*, Vol. 53, No. 1, 2020.
[118] "Losing the Beat: Understanding and Mitigating Namespace-Cgroup Desynchronization in Containers," in *Proc. NDSS Symposium*.

### 자원 관리/성능 격리
[119] D. Lo et al., "Heracles: Improving Resource Efficiency at Scale," in *Proc. 42nd ACM/IEEE ISCA*, 2015.
[120] C. Delimitrou and C. Kozyrakis, "Quasar: Resource-Efficient and QoS-Aware Cluster Management," in *Proc. 19th ACM ASPLOS*, 2014.
[121] A. Ousterhout et al., "Shenango: Achieving High CPU Efficiency for Latency-sensitive Datacenter Workloads," in *Proc. 16th USENIX NSDI*, 2019.

### 마이크로서비스 관측/디버깅
[122] Y. Gan et al., "An Open-Source Benchmark Suite for Cloud Microservices (DeathStarBench)," in *Proc. 24th ACM ASPLOS*, 2019.
[123] Y. Gan et al., "Seer: Leveraging Big Data to Navigate the Complexity of Performance Debugging in Cloud Microservices," in *Proc. 24th ACM ASPLOS*, 2019.
[124] Y. Gan et al., "Sage: Practical and Scalable ML-Driven Performance Debugging in Microservices," in *Proc. 26th ACM ASPLOS*, 2021.

### 컨테이너 마이그레이션/네트워킹/서비스 메시
[125] "MigrOS: Transparent Live Migration Support for Containerised RDMA Applications," in *Proc. USENIX ATC*, 2021.
[126] M. Tamiru et al., "mck8s: An Orchestration Platform for Geo-Distributed Multi-Cluster Environments," in *Proc. IEEE ICCCN*, 2021.
[127] F. Rossi et al., "Geo-distributed Efficient Deployment of Containers with Kubernetes (ge-kube)," *Computer Communications*, Vol. 159, 2020.
[128] "Lightweight Sidecar for Serverless Service Mesh," in *Proc. ACM SoCC*, 2025.
[129] "eBPF Kernel Extensions for Packet Processing (hXDP)," in *Proc. ACM SIGCOMM*, 2021.
[130] "Container Networking Performance," in *Proc. SIGCOMM HotConNet*, 2017.
