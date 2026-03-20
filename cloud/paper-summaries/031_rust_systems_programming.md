# System Programming in Rust: Beyond Safety

- **참고문헌 번호:** 31
- **저자:** Balasubramanian et al.
- **학회/출처:** HotOS, 2017
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=System+Programming+in+Rust+Beyond+Safety)
- **분류:** 기반, Rust 시스템 프로그래밍

## 요약

Rust를 시스템 프로그래밍에 활용할 때 메모리 안전성 이상의 이점을 분석한 논문이다. Rust의 타입 시스템과 소유권 모델이 동시성 버그 방지, 리소스 관리, API 설계 등 시스템 프로그래밍의 다양한 측면에서 어떤 장점을 제공하는지 탐구한다.

## 핵심 기여

- Rust의 시스템 프로그래밍 적용 사례 분석 및 장점 정리
- 메모리 안전성 이외의 Rust 타입 시스템 활용 방안 제시
- 동시성 안전성, 리소스 관리 등 추가적인 보장 항목 식별
- 시스템 소프트웨어에서 Rust 도입의 실용적 가이드라인 제시

## Firecracker와의 관계

Firecracker가 Rust를 선택한 이유와 그로 인해 얻는 시스템 수준의 이점을 이해하는 데 기반이 되는 연구이다. Firecracker가 Rust를 통해 달성하는 안전한 VMM 구현의 이론적·실용적 근거를 제공한다.

## 키워드

Rust, 시스템 프로그래밍, 메모리 안전성, 동시성, 타입 시스템, 소유권
