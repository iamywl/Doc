# Container Networking Performance

- **참고문헌 번호:** 130
- **저자:** (Not specified)
- **학회/출처:** SIGCOMM HotConNet, 2017
- **논문 링크:** https://scholar.google.com/scholar?q=Container+Networking+Performance+HotConNet+SIGCOMM
- **분류:** 보완

## 요약

컨테이너 네트워킹의 성능 오버헤드를 체계적으로 분석한 연구이다. 다양한 컨테이너 네트워킹 모드(bridge, host, overlay 등)에서의 처리량, 지연 시간, CPU 사용량을 측정하고 비교한다. 컨테이너 네트워킹의 병목 지점을 식별하고 최적화 방향을 제시한다.

## 핵심 기여

- 컨테이너 네트워킹 모드별 성능 오버헤드 분석
- 처리량, 지연 시간, CPU 사용량 정량적 비교
- 네트워킹 병목 지점 식별
- 네트워킹 모드별 최적 사용 시나리오 제시

## Firecracker와의 관계

컨테이너 네트워킹의 오버헤드 분석은 Firecracker microVM의 네트워킹 설계와 비교하는 기준점을 제공한다. Firecracker의 virtio-net 기반 네트워킹과 컨테이너의 다양한 네트워킹 모드 간 성능 차이를 이해하는 데 참고가 된다.

## 키워드

컨테이너 네트워킹, 성능 오버헤드, bridge, overlay, 처리량, 지연 시간, 병목
