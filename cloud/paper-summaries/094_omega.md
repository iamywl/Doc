# Omega: Flexible, Scalable Schedulers for Large Compute Clusters

- **참고문헌 번호:** 94
- **저자:** Schwarzkopf et al.
- **학회/출처:** EuroSys 2013 (Best Student Paper)
- **논문 링크:** https://scholar.google.com/scholar?q=Omega+Flexible+Scalable+Schedulers+Large+Compute+Clusters
- **분류:** 기반

## 요약

Omega는 Google에서 개발한 공유 상태(shared-state) 기반 클러스터 스케줄러이다. 중앙 집중식(Borg)과 분산식(Mesos) 스케줄링의 한계를 극복하기 위해, 낙관적 동시성 제어(optimistic concurrency)를 사용한다. 각 스케줄러가 전체 클러스터 상태의 복사본을 가지고 독립적으로 결정한 후 충돌을 해결하는 방식이다.

## 핵심 기여

- 공유 상태 기반 스케줄링 아키텍처 제안
- 낙관적 동시성 제어를 통한 스케줄링 확장성 향상
- 중앙 집중식, 2-레벨, 공유 상태 스케줄링 비교 분석
- 시뮬레이션을 통한 대규모 클러스터 스케줄링 평가

## Firecracker와의 관계

Omega의 스케줄링 아키텍처는 Firecracker microVM을 대규모로 배포하는 클라우드 환경에서 효율적인 배치 결정을 위한 기반 기술이다. microVM의 빠른 시작 시간은 Omega의 낙관적 동시성 모델에서 충돌 시 재시도 비용을 줄인다.

## 키워드

Omega, 공유 상태 스케줄링, 낙관적 동시성, 클러스터 스케줄러, Google, 확장성
