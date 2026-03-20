# LFI: Lightweight Fault Isolation for Software-based Sandboxing

- **참고문헌 번호:** 49
- **저자:** Ghosn et al.
- **학회/출처:** ASPLOS, 2024
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=LFI+Lightweight+Fault+Isolation+for+Software-based+Sandboxing)
- **분류:** 반대, SW 결함 격리

## 요약

단일 프로세스 내에서 수만 개의 샌드박스를 7% 이하의 오버헤드로 실행할 수 있는 소프트웨어 기반 결함 격리 시스템이다. 하드웨어 가상화나 프로세스 격리 없이 컴파일러 수준의 계측(instrumentation)만으로 메모리 격리를 달성하여, 극도로 높은 밀도의 샌드박스 실행을 가능하게 한다.

## 핵심 기여

- 단일 프로세스 내 수만 개 샌드박스 실행(7% 오버헤드)
- 컴파일러 기반 계측으로 하드웨어 지원 없이 메모리 격리 달성
- 기존 SFI 기법 대비 극도로 높은 샌드박스 밀도
- 서드파티 라이브러리 격리에 적합한 세밀한 격리 제공

## Firecracker와의 관계

Firecracker가 VM당 하나의 격리 단위를 제공하는 것과 반대로, LFI는 단일 프로세스 내에서 수만 개의 격리 단위를 제공한다. 격리 입도와 오버헤드 면에서 극단적으로 다른 트레이드오프를 보여주며, 함수/라이브러리 수준의 세밀한 격리가 필요한 경우 Firecracker의 대안이 될 수 있다.

## 키워드

Software Fault Isolation, 샌드박싱, 경량 격리, 컴파일러 계측, 고밀도
