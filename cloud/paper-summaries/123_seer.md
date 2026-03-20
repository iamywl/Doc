# Seer: Leveraging Big Data to Navigate the Complexity of Performance Debugging in Cloud Microservices

- **참고문헌 번호:** 123
- **저자:** Gan et al.
- **학회/출처:** ASPLOS, 2019
- **논문 링크:** https://scholar.google.com/scholar?q=Seer+Performance+Debugging+Cloud+Microservices+Gan
- **분류:** 보완

## 요약

Seer는 딥러닝을 활용하여 클라우드 마이크로서비스의 QoS 위반을 예측하고 근본 원인을 분석하는 시스템이다. 분산 트레이싱 데이터를 딥러닝 모델로 분석하여 성능 문제를 사전에 감지하고, 영향을 받는 서비스의 근본 원인을 자동으로 식별한다.

## 핵심 기여

- 딥러닝 기반 QoS 위반 예측
- 자동 근본 원인 분석(root cause analysis)
- 분산 트레이싱 데이터 활용
- 마이크로서비스 성능 디버깅 자동화

## Firecracker와의 관계

Seer의 성능 디버깅 기법은 Firecracker microVM 위에서 실행되는 마이크로서비스의 성능 문제를 진단하는 데 활용될 수 있다. microVM 수준의 자원 경합이 마이크로서비스 성능에 미치는 영향을 자동으로 식별할 수 있다.

## 키워드

Seer, 딥러닝, 성능 디버깅, QoS 예측, 근본 원인 분석, 마이크로서비스, 트레이싱
