# CAP-VMs: Capability-Based Isolation and Sharing in the Cloud

- **참고문헌 번호:** 51
- **저자:** Sartakov et al.
- **학회/출처:** OSDI 2022
- **논문 링크:** https://scholar.google.com/scholar?q=CAP-VMs+Capability-Based+Isolation+and+Sharing+in+the+Cloud
- **분류:** 대안

## 요약

CAP-VMs는 ARM CHERI 하드웨어 capability를 활용하여 클라우드 환경에서 VM 간 격리와 공유를 동시에 달성하는 시스템이다. 기존 VM 기반 격리가 메모리 복사 오버헤드를 수반하는 반면, CAP-VMs는 capability 기반 접근 제어를 통해 zero-copy 공유를 안전하게 지원한다. 이를 통해 격리 수준을 유지하면서도 VM 간 데이터 공유 성능을 크게 향상시킨다.

## 핵심 기여

- ARM CHERI capability를 클라우드 VM 격리에 적용한 최초의 시스템 설계
- Zero-copy VM 간 데이터 공유 메커니즘 구현
- 기존 하이퍼바이저 기반 격리 대비 공유 성능 향상
- 능력(capability) 기반 세밀한 접근 제어 모델 제안

## Firecracker와의 관계

Firecracker가 하이퍼바이저 기반 VM 격리를 사용하는 반면, CAP-VMs는 하드웨어 capability를 통한 격리라는 근본적으로 다른 접근법을 제시한다. 특히 Firecracker의 VM 간 데이터 공유 시 발생하는 메모리 복사 오버헤드를 capability 기반 zero-copy 공유로 해결할 수 있는 대안적 아키텍처이다.

## 키워드

ARM CHERI, capability-based isolation, zero-copy sharing, cloud VM, hardware security
