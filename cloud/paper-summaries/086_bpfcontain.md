# BPFContain: Fixing the Soft Underbelly of Container Security

- **참고문헌 번호:** 86
- **저자:** Findlay et al.
- **학회/출처:** 2021
- **논문 링크:** https://scholar.google.com/scholar?q=BPFContain+eBPF+Container+Security+Findlay
- **분류:** 보완

## 요약

BPFContain은 eBPF를 활용하여 컨테이너 보안을 강화하는 프레임워크이다. 기존 컨테이너 보안 메커니즘(seccomp, AppArmor 등)의 한계를 극복하고, eBPF 프로그램을 통해 커널 수준에서 세밀한 보안 정책을 적용한다. 런타임 오버헤드를 최소화하면서도 컨테이너별 맞춤형 보안 정책을 동적으로 적용할 수 있다.

## 핵심 기여

- eBPF 기반 컨테이너 보안 프레임워크 설계 및 구현
- 기존 seccomp/AppArmor 대비 더 세밀한 보안 정책 지원
- 낮은 런타임 오버헤드로 실용적 보안 강화
- 컨테이너별 동적 보안 정책 적용 메커니즘

## Firecracker와의 관계

Firecracker는 microVM 수준의 격리를 제공하지만, 컨테이너 환경에서는 BPFContain과 같은 eBPF 기반 보안 프레임워크가 보완적 보안 계층을 제공할 수 있다. Firecracker의 jailer와 결합하면 다층 보안 체계를 구축할 수 있다.

## 키워드

eBPF, 컨테이너 보안, BPF, seccomp, AppArmor, 런타임 보안
