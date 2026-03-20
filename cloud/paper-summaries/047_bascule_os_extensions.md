# Bascule: Composing OS Extensions Safely and Efficiently

- **참고문헌 번호:** 47
- **저자:** Baumann et al.
- **학회/출처:** EuroSys, 2013
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Bascule+Composing+OS+Extensions+Safely+and+Efficiently)
- **분류:** 반대, OS 확장

## 요약

하드웨어 가상화 없이 계층적 Library OS 구조를 통해 OS 확장을 안전하고 효율적으로 구성하는 시스템이다. 각 애플리케이션이 자체 Library OS 인스턴스를 갖고, 이를 통해 호스트 커널과의 인터페이스를 제한하여 격리를 달성한다.

## 핵심 기여

- 계층적 Library OS 기반의 안전한 OS 확장 프레임워크 설계
- 하드웨어 가상화 없이 커널 공격 표면 축소
- 애플리케이션별 독립적인 OS 정책 적용 지원
- Library OS 계층의 효율적 합성(composition) 메커니즘

## Firecracker와의 관계

Firecracker가 KVM 하드웨어 가상화로 격리를 제공하는 것과 반대로, Bascule는 Library OS 계층화만으로 격리를 달성하는 접근이다. 하드웨어 가상화 오버헤드 없이 격리를 제공하지만, VM 수준의 강력한 격리에는 미치지 못한다.

## 키워드

Library OS, OS 확장, 격리, 계층적 구조, 커널 공격 표면
