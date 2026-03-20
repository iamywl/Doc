# Jolteon: Unleashing the Promise of Serverless for Serverless Workflows

- **참고문헌 번호:** 61
- **저자:** Zhang et al.
- **학회/출처:** NSDI 2024
- **논문 링크:** https://scholar.google.com/scholar?q=Jolteon+Unleashing+the+Promise+of+Serverless+for+Serverless+Workflows
- **분류:** 확장

## 요약

Jolteon은 확률적 성능 모델을 기반으로 서버리스 워크플로의 비용과 지연 시간을 동시에 최적화하는 시스템이다. 워크플로 내 각 함수의 자원 구성을 자동으로 결정하여 비용 2.3배, 지연 시간 2.1배 개선을 달성한다.

## 핵심 기여

- 서버리스 워크플로를 위한 확률적 성능 모델 설계
- 비용 2.3배, 지연 시간 2.1배 동시 개선
- 워크플로 수준의 자동 자원 구성 최적화
- 비용-성능 트레이드오프의 체계적 탐색 방법론

## Firecracker와의 관계

Firecracker 기반 서버리스 플랫폼에서 워크플로를 실행할 때, Jolteon의 확률적 모델을 적용하면 각 microVM의 자원 할당을 최적화하고 전체 워크플로의 비용과 성능을 동시에 개선할 수 있다.

## 키워드

serverless workflows, probabilistic model, cost optimization, latency optimization, resource configuration
