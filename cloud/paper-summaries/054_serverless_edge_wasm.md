# Pushing Serverless to the Edge with WebAssembly Runtimes

- **참고문헌 번호:** 54
- **저자:** (저자 미상)
- **학회/출처:** CCGrid 2022
- **논문 링크:** https://scholar.google.com/scholar?q=Pushing+Serverless+to+the+Edge+with+WebAssembly+Runtimes
- **분류:** 반대

## 요약

엣지 환경에서 WebAssembly(Wasm) 런타임을 활용하여 서버리스 함수의 콜드 스타트를 99.5% 감소시킨 연구이다. 기존 VM이나 컨테이너 기반 격리 대신 Wasm 샌드박스를 사용함으로써 리소스 제약이 있는 엣지 디바이스에서도 효율적인 서버리스 실행이 가능함을 보여준다.

## 핵심 기여

- Wasm 런타임 기반 엣지 서버리스 실행 환경 구현
- 99.5%의 콜드 스타트 시간 감소 달성
- 엣지 환경에 적합한 경량 격리 메커니즘 제시
- VM/컨테이너 대비 Wasm의 엣지 적합성 실증

## Firecracker와의 관계

Firecracker의 microVM 기반 격리와 근본적으로 다른 접근법으로, Wasm 샌드박스를 격리 단위로 사용한다. Firecracker가 서버 환경에서 강력한 격리를 제공하는 반면, Wasm은 엣지 환경에서 훨씬 가벼운 격리와 빠른 시작 시간을 제공하여 Firecracker가 적합하지 않은 엣지 시나리오의 대안이 된다.

## 키워드

WebAssembly, edge computing, cold start, lightweight isolation, serverless
