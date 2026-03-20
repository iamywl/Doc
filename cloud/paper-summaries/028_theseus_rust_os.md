# Theseus: An Experiment in Operating System Structure and State Management

- **참고문헌 번호:** 28
- **저자:** Boos et al.
- **학회/출처:** OSDI, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Theseus+An+Experiment+in+Operating+System+Structure+and+State+Management)
- **분류:** 유사, Rust OS

## 요약

Rust 언어의 소유권(ownership) 모델을 OS 설계에 적용하여, 교체 가능한 셀(cell) 단위로 구성된 새로운 운영체제 구조를 제안한 논문이다. 각 셀은 독립적으로 교체 가능하며, Rust의 타입 시스템을 통해 상태 관리와 격리를 컴파일 타임에 보장한다. 이를 통해 런타임 오버헤드 없이 높은 모듈성과 안정성을 달성한다.

## 핵심 기여

- Rust 소유권 모델을 OS 커널 구조에 적용한 최초의 본격적 실험
- 교체 가능한 셀 기반의 OS 구성으로 라이브 업데이트 및 장애 복구 지원
- 컴파일 타임 상태 관리로 런타임 격리 오버헤드 제거
- 기존 모놀리식/마이크로커널 OS 대비 새로운 구조적 대안 제시

## Firecracker와의 관계

Firecracker도 Rust로 작성되어 메모리 안전성을 확보하고 있으며, Theseus는 Rust의 안전성 보장을 OS 수준으로 확장한 유사한 철학을 공유한다. Theseus의 모듈식 셀 구조는 Firecracker의 최소 공격 표면 설계 원칙과 맥을 같이한다.

## 키워드

Rust, 운영체제, 소유권 모델, 모듈성, 상태 관리, 라이브 업데이트
