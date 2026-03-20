# Network Startup Bottleneck in Secure Container Runtimes

- **참고문헌 번호:** 80
- **저자:** Liu et al.
- **학회/출처:** ACM IMC 2024
- **논문 링크:** https://scholar.google.com/scholar?q=Network+Startup+Bottleneck+in+Secure+Container+Runtimes
- **분류:** 보완

## 요약

보안 컨테이너 런타임에서 네트워크 스택 초기화가 시작 시간의 주요 병목임을 밝히는 연구이다. VM 부팅이나 커널 초기화뿐 아니라 네트워크 구성이 콜드 스타트 지연의 상당 부분을 차지하며, 이에 대한 분석과 개선 방향을 제시한다.

## 핵심 기여

- 보안 컨테이너 런타임의 네트워크 스택 초기화 병목 최초 규명
- 콜드 스타트 지연에서 네트워크 구성의 비중 정량 분석
- 다양한 보안 런타임(Kata, gVisor 등)에서의 네트워크 병목 비교
- 네트워크 초기화 최적화 방향 제시

## Firecracker와의 관계

Firecracker microVM의 빠른 부팅 시간(125ms)에도 불구하고 네트워크 스택 초기화가 추가적인 콜드 스타트 지연을 유발할 수 있다. 이 연구의 분석 결과는 Firecracker의 네트워크 초기화 과정을 최적화하여 전체 콜드 스타트 시간을 더욱 단축하는 데 활용할 수 있다.

## 키워드

network startup, cold start bottleneck, secure container runtime, network initialization, latency analysis
