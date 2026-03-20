# Quasar: Resource-Efficient and QoS-Aware Cluster Management

- **참고문헌 번호:** 120
- **저자:** Delimitrou, Kozyrakis
- **학회/출처:** ASPLOS, 2014
- **논문 링크:** https://scholar.google.com/scholar?q=Quasar+Resource-Efficient+QoS-Aware+Cluster+Management+Delimitrou
- **분류:** 확장

## 요약

Quasar는 ML(머신러닝)을 활용하여 클러스터 자원을 효율적으로 할당하는 시스템이다. 협업 필터링(collaborative filtering) 기법으로 워크로드의 자원 요구량을 예측하고, QoS를 보장하면서도 47% 적은 서버로 동일한 작업을 처리한다. 자원 할당 자동화를 통해 운영 복잡성을 줄인다.

## 핵심 기여

- 협업 필터링 기반 자원 요구량 예측
- 47% 적은 서버로 동일 워크로드 처리
- QoS 보장과 자원 효율의 동시 달성
- 자원 할당 자동화를 통한 운영 간소화

## Firecracker와의 관계

Quasar의 ML 기반 자원 할당은 Firecracker microVM의 자원 프로비저닝에 활용될 수 있다. 워크로드 특성에 따른 최적 microVM 사양(CPU, 메모리)을 자동으로 결정하여 자원 낭비를 줄일 수 있다.

## 키워드

Quasar, ML, 협업 필터링, 자원 할당, QoS, 클러스터 관리, 자원 효율
