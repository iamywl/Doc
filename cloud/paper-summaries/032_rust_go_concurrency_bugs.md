# Understanding Real-World Concurrency Bugs in Go and Rust

- **참고문헌 번호:** 32
- **저자:** Qin et al.
- **학회/출처:** PLDI, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Understanding+Real-World+Concurrency+Bugs+in+Go+and+Rust)
- **분류:** 기반, Rust 동시성 버그

## 요약

Go와 Rust의 실제 오픈소스 프로젝트에서 발생한 동시성 버그를 실증적으로 분석한 연구이다. Rust의 소유권 모델이 많은 동시성 버그를 방지하지만, unsafe 코드 사용이나 논리적 데드락 등 여전히 발생 가능한 동시성 문제들을 분류하고 패턴을 식별하였다.

## 핵심 기여

- Rust와 Go의 실제 동시성 버그에 대한 대규모 실증 연구
- Rust 소유권 모델이 방지하는 버그 유형과 방지하지 못하는 유형 분류
- unsafe 코드에서 발생하는 동시성 버그 패턴 식별
- 두 언어의 동시성 안전성 모델의 강점과 한계 비교

## Firecracker와의 관계

Firecracker가 Rust로 작성되어 동시성 버그로부터 어느 정도 보호받는지를 이해하는 데 기반이 되는 연구이다. Firecracker의 멀티스레드 처리에서 Rust가 방지하는 버그와 여전히 주의해야 할 동시성 문제를 이해하는 데 도움을 준다.

## 키워드

Rust, Go, 동시성 버그, 데이터 레이스, 데드락, 소유권, unsafe
