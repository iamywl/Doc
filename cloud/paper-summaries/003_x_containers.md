# X-Containers: Breaking Down Barriers to Improve Performance and Isolation

- **참고문헌 번호:** 3
- **저자:** Shen et al.
- **학회/출처:** ASPLOS, 2019
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=X-Containers+Breaking+Down+Barriers+to+Improve+Performance+and+Isolation)
- **분류:** 유사

## 요약

X-Containers는 Exokernel 아키텍처와 Library OS(LibOS)를 결합하여 컨테이너의 성능과 격리를 동시에 향상시킨 연구이다. 시스템 콜 처리를 사용자 공간 LibOS로 이동시켜 27배의 syscall 처리량 향상을 달성하면서도 강력한 격리를 유지한다.

## 핵심 기여

- Exokernel + LibOS 기반의 새로운 컨테이너 아키텍처 제안
- 27배의 syscall 처리량 향상
- 성능과 격리 간의 트레이드오프 해소
- 기존 컨테이너 대비 향상된 보안 격리

## Firecracker와의 관계

Firecracker가 하드웨어 가상화를 통해 격리를 달성하는 반면, X-Containers는 Exokernel과 LibOS를 활용하여 유사한 격리 수준을 소프트웨어적으로 달성한다. 두 접근 모두 멀티테넌트 환경에서의 강력한 격리를 목표로 한다.

## 키워드

X-Containers, Exokernel, LibOS, Syscall Throughput, Container Isolation, Performance
