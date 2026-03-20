# Firecracker 관련 논문 개별 요약 모음

> **기준 논문:** Agache, A. et al. "Firecracker: Lightweight Virtualization for Serverless Applications," NSDI 2020.
>
> 총 **129편**의 논문 요약 문서 (참고문헌 [1]~[130], [52]는 [48]과 중복)

---

## 1. 기준 논문

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 1 | [001_firecracker.md](001_firecracker.md) | Firecracker: Lightweight Virtualization for Serverless Applications | NSDI 2020 |

## 2. 유사한 접근 (Similar Approaches)

### 2.1 경량 VMM / MicroVM

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 2 | [002_lightvm.md](002_lightvm.md) | LightVM: My VM is Lighter (and Safer) than your Container | SOSP 2017 |
| 3 | [003_x_containers.md](003_x_containers.md) | X-Containers: Breaking Down Barriers | ASPLOS 2019 |
| 4 | [004_osv.md](004_osv.md) | OSv: Optimizing the OS for Virtual Machines | ATC 2014 |
| 5 | [005_cloud_hypervisor.md](005_cloud_hypervisor.md) | Intel Cloud Hypervisor | Industry 2019~ |
| 6 | [006_nemu.md](006_nemu.md) | Intel NEMU | Industry 2018 |

### 2.2 샌드박스 기반 격리

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 7 | [007_gvisor.md](007_gvisor.md) | gVisor: Container Runtime Sandbox | Google 2018 |
| 8 | [008_nabla_containers.md](008_nabla_containers.md) | Nabla Containers | HotCloud 2018 |

### 2.3 유니커널

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 9 | [009_mirageos.md](009_mirageos.md) | MirageOS: Library OS for the Cloud | ASPLOS 2013 |
| 10 | [010_unikraft.md](010_unikraft.md) | Unikraft: Fast, Specialized Unikernels | EuroSys 2021 (Best Paper) |
| 11 | [011_unikernels_as_processes.md](011_unikernels_as_processes.md) | Unikernels as Processes | SoCC 2018 |
| 12 | [012_unikernels_edge.md](012_unikernels_edge.md) | Are Unikernels Ready for Serverless on the Edge? | IC2E 2024 |

### 2.4 서버리스 콜드 스타트 최적화

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 13 | [013_sock.md](013_sock.md) | SOCK: Rapid Task Provisioning | ATC 2018 |
| 14 | [014_nightcore.md](014_nightcore.md) | Nightcore: Efficient Serverless Computing | ASPLOS 2021 |
| 15 | [015_seuss.md](015_seuss.md) | SEUSS: Skip Redundant Paths | EuroSys 2020 |
| 16 | [016_prebaking.md](016_prebaking.md) | Prebaking Functions to Warm Cold Start | Middleware 2020 |
| 17 | [017_photons.md](017_photons.md) | Photons: Lambdas on a Diet | SoCC 2020 |
| 18 | [018_aquatope.md](018_aquatope.md) | AQUATOPE: QoS-Aware Resource Management | ASPLOS 2023 |

### 2.5 스냅샷/복원 기반 빠른 시작

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 19 | [019_catalyzer.md](019_catalyzer.md) | Catalyzer: Sub-millisecond Startup | ASPLOS 2020 |
| 20 | [020_reap.md](020_reap.md) | REAP/vHive: Serverless Function Snapshots | ASPLOS 2021 |
| 21 | [021_fireworks.md](021_fireworks.md) | Fireworks: VM-level post-JIT Snapshot | EuroSys 2022 |
| 22 | [022_slacker.md](022_slacker.md) | Slacker: Lazy Docker Containers | FAST 2016 |

### 2.6 하드웨어 기반 기밀 컴퓨팅

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 23 | [023_microarch_security_firecracker.md](023_microarch_security_firecracker.md) | Microarchitectural Security of Firecracker | ICISS 2024 |
| 24 | [024_occlum_sgx.md](024_occlum_sgx.md) | Occlum: SGX Multitasking | ASPLOS 2020 |
| 25 | [025_wallet_confidential_serverless.md](025_wallet_confidential_serverless.md) | Wallet: Confidential Serverless | NSDI 2026 |
| 26 | [026_cvm_sev_tdx_analysis.md](026_cvm_sev_tdx_analysis.md) | Confidential VMs: SEV-SNP vs TDX | SIGMETRICS 2025 |
| 27 | [027_heckler_cvm_attack.md](027_heckler_cvm_attack.md) | HECKLER: Breaking Confidential VMs | Security 2024 |

### 2.7 Rust 기반 시스템 소프트웨어

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 28 | [028_theseus_rust_os.md](028_theseus_rust_os.md) | Theseus: Rust OS Structure | OSDI 2020 |
| 29 | [029_redleaf_rust_isolation.md](029_redleaf_rust_isolation.md) | RedLeaf: Language-level Isolation | OSDI 2020 |
| 30 | [030_rustbelt_formal_verification.md](030_rustbelt_formal_verification.md) | RustBelt: Rust Formal Verification | POPL 2018 |
| 31 | [031_rust_systems_programming.md](031_rust_systems_programming.md) | System Programming in Rust | HotOS 2017 |
| 32 | [032_rust_go_concurrency_bugs.md](032_rust_go_concurrency_bugs.md) | Real-World Concurrency Bugs in Rust | PLDI 2020 |

### 2.8 컨테이너/VM 최적화

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 33 | [033_cntr_lightweight_containers.md](033_cntr_lightweight_containers.md) | CNTR: Lightweight OS Containers | ATC 2018 |
| 34 | [034_security_namespace.md](034_security_namespace.md) | Security Namespace for Containers | Security 2018 |

### 2.9 컨테이너 이미지 최적화

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 35 | [035_dadi_block_image.md](035_dadi_block_image.md) | DADI: Block-Level Image Service | ATC 2020 |
| 36 | [036_starlight_edge_provisioning.md](036_starlight_edge_provisioning.md) | Starlight: Edge Container Provisioning | NSDI 2022 |

---

## 3. 반대/대안적 접근 (Opposing/Alternative Approaches)

### 3.1 컨테이너 격리만으로 충분하다

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 37 | [037_rund_secure_container.md](037_rund_secure_container.md) | RunD: Lightweight Secure Container | ATC 2022 |
| 38 | [038_blending_containers_vms.md](038_blending_containers_vms.md) | Blending Containers and VMs | VEE 2020 |
| 39 | [039_sand_serverless.md](039_sand_serverless.md) | SAND: High-Performance Serverless | ATC 2018 |

### 3.2 언어 수준 격리 (WebAssembly / V8 Isolates)

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 40 | [040_faasm_wasm_serverless.md](040_faasm_wasm_serverless.md) | FAASM: Wasm Stateful Serverless | ATC 2020 |
| 41 | [041_sledge_edge_wasm.md](041_sledge_edge_wasm.md) | Sledge: Edge Wasm Runtime | Middleware 2020 |
| 42 | [042_cloudflare_v8_isolates.md](042_cloudflare_v8_isolates.md) | Cloudflare Workers (V8 Isolates) | Industry 2018 |
| 43 | [043_wasm_performance.md](043_wasm_performance.md) | Not So Fast: Wasm vs Native | ATC 2019 |
| 44 | [044_sfi_zero_cost.md](044_sfi_zero_cost.md) | Isolation Without Taxation: SFI | POPL 2022 |

### 3.3 Library OS / OS 리팩토링

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 45 | [045_drawbridge_picoprocess.md](045_drawbridge_picoprocess.md) | Drawbridge: Picoprocess + LibOS | ASPLOS 2011 |
| 46 | [046_graphene_libos_sgx.md](046_graphene_libos_sgx.md) | Graphene / Graphene-SGX | EuroSys 2014 / ATC 2017 |
| 47 | [047_bascule_os_extensions.md](047_bascule_os_extensions.md) | Bascule: OS Extensions | EuroSys 2013 |

### 3.4 순수 소프트웨어 격리 (SFI / 하드웨어 확장)

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 49 | [049_lfi_software_sandboxing.md](049_lfi_software_sandboxing.md) | LFI: Software-based Sandboxing | ASPLOS 2024 |
| 50 | [050_hfi_hardware_function_isolation.md](050_hfi_hardware_function_isolation.md) | HFI: Hardware Function Isolation | ASPLOS 2023 |
| 51 | [051_cap_vms.md](051_cap_vms.md) | CAP-VMs: Capability-Based Isolation | OSDI 2022 |

### 3.5 서버리스 모델 비판

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 48 | [048_berkeley_serverless.md](048_berkeley_serverless.md) | Berkeley View on Serverless | UCB 2019 |
| 53 | [053_prime_video_monolith.md](053_prime_video_monolith.md) | Amazon Prime Video: Monolith Migration | Industry 2023 |

### 3.6 엣지/IoT 환경의 대안

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 54 | [054_serverless_edge_wasm.md](054_serverless_edge_wasm.md) | Pushing Serverless to Edge with Wasm | CCGrid 2022 |

---

## 4. 확장 연구 (Extended Research)

### 4.1 서버리스 스케줄링/오케스트레이션

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 55 | [055_xfaas_meta.md](055_xfaas_meta.md) | XFaaS: Meta Hyperscale Serverless | SOSP 2023 |
| 56 | [056_dirigent.md](056_dirigent.md) | Dirigent: Lightweight Orchestration | SOSP 2024 |
| 57 | [057_hermod.md](057_hermod.md) | Hermod: Serverless Scheduling | SoCC 2022 |
| 58 | [058_fifer.md](058_fifer.md) | Fifer: Resource Underutilization | Middleware 2020 |
| 59 | [059_palette.md](059_palette.md) | Palette: Locality Hints for FaaS | EuroSys 2023 |
| 60 | [060_iluvatar.md](060_iluvatar.md) | Iluvatar: Fast Control Plane | HPDC 2023 |
| 61 | [061_jolteon.md](061_jolteon.md) | Jolteon: Serverless Workflows | NSDI 2024 |

### 4.2 서버리스 워크플로/함수 합성

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 62 | [062_faastlane.md](062_faastlane.md) | Faastlane: FaaS Workflow Acceleration | ATC 2021 |
| 63 | [063_sonic.md](063_sonic.md) | SONIC: Data Passing Optimization | ATC 2021 |
| 64 | [064_wisefuse.md](064_wisefuse.md) | WISEFUSE: DAG Transformation | SIGMETRICS 2022 (Best Paper) |
| 65 | [065_pheromone.md](065_pheromone.md) | Pheromone: App-Aware Scheduling | NSDI 2023 |
| 66 | [066_unum.md](066_unum.md) | Unum: Decentralized Coordination | NSDI 2023 |

### 4.3 서버리스 상태 관리

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 67 | [067_boki.md](067_boki.md) | Boki: Stateful Serverless with Shared Logs | SOSP 2021 |
| 68 | [068_halfmoon.md](068_halfmoon.md) | Halfmoon: Log-Optimal Stateful Serverless | SOSP 2023 |
| 69 | [069_beldi.md](069_beldi.md) | Beldi: Transactional Serverless | OSDI 2020 |
| 70 | [070_durable_functions.md](070_durable_functions.md) | Durable Functions: Stateful Semantics | OOPSLA 2021 |
| 71 | [071_cloudburst.md](071_cloudburst.md) | Cloudburst: Stateful FaaS | VLDB 2020 |

### 4.4 서버리스 보안

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 72 | [072_kalium.md](072_kalium.md) | Kalium: Serverless CFI | Security 2023 |
| 73 | [073_trapeze.md](073_trapeze.md) | Trapeze: Dynamic IFC for Serverless | OOPSLA 2018 |

### 4.5 GPU/가속기 서버리스

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 74 | [074_molecule.md](074_molecule.md) | Molecule: Heterogeneous Serverless | ASPLOS 2022 |
| 75 | [075_streambox.md](075_streambox.md) | StreamBox: GPU Sandbox | ATC 2024 |
| 76 | [076_infless.md](076_infless.md) | INFless: ML Inference Serverless | ASPLOS 2022 |
| 77 | [077_serverlessllm.md](077_serverlessllm.md) | ServerlessLLM: LLM Serving | OSDI 2024 |

### 4.6 메모리 최적화

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 78 | [078_upm.md](078_upm.md) | UPM: Page Merging for Serverless | BigData 2023 |

### 4.7 보안 컨테이너

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 79 | [079_container_comparison.md](079_container_comparison.md) | RunC/gVisor/Kata Comparison | Cluster Computing 2022 |
| 80 | [080_network_bottleneck.md](080_network_bottleneck.md) | Network Startup Bottleneck | IMC 2024 |
| 81 | [081_cki.md](081_cki.md) | CKI: Container Kernel Isolation (PKS) | EuroSys 2025 |
| 82 | [082_pvm.md](082_pvm.md) | PVM: Nested Secure Containers | SOSP 2023 |
| 83 | [083_rcontainer.md](083_rcontainer.md) | RContainer: ARM CCA Containers | NDSS 2025 |

### 4.8 eBPF 기반 보안/격리

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 84 | [084_ebpf_syscall_security.md](084_ebpf_syscall_security.md) | eBPF Programmable Syscall Security | arXiv 2023 |
| 85 | [085_ebpf_cross_container.md](085_ebpf_cross_container.md) | Cross-container eBPF Attacks | Security 2023 |
| 86 | [086_bpfcontain.md](086_bpfcontain.md) | BPFContain: eBPF Container Security | 2021 |
| 87 | [087_rex_rust_kernel.md](087_rex_rust_kernel.md) | Rex: Rust Kernel Extensions | ATC 2025 |

### 4.9 서버리스 네트워킹/NFV

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 88 | [088_serverless_nfv.md](088_serverless_nfv.md) | Serverless for NFV | Proc. IEEE 2019 |
| 89 | [089_serpens_nfv.md](089_serpens_nfv.md) | Serpens: Serverless NFV Platform | IWQoS 2020 |

### 4.10 마이크로커널/하이퍼바이저 비교

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 90 | [090_sel4_vmm.md](090_sel4_vmm.md) | seL4 as VMM | MDPI 2022 |
| 91 | [091_xen_kvm_comparison.md](091_xen_kvm_comparison.md) | Quantitative Comparison of Xen and KVM | Xen Summit 2008 |
| 92 | [092_xen_kvm_scalability.md](092_xen_kvm_scalability.md) | Scalability Comparison of Xen and KVM | IEEE ECAI 2013 |

---

## 5. 컨테이너 런타임 및 클러스터 오케스트레이션

### 5.1 클러스터 관리 시스템

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 93 | [093_borg.md](093_borg.md) | Google Borg | EuroSys 2015 |
| 94 | [094_omega.md](094_omega.md) | Omega: Shared-State Schedulers | EuroSys 2013 (Best Student Paper) |
| 95 | [095_borg_omega_k8s_lessons.md](095_borg_omega_k8s_lessons.md) | Borg, Omega, and Kubernetes Lessons | CACM 2016 |
| 96 | [096_borg_next_gen.md](096_borg_next_gen.md) | Borg: the Next Generation | EuroSys 2020 |
| 97 | [097_mesos.md](097_mesos.md) | Mesos: Fine-Grained Resource Sharing | NSDI 2011 |
| 98 | [098_drf.md](098_drf.md) | Dominant Resource Fairness | NSDI 2011 |
| 99 | [099_yarn.md](099_yarn.md) | YARN: Yet Another Resource Negotiator | SoCC 2013 |
| 100 | [100_twine.md](100_twine.md) | Twine: Meta Cluster Management | OSDI 2020 |

### 5.2 클러스터 스케줄링 알고리즘

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 101 | [101_sparrow.md](101_sparrow.md) | Sparrow: Distributed Scheduling | SOSP 2013 |
| 102 | [102_firmament.md](102_firmament.md) | Firmament: Centralized Scheduling | OSDI 2016 |
| 103 | [103_tetris.md](103_tetris.md) | Tetris: Multi-Resource Packing | SIGCOMM 2014 |
| 104 | [104_quincy.md](104_quincy.md) | Quincy: Fair Scheduling | SOSP 2009 |
| 105 | [105_delay_scheduling.md](105_delay_scheduling.md) | Delay Scheduling | EuroSys 2010 |

### 5.3 Kubernetes 연구

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 106 | [106_k8s_scheduling_taxonomy.md](106_k8s_scheduling_taxonomy.md) | K8s Scheduling Taxonomy | ACM Computing Surveys 2022 |
| 107 | [107_k8s_scheduling_survey.md](107_k8s_scheduling_survey.md) | K8s Scheduling Algorithms Survey | JCC 2023 |
| 108 | [108_acto.md](108_acto.md) | Acto: K8s Operator Testing | SOSP 2023 |

### 5.4 오토스케일링

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 109 | [109_autopilot.md](109_autopilot.md) | Autopilot: Google Autoscaling | EuroSys 2020 |
| 110 | [110_madu_prediction_autoscaling.md](110_madu_prediction_autoscaling.md) | Madu: Predictive Autoscaling | SoCC 2022 |
| 111 | [111_deepscaling.md](111_deepscaling.md) | DeepScaling: DL Autoscaling | SoCC 2022 |

### 5.5 컨테이너 vs VM 비교 연구

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 112 | [112_vm_vs_container_performance.md](112_vm_vs_container_performance.md) | VM vs Container Performance | IEEE ISPASS 2015 |
| 113 | [113_container_vm_at_scale.md](113_container_vm_at_scale.md) | Containers and VMs at Scale | Middleware 2016 |
| 114 | [114_serverless_peek.md](114_serverless_peek.md) | Peeking Behind Serverless Platforms | ATC 2018 |
| 115 | [115_container_hpc.md](115_container_hpc.md) | Container-based Virtualization for HPC | IEEE PDP 2013 |

### 5.6 컨테이너 설계 패턴 및 역사

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 116 | [116_container_design_patterns.md](116_container_design_patterns.md) | Design Patterns for Containers | HotCloud 2016 |
| 117 | [117_vm_container_history.md](117_vm_container_history.md) | History of VMs and Containers | ACM Computing Surveys 2020 |
| 118 | [118_namespace_cgroup_desync.md](118_namespace_cgroup_desync.md) | Namespace-Cgroup Desynchronization | NDSS |

### 5.7 자원 관리 및 성능 격리

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 119 | [119_heracles.md](119_heracles.md) | Heracles: Resource Efficiency at Scale | ISCA 2015 |
| 120 | [120_quasar.md](120_quasar.md) | Quasar: ML-based Cluster Management | ASPLOS 2014 |
| 121 | [121_shenango.md](121_shenango.md) | Shenango: High CPU Efficiency | NSDI 2019 |

### 5.8 마이크로서비스 관측/디버깅

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 122 | [122_deathstarbench.md](122_deathstarbench.md) | DeathStarBench: Microservices Benchmark | ASPLOS 2019 |
| 123 | [123_seer.md](123_seer.md) | Seer: DL Performance Debugging | ASPLOS 2019 |
| 124 | [124_sage.md](124_sage.md) | Sage: ML Performance Debugging | ASPLOS 2021 |

### 5.9 컨테이너 마이그레이션

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 125 | [125_migros.md](125_migros.md) | MigrOS: RDMA Container Migration | ATC 2021 |

### 5.10 멀티 클러스터/페더레이션

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 126 | [126_mck8s.md](126_mck8s.md) | mck8s: Multi-Cluster K8s | ICCCN 2021 |
| 127 | [127_ge_kube.md](127_ge_kube.md) | ge-kube: Geo-distributed K8s | Computer Communications 2020 |

### 5.11 서비스 메시/네트워킹

| # | 파일 | 논문 | 학회 |
|---|------|------|------|
| 128 | [128_lightweight_sidecar.md](128_lightweight_sidecar.md) | Lightweight Serverless Sidecar | SoCC 2025 |
| 129 | [129_hxdp_ebpf.md](129_hxdp_ebpf.md) | hXDP: eBPF Packet Processing | SIGCOMM 2021 |
| 130 | [130_container_networking.md](130_container_networking.md) | Container Networking Performance | HotConNet 2017 |

---

## 분류별 통계

| 분류 | 논문 수 |
|------|---------|
| 유사 (Similar) | ~36 |
| 반대 (Opposing) | ~17 |
| 확장 (Extended) | ~33 |
| 보완 (Complementary) | ~25 |
| 기반 (Foundational) | ~12 |
| 대안 (Alternative) | ~6 |
| 비판 (Critical) | ~2 |

---

> 이 디렉토리는 [cloud/README.md](../README.md)의 참고문헌 [1]~[130]에 대한 개별 요약 문서를 포함합니다.
