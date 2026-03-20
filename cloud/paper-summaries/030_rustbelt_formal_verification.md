# RustBelt: Securing the Foundations of the Rust Programming Language

- **참고문헌 번호:** 30
- **저자:** Jung et al.
- **학회/출처:** POPL, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=RustBelt+Securing+the+Foundations+of+the+Rust+Programming+Language)
- **분류:** 기반, Rust 형식 검증

## 요약

Rust 타입 시스템의 안전성을 형식적으로 검증한 기념비적 연구이다. Rust의 unsafe 코드를 포함한 핵심 라이브러리들이 타입 시스템의 안전성 보장을 위반하지 않음을 Coq 증명 보조기를 사용하여 기계적으로 증명하였다. 이를 통해 Rust의 소유권 모델과 수명 시스템의 수학적 토대를 확립하였다.

## 핵심 기여

- Rust 타입 시스템의 최초 형식적 안전성 증명 제공
- unsafe 코드가 포함된 라이브러리의 안전성을 기계적으로 검증
- Iris 논리 프레임워크 기반의 시맨틱 타입 안전성 모델 구축
- Rust의 소유권/수명 시스템에 대한 수학적 기반 확립

## Firecracker와의 관계

Firecracker가 Rust로 작성되어 얻는 메모리 안전성 보장의 이론적 기반을 제공하는 연구이다. RustBelt의 형식 검증은 Firecracker가 Rust 타입 시스템에 의존하여 보안을 확보하는 접근 방식의 신뢰성을 뒷받침한다.

## 키워드

Rust, 형식 검증, 타입 안전성, 소유권, Coq, Iris, unsafe
