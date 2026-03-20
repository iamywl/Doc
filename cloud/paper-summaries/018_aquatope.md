# AQUATOPE: QoS-and-Uncertainty-Aware Resource Management

- **참고문헌 번호:** 18
- **저자:** Zhou et al.
- **학회/출처:** ASPLOS, 2023
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=AQUATOPE+QoS+Uncertainty+Aware+Resource+Management+Serverless)
- **분류:** 유사

## 요약

AQUATOPE는 베이지안 예측 모델을 활용하여 서버리스 환경의 QoS를 보장하는 리소스 관리 시스템이다. 함수 호출 패턴의 불확실성을 모델링하여 사전 웜업(prewarming) 전략을 최적화하고, 5배의 QoS 개선을 달성한다. 콜드 스타트 예방을 위한 지능형 예측 기반 접근법이다.

## 핵심 기여

- 베이지안 예측 기반의 불확실성 인지 리소스 관리
- 5배의 QoS 개선 달성
- 지능형 사전 웜업(prewarming) 전략
- 서버리스 워크로드의 불확실성 모델링

## Firecracker와의 관계

Firecracker MicroVM의 콜드 스타트 문제를 상위 레벨에서 해결하는 보완적 연구이다. AQUATOPE의 예측 기반 사전 웜업은 Firecracker MicroVM을 언제 미리 부팅해야 하는지를 결정하는 데 활용될 수 있다.

## 키워드

AQUATOPE, Bayesian Prediction, QoS, Prewarming, Resource Management, Uncertainty, Serverless
