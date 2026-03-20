# Intel NEMU

- **참고문헌 번호:** 6
- **저자:** Intel
- **학회/출처:** Open Source Project, 2018-2019 (archived)
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Intel+NEMU+stripped+down+QEMU)
- **분류:** 유사

## 요약

Intel NEMU는 QEMU에서 레거시 기능을 제거하여 경량화한 VMM이다. 클라우드 네이티브 워크로드에 불필요한 레거시 하드웨어 에뮬레이션, 비 x86 아키텍처 지원 등을 제거하여 코드베이스를 대폭 축소하고 공격 표면을 줄였다. 이후 Cloud Hypervisor 프로젝트로 발전적 전환되었다.

## 핵심 기여

- QEMU의 레거시 기능 제거를 통한 경량화
- 클라우드 워크로드에 특화된 최소 VMM 구현
- 코드베이스 축소를 통한 공격 표면 감소
- Cloud Hypervisor 프로젝트의 전신

## Firecracker와의 관계

Firecracker가 KVM 위에 새로운 VMM을 처음부터 구축한 반면, NEMU는 기존 QEMU를 경량화하는 접근법을 취했다. 두 프로젝트 모두 클라우드 워크로드를 위한 경량 VMM을 목표로 하지만, 구현 전략이 상이하다.

## 키워드

NEMU, QEMU, Lightweight VMM, Legacy Removal, Cloud Native, Intel
