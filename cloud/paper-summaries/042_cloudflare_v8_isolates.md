# Cloudflare Workers: Cloud Computing without Containers (V8 Isolates)

- **참고문헌 번호:** 42
- **저자:** Bloom
- **학회/출처:** Cloudflare, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Cloudflare+Workers+Cloud+Computing+without+Containers+V8+Isolates)
- **분류:** 반대, V8 Isolates

## 요약

Cloudflare Workers가 V8 JavaScript 엔진의 Isolate 기능을 활용하여 컨테이너나 VM 없이 서버리스 함수를 실행하는 아키텍처를 소개한다. V8 Isolate는 거의 제로에 가까운 콜드 스타트와 KB 수준의 메모리 오버헤드를 제공하여, 전통적인 컨테이너/VM 기반 서버리스 대비 극적으로 낮은 오버헤드를 달성한다.

## 핵심 기여

- V8 Isolate 기반의 거의 제로 콜드 스타트 서버리스 실행 환경
- KB 수준의 극도로 낮은 메모리 오버헤드
- 컨테이너/VM 없는 서버리스 아키텍처의 상용 구현
- 글로벌 엣지 네트워크에서의 대규모 프로덕션 검증

## Firecracker와의 관계

Firecracker의 VM 기반 강격리와 정반대의 접근으로, V8 Isolate라는 언어 런타임 수준 격리만으로 멀티테넌시를 구현한다. 콜드 스타트와 오버헤드 면에서 Firecracker를 크게 능가하지만, 격리 강도는 VM 수준에 미치지 못하며 JavaScript/Wasm 워크로드로 제한된다.

## 키워드

V8 Isolate, Cloudflare Workers, 서버리스, 제로 콜드 스타트, 경량 격리
