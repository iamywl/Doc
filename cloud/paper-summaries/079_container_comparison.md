# A Comparative Study of RunC, gVisor, Kata Containers

- **참고문헌 번호:** 79
- **저자:** Wang et al.
- **학회/출처:** Cluster Computing 2022
- **논문 링크:** https://scholar.google.com/scholar?q=A+Comparative+Study+of+RunC+gVisor+Kata+Containers
- **분류:** 보완

## 요약

RunC, gVisor, Kata Containers 세 가지 컨테이너 런타임에 대한 체계적 벤치마크 비교 연구이다. 각 런타임의 성능, 보안, 자원 사용량을 다양한 워크로드에서 측정하고 비교하여, 각 기술의 장단점과 적합한 사용 시나리오를 제시한다.

## 핵심 기여

- RunC, gVisor, Kata Containers의 체계적 성능 벤치마크
- 보안, 성능, 자원 사용량 다차원 비교 분석
- 워크로드별 최적 런타임 선택 가이드라인 제시
- 컨테이너 격리 기술의 현황 종합 정리

## Firecracker와의 관계

Firecracker와 동일한 문제 영역(컨테이너/VM 격리)의 대안 기술들을 비교한 연구로, Firecracker의 위치를 이해하는 데 유용하다. 특히 Kata Containers가 Firecracker와 유사한 microVM 접근법을 사용하므로, 이 비교 결과는 Firecracker의 상대적 강점과 약점을 파악하는 참고 자료가 된다.

## 키워드

container runtime, benchmark, RunC, gVisor, Kata Containers, isolation comparison
