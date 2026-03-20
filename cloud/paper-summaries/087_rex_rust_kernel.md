# Rex: Rust Kernel Extensions Replacing eBPF

- **참고문헌 번호:** 87
- **저자:** Jia et al.
- **학회/출처:** ATC 2025
- **논문 링크:** https://scholar.google.com/scholar?q=Rex+Rust+Kernel+Extensions+Replacing+eBPF
- **분류:** 보완

## 요약

Rex는 eBPF를 대체하여 Rust로 작성된 커널 확장을 제안한다. Rust의 타입 안전성(type safety)을 활용하여 eBPF 검증기의 한계를 극복하고, 더 표현력 있는 커널 확장 프로그램을 안전하게 실행할 수 있게 한다. eBPF의 제한된 프로그래밍 모델을 넘어서면서도 커널 안정성을 보장한다.

## 핵심 기여

- Rust 기반 커널 확장 프레임워크 설계
- eBPF 검증기 한계 극복 (루프, 복잡한 데이터 구조 등)
- Rust 타입 시스템을 통한 메모리 안전성 보장
- eBPF 대비 더 높은 표현력과 성능 달성

## Firecracker와의 관계

Firecracker 자체가 Rust로 작성되어 있어, Rex의 Rust 커널 확장 접근법은 Firecracker 생태계와 자연스럽게 통합될 수 있다. microVM의 네트워킹이나 보안 정책을 Rust 커널 확장으로 구현하면 일관된 안전성을 확보할 수 있다.

## 키워드

Rust, 커널 확장, eBPF 대안, 타입 안전성, 메모리 안전성, 커널 프로그래밍
