# Hermod: Principled and Practical Scheduling for Serverless Functions

- **참고문헌 번호:** 57
- **저자:** Kaffes et al.
- **학회/출처:** SoCC 2022
- **논문 링크:** https://scholar.google.com/scholar?q=Hermod+Principled+and+Practical+Scheduling+for+Serverless+Functions
- **분류:** 확장

## 요약

Hermod는 서버리스 함수 스케줄링에 대한 체계적 분류 체계를 제시하고, 비용, 부하, 지역성을 동시에 고려하는 실용적 스케줄링 알고리즘을 제안한다. 기존 서버리스 스케줄러들의 한계를 분석하고, 다양한 최적화 목표를 균형 있게 달성하는 원칙적 접근법을 보여준다.

## 핵심 기여

- 서버리스 스케줄링에 대한 체계적 분류 체계(taxonomy) 제시
- 비용(cost), 부하(load), 지역성(locality) 인식 스케줄링 알고리즘
- 다양한 서버리스 스케줄링 전략의 비교 분석
- 실용적이면서도 원칙적인 스케줄링 프레임워크 설계

## Firecracker와의 관계

Firecracker microVM의 배치 및 스케줄링을 최적화하는 데 Hermod의 스케줄링 분류 체계와 알고리즘을 적용할 수 있다. 특히 콜드 스타트 최소화를 위한 지역성 인식 스케줄링은 Firecracker의 빠른 부팅 시간과 결합하여 전체 함수 실행 지연 시간을 줄이는 데 기여한다.

## 키워드

serverless scheduling, taxonomy, cost-aware, load balancing, locality-aware
