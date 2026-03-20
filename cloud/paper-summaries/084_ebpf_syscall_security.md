# Programmable System Call Security with eBPF

- **참고문헌 번호:** 84
- **저자:** Jia et al.
- **학회/출처:** arXiv 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Programmable+System+Call+Security+with+eBPF
- **분류:** 보완

## 요약

eBPF를 활용하여 시스템 콜 보안 정책을 프로그래밍 가능하게 만드는 연구이다. 기존의 정적 시스템 콜 필터링(seccomp-BPF) 대비 더 유연하고 표현력 있는 보안 정책을 eBPF 프로그램으로 정의할 수 있으며, 런타임에 동적으로 정책을 변경할 수 있다.

## 핵심 기여

- eBPF 기반 프로그래밍 가능한 시스템 콜 보안 프레임워크
- seccomp-BPF 대비 향상된 유연성과 표현력
- 런타임 동적 보안 정책 변경 지원
- 세밀한 시스템 콜 필터링 및 모니터링

## Firecracker와의 관계

Firecracker는 jailer를 통해 seccomp 기반 시스템 콜 필터링을 사용한다. eBPF 기반 프로그래밍 가능한 시스템 콜 보안은 Firecracker의 기존 seccomp 정책을 더 유연하고 동적으로 확장할 수 있는 보완적 기술이다.

## 키워드

eBPF, system call security, programmable security, seccomp, dynamic policy
