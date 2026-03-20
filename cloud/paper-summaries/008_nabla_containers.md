# Nabla Containers: A New Approach to Container Isolation

- **참고문헌 번호:** 8
- **저자:** Williams, Koller
- **학회/출처:** HotCloud, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Nabla+Containers+A+New+Approach+to+Container+Isolation)
- **분류:** 유사

## 요약

Nabla Containers는 Solo5 LibOS를 활용하여 컨테이너가 호스트 커널에 단 7개의 시스템 콜만으로 접근하도록 제한하는 격리 기술이다. seccomp를 통해 허용된 syscall을 최소화함으로써 공격 표면을 극단적으로 줄이면서도 컨테이너와 유사한 사용성을 제공한다.

## 핵심 기여

- Solo5 LibOS 기반의 최소 syscall 컨테이너 설계
- 호스트 커널 접촉을 7개 syscall로 제한
- seccomp 기반의 강력한 격리 메커니즘
- 컨테이너 수준의 사용성과 VM 수준의 격리 달성

## Firecracker와의 관계

Firecracker와 유사하게 강력한 격리를 목표로 하지만, 하드웨어 가상화 대신 LibOS와 seccomp를 활용한다. Nabla의 7개 syscall 접근법은 gVisor의 ~20개보다 더 극단적인 최소화를 추구하며, Firecracker의 하드웨어 격리와는 다른 소프트웨어 기반 접근이다.

## 키워드

Nabla Containers, Solo5, LibOS, seccomp, Syscall Minimization, Container Isolation
