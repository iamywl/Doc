# FAASM: Lightweight Isolation for Efficient Stateful Serverless Computing

- **참고문헌 번호:** 40
- **저자:** Shillaker, Pietzuch
- **학회/출처:** ATC, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=FAASM+Lightweight+Isolation+for+Efficient+Stateful+Serverless+Computing)
- **분류:** 반대, Wasm SFI

## 요약

WebAssembly의 SFI(Software Fault Isolation)를 활용하여 Docker 대비 200배 빠른 초기화를 달성한 상태 저장(stateful) 서버리스 컴퓨팅 시스템이다. Wasm의 선형 메모리 모델을 통해 경량 격리를 제공하면서, 함수 간 효율적인 상태 공유를 지원한다.

## 핵심 기여

- WebAssembly SFI 기반의 Docker 대비 200배 빠른 초기화 달성
- 상태 저장 서버리스를 위한 효율적 공유 메모리 메커니즘 설계
- Wasm 선형 메모리를 활용한 경량 격리와 빠른 스냅샷/복원
- 대규모 분산 상태 관리와 함수 격리의 양립

## Firecracker와의 관계

Firecracker의 VM 수준 격리와 반대되는 Wasm SFI 기반 경량 격리를 제안한다. FAASM은 하드웨어 가상화 없이 소프트웨어 수준에서 격리하여 훨씬 빠른 시작 시간과 낮은 오버헤드를 달성하지만, Firecracker만큼 강력한 보안 격리를 제공하지는 않는다.

## 키워드

WebAssembly, Software Fault Isolation, 서버리스, 상태 저장, 경량 격리
