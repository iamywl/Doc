# RedLeaf: Isolation and Communication in a Safe Operating System

- **참고문헌 번호:** 29
- **저자:** Narayanan et al.
- **학회/출처:** OSDI, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=RedLeaf+Isolation+and+Communication+in+a+Safe+Operating+System)
- **분류:** 대안, Rust 언어 격리

## 요약

하드웨어 지원 없이 Rust 언어의 타입 시스템만으로 OS 내 격리를 구현한 운영체제이다. 기존의 하드웨어 기반 주소 공간 격리 대신, Rust의 소유권과 수명(lifetime) 시스템을 활용하여 도메인 간 안전한 통신과 격리를 제공한다. 이를 통해 하드웨어 가상화 오버헤드 없이 강력한 격리를 달성한다.

## 핵심 기여

- Rust 타입 시스템 기반의 순수 언어 수준 격리 메커니즘 구현
- 하드웨어 지원 없는 제로 오버헤드 도메인 격리 달성
- 안전한 도메인 간 통신(cross-domain communication) 프레임워크 제공
- 장애 격리와 복구를 언어 수준에서 지원

## Firecracker와의 관계

Firecracker의 KVM 기반 하드웨어 가상화 격리와 대조적으로, RedLeaf는 하드웨어 없이 언어 수준에서 격리를 달성하는 대안적 접근을 보여준다. Firecracker VMM 내부 구조에서 Rust의 안전성을 더 적극적으로 활용할 수 있는 가능성을 시사한다.

## 키워드

Rust, 언어 수준 격리, 소유권, 운영체제, 제로 오버헤드, 도메인 격리
