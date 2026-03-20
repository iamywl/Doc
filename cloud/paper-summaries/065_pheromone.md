# Pheromone: Application-Aware Serverless Function Scheduling

- **참고문헌 번호:** 65
- **저자:** Yu et al.
- **학회/출처:** NSDI 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Pheromone+Application-Aware+Serverless+Function+Scheduling
- **분류:** 확장

## 요약

Pheromone은 데이터 흐름을 인식하는 서버리스 함수 배치 시스템으로, 함수 간 데이터 의존성을 분석하여 관련 함수들을 가까운 노드에 배치한다. 개미 페로몬에서 영감을 받은 이름처럼, 데이터 흐름 경로를 추적하여 최적의 함수 배치를 결정한다.

## 핵심 기여

- 데이터 흐름 인식 기반 서버리스 함수 배치 전략
- 애플리케이션 수준의 데이터 의존성 분석
- 데이터 이동 비용을 최소화하는 함수 배치 알고리즘
- 다양한 서버리스 워크로드에 대한 적응형 스케줄링

## Firecracker와의 관계

Firecracker microVM의 배치 시 데이터 흐름을 고려하면, 함수 체인 내 microVM 간 네트워크 통신 오버헤드를 줄일 수 있다. Pheromone의 데이터 흐름 인식 배치를 Firecracker 기반 서버리스 플랫폼에 적용하여 워크플로 성능을 향상시킬 수 있다.

## 키워드

data flow-aware scheduling, function placement, application-aware, serverless optimization, data locality
