# Heracles: Improving Resource Efficiency at Scale

- **참고문헌 번호:** 119
- **저자:** Lo et al.
- **학회/출처:** ISCA, 2015
- **논문 링크:** https://scholar.google.com/scholar?q=Heracles+Improving+Resource+Efficiency+Scale+Lo
- **분류:** 확장

## 요약

Heracles는 대규모 데이터센터에서 LLC(Last Level Cache), 메모리 대역폭, 네트워크 대역폭을 동적으로 파티셔닝하여 자원 활용률을 90%까지 높이는 시스템이다. 지연 시간에 민감한(latency-critical) 워크로드의 SLO를 보장하면서도 배치(batch) 워크로드와 자원을 공유할 수 있게 한다.

## 핵심 기여

- LLC/메모리/네트워크의 동적 파티셔닝 메커니즘
- 자원 활용률 90% 달성
- 지연 시간 민감 워크로드의 SLO 보장
- 혼합 워크로드 환경에서의 자원 공유 최적화

## Firecracker와의 관계

Heracles의 동적 자원 파티셔닝은 동일 호스트에서 여러 Firecracker microVM이 자원을 공유하는 환경에서 적용 가능하다. microVM 간 LLC 및 메모리 대역폭 간섭을 관리하여 성능 격리를 강화할 수 있다.

## 키워드

Heracles, 자원 효율, LLC 파티셔닝, 메모리 대역폭, SLO, 동적 파티셔닝, 90% 활용
