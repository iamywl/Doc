# Unikernels as Processes

- **참고문헌 번호:** 11
- **저자:** Williams et al.
- **학회/출처:** SoCC, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Unikernels+as+Processes)
- **분류:** 유사

## 요약

이 연구는 유니커널을 하이퍼바이저 위의 VM이 아닌 일반 OS 프로세스로 실행하는 방법을 제안한다. seccomp를 활용하여 프로세스 수준에서 격리를 제공하면서도 유니커널의 경량화 이점을 유지한다. 이를 통해 유니커널의 배포와 관리가 일반 프로세스만큼 간편해진다.

## 핵심 기여

- 유니커널을 OS 프로세스로 실행하는 새로운 패러다임
- seccomp 기반의 프로세스 수준 격리
- 하이퍼바이저 의존성 제거
- 기존 프로세스 관리 도구와의 호환성

## Firecracker와의 관계

Firecracker가 하드웨어 가상화를 통해 유니커널을 격리하는 반면, 이 접근법은 seccomp를 통해 프로세스 수준에서 유니커널을 격리한다. 하이퍼바이저 오버헤드를 제거하면서도 합리적인 격리를 제공하는 대안적 접근이다.

## 키워드

Unikernel, Process, seccomp, Lightweight Isolation, No Hypervisor
