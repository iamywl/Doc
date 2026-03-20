# Sledge: A Serverless-first, Light-weight Wasm Runtime for Edge

- **참고문헌 번호:** 41
- **저자:** Gadepalli et al.
- **학회/출처:** Middleware, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Sledge+Serverless-first+Light-weight+Wasm+Runtime+for+Edge)
- **분류:** 반대, 엣지 Wasm

## 요약

엣지 환경을 위한 단일 프로세스 WebAssembly 런타임으로, 서버리스 함수를 Wasm 모듈로 실행한다. 모든 함수를 하나의 프로세스 내에서 Wasm 격리를 통해 실행하여 컨텍스트 전환 오버헤드를 최소화하고, 리소스가 제한된 엣지 디바이스에서도 효율적으로 동작한다.

## 핵심 기여

- 엣지 환경에 최적화된 단일 프로세스 Wasm 서버리스 런타임 설계
- Wasm 격리를 통한 컨텍스트 전환 오버헤드 최소화
- 리소스 제약 환경에서의 효율적 함수 스케줄링
- 엣지 서버리스 워크로드에 대한 성능 벤치마크 제공

## Firecracker와의 관계

Firecracker의 VM 기반 격리와 반대로 Wasm 기반 경량 격리를 사용하여 엣지 환경의 리소스 제약에 대응한다. Firecracker microVM은 엣지 디바이스에서 오버헤드가 클 수 있는 반면, Sledge는 단일 프로세스 모델로 최소한의 리소스만 사용한다.

## 키워드

WebAssembly, 엣지 컴퓨팅, 서버리스, 단일 프로세스, 경량 런타임
