# ge-kube: Geo-distributed Efficient Deployment of Containers with Kubernetes

- **참고문헌 번호:** 127
- **저자:** Rossi et al.
- **학회/출처:** Computer Communications, 2020
- **논문 링크:** https://scholar.google.com/scholar?q=ge-kube+Geo-distributed+Efficient+Deployment+Containers+Kubernetes
- **분류:** 확장

## 요약

ge-kube는 강화학습(RL)과 네트워크 인지(network-aware) 배치를 결합하여 지리적으로 분산된 환경에서 Kubernetes 컨테이너를 효율적으로 배포하는 시스템이다. 네트워크 지연, 대역폭, 자원 가용성을 고려한 최적 배치를 학습하여 서비스 품질을 개선한다.

## 핵심 기여

- 강화학습 기반 지리 분산 컨테이너 배치
- 네트워크 인지 배치 최적화
- 네트워크 지연과 자원 가용성 동시 고려
- Kubernetes 환경에서의 실제 구현 및 평가

## Firecracker와의 관계

ge-kube의 네트워크 인지 배치 전략은 지리적으로 분산된 Firecracker microVM 배치에 적용 가능하다. 엣지 컴퓨팅 환경에서 사용자와 가까운 위치에 microVM을 최적으로 배치하는 데 RL 기반 접근법을 활용할 수 있다.

## 키워드

ge-kube, 강화학습, 지리 분산, 네트워크 인지, Kubernetes, 컨테이너 배치, 최적화
