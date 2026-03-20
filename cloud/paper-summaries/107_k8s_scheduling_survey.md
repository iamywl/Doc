# A Survey of Kubernetes Scheduling Algorithms

- **참고문헌 번호:** 107
- **저자:** Senjab et al.
- **학회/출처:** JCC, 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Survey+Kubernetes+Scheduling+Algorithms+Senjab
- **분류:** 보완

## 요약

47개의 연구를 분석하여 Kubernetes 스케줄링 알고리즘을 종합적으로 리뷰한다. 기본 스케줄러의 동작 원리부터 커스텀 스케줄러, ML 기반 스케줄러까지 다양한 접근법을 비교 분석한다. 자원 효율성, QoS 보장, 에너지 절감 등 다양한 최적화 목표별로 알고리즘을 분류한다.

## 핵심 기여

- 47개 연구 기반 K8s 스케줄링 알고리즘 종합 리뷰
- 기본/커스텀/ML 기반 스케줄러 비교
- 최적화 목표별 알고리즘 분류 및 평가
- 연구 트렌드 및 갭 분석

## Firecracker와의 관계

K8s 스케줄링 알고리즘의 포괄적 리뷰는 Firecracker microVM을 K8s에서 운영할 때의 스케줄링 전략 선택에 도움이 된다. microVM의 경량성을 고려한 스케줄링 최적화 방향을 이 서베이에서 도출할 수 있다.

## 키워드

Kubernetes, 스케줄링 알고리즘, 서베이, ML 스케줄링, QoS, 자원 효율성
