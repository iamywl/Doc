# Not So Fast: Analyzing the Performance of WebAssembly vs. Native Code

- **참고문헌 번호:** 43
- **저자:** Jangda et al.
- **학회/출처:** ATC, 2019
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Not+So+Fast+Analyzing+the+Performance+of+WebAssembly+vs+Native+Code)
- **분류:** 균형, Wasm 성능 한계

## 요약

WebAssembly와 네이티브 코드의 성능을 체계적으로 비교 분석하여, Wasm이 평균 45% 느리다는 결과를 제시한 논문이다. SPEC 벤치마크를 활용하여 Wasm의 성능 병목이 간접 분기, 메모리 접근 패턴, 레지스터 할당 등에 기인함을 분석하였다.

## 핵심 기여

- WebAssembly와 네이티브 코드의 포괄적 성능 비교(평균 45% 느림)
- Wasm 성능 병목의 근본 원인 분석(간접 분기, 메모리, 레지스터)
- SPEC 벤치마크 기반의 체계적 성능 측정 방법론
- Wasm 런타임 최적화를 위한 구체적 개선 방향 제시

## Firecracker와의 관계

Wasm 기반 격리가 Firecracker VM 격리의 대안으로 제시될 때, Wasm의 성능 한계를 정량적으로 보여주는 균형 잡힌 시각을 제공한다. 45%의 성능 오버헤드는 Wasm 기반 격리가 모든 워크로드에 적합하지 않을 수 있음을 시사하며, Firecracker의 네이티브 실행 성능의 가치를 뒷받침한다.

## 키워드

WebAssembly, 성능 분석, 네이티브 코드, 벤치마크, 오버헤드
