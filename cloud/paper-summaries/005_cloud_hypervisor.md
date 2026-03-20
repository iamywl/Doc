# Intel Cloud Hypervisor

- **참고문헌 번호:** 5
- **저자:** Intel / Open Source Community
- **학회/출처:** Open Source Project, 2019~
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Intel+Cloud+Hypervisor+rust+vmm)
- **분류:** 유사

## 요약

Intel Cloud Hypervisor는 Rust로 작성된 범용 VMM으로, Firecracker와 rust-vmm 크레이트를 공유한다. Firecracker가 서버리스 워크로드에 특화된 반면, Cloud Hypervisor는 보다 범용적인 클라우드 워크로드를 지원하며, VFIO, vhost-user 등 고급 기능을 제공한다.

## 핵심 기여

- Rust 기반의 안전한 범용 VMM 구현
- rust-vmm 크레이트 생태계 공유 및 발전 기여
- VFIO 디바이스 패스스루 지원
- vhost-user 기반 네트워크/스토리지 백엔드 지원

## Firecracker와의 관계

Firecracker와 동일한 rust-vmm 크레이트 기반을 공유하는 형제 프로젝트이다. Firecracker가 최소한의 기능으로 서버리스에 특화된 반면, Cloud Hypervisor는 범용 클라우드 워크로드를 위해 더 많은 기능을 제공한다.

## 키워드

Cloud Hypervisor, Rust, rust-vmm, Intel, VMM, VFIO, vhost-user
