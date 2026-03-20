# Quincy: Fair Scheduling for Distributed Computing Clusters

- **참고문헌 번호:** 104
- **저자:** Isard et al.
- **학회/출처:** SOSP, 2009
- **논문 링크:** https://scholar.google.com/scholar?q=Quincy+Fair+Scheduling+Distributed+Computing+Clusters+Isard
- **분류:** 확장

## 요약

Quincy는 그래프 기반 스케줄링 알고리즘으로, 작업 배치 문제를 MCMF(Min-Cost Max-Flow) 문제로 모델링한다. 데이터 지역성, 공정성, 자원 활용 등의 다양한 목표를 비용 함수로 통합하여 최적 배치를 계산한다. 분산 컴퓨팅 환경에서의 공정하고 효율적인 스케줄링을 달성한다.

## 핵심 기여

- 작업 배치 문제의 그래프/MCMF 모델링
- 데이터 지역성과 공정성의 통합 최적화
- 다양한 목표를 비용 함수로 유연하게 표현
- Firmament 등 후속 연구의 기반 제공

## Firecracker와의 관계

Quincy의 그래프 기반 스케줄링 모델은 microVM 배치 시 다양한 제약 조건(자원, 지역성, 보안 격리 등)을 통합적으로 고려하는 프레임워크로 활용될 수 있다.

## 키워드

Quincy, MCMF, 그래프 기반 스케줄링, 공정성, 데이터 지역성, 비용 함수, 최적화
