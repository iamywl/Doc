# Delay Scheduling: A Simple Technique for Achieving Locality and Fairness in Cluster Scheduling

- **참고문헌 번호:** 105
- **저자:** Zaharia et al.
- **학회/출처:** EuroSys, 2010
- **논문 링크:** https://scholar.google.com/scholar?q=Delay+Scheduling+Simple+Technique+Locality+Fairness+Zaharia
- **분류:** 확장

## 요약

지연 스케줄링(Delay Scheduling)은 작업이 로컬 데이터가 있는 노드를 기다리는 간단한 기법을 통해 데이터 지역성과 공정성을 동시에 달성한다. 짧은 대기 시간으로 거의 최적의 데이터 지역성을 확보하며, 처리량을 2배까지 향상시킨다. 구현이 간단하면서도 효과적인 스케줄링 최적화를 제공한다.

## 핵심 기여

- 지연 스케줄링을 통한 데이터 지역성 최적화
- 거의 최적의 지역성과 2배 처리량 향상 달성
- 공정성과 지역성 사이의 트레이드오프 분석
- 간단한 구현으로 높은 효과 달성

## Firecracker와의 관계

지연 스케줄링의 원리는 Firecracker microVM 배치 시 데이터 지역성을 고려하는 데 적용될 수 있다. 특히 스냅샷 기반 빠른 복원 시 로컬 스냅샷이 있는 노드를 선호하는 스케줄링에 유사한 기법을 적용할 수 있다.

## 키워드

지연 스케줄링, 데이터 지역성, 공정성, Hadoop, 처리량, 스케줄링 최적화
