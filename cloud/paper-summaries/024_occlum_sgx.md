# Occlum: Secure and Efficient Multitasking Inside a Single Enclave of Intel SGX

- **참고문헌 번호:** 24
- **저자:** Shen et al.
- **학회/출처:** ASPLOS, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Occlum+Secure+and+Efficient+Multitasking+Inside+a+Single+Enclave+of+Intel+SGX)
- **분류:** 대안, SGX 멀티태스킹

## 요약

Intel SGX 단일 엔클레이브 내에서 SFI(Software Fault Isolation) 기반의 다중 프로세스 격리를 구현한 Library OS이다. 기존 SGX 활용 방식이 엔클레이브 하나당 프로세스 하나로 제한되는 비효율성을 해결하고자, 엔클레이브 내부에서 여러 격리된 프로세스를 안전하게 실행할 수 있는 아키텍처를 제안한다.

## 핵심 기여

- 단일 SGX 엔클레이브 내 다중 프로세스 격리를 SFI를 통해 구현
- 엔클레이브 전환 오버헤드 없이 멀티태스킹 지원
- POSIX 호환 인터페이스를 제공하여 기존 애플리케이션의 이식성 확보
- 기존 SGX Library OS 대비 우수한 성능과 보안성 달성

## Firecracker와의 관계

Firecracker가 하드웨어 가상화(KVM) 기반 격리를 사용하는 것과 달리, Occlum은 SGX 하드웨어 기밀 실행 환경 내에서 소프트웨어 기반 격리를 제공하는 대안적 접근이다. TEE 기반의 서버리스 컴퓨팅을 위한 격리 메커니즘으로 Firecracker와 상호 보완적으로 활용될 수 있다.

## 키워드

Intel SGX, Software Fault Isolation, Library OS, 엔클레이브, 멀티태스킹, TEE
