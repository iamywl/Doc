# Containers and Virtual Machines at Scale: A Comparative Study

- **참고문헌 번호:** 113
- **저자:** Sharma et al.
- **학회/출처:** Middleware, 2016
- **논문 링크:** https://scholar.google.com/scholar?q=Containers+Virtual+Machines+Scale+Comparative+Study+Sharma
- **분류:** 기반

## 요약

대규모 환경에서 컨테이너와 가상 머신을 비교한 연구이다. 이미지 크기(컨테이너 5MB vs VM 250MB), 시작 시간, 자원 사용량 등에서 컨테이너의 우위를 정량적으로 보여준다. 수백 개의 인스턴스를 동시 배포하는 대규모 시나리오에서 두 기술의 확장성 차이를 분석한다.

## 핵심 기여

- 대규모 환경에서의 컨테이너 vs VM 비교
- 이미지 크기 차이 정량화 (5MB vs 250MB)
- 동시 배포 시나리오에서의 확장성 분석
- 실제 클라우드 환경에서의 성능 측정

## Firecracker와의 관계

이 논문이 보여주는 VM의 자원 오버헤드 문제가 Firecracker 개발의 동기 중 하나이다. Firecracker는 microVM을 통해 VM의 이미지 크기와 시작 시간을 컨테이너에 가깝게 줄이면서도 VM 수준의 격리를 유지한다.

## 키워드

컨테이너, 가상 머신, 대규모 비교, 이미지 크기, 시작 시간, 확장성, 자원 효율
