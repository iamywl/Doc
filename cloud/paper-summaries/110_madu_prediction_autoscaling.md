# The Power of Prediction: Microservice Auto Scaling via Workload Learning

- **참고문헌 번호:** 110
- **저자:** Luo et al.
- **학회/출처:** SoCC, 2022
- **논문 링크:** https://scholar.google.com/scholar?q=Power+Prediction+Microservice+Auto+Scaling+Workload+Learning+Luo
- **분류:** 확장

## 요약

Madu는 워크로드 학습을 통한 예측 기반(proactive) 마이크로서비스 오토스케일러이다. 과거 워크로드 패턴을 학습하여 미래 부하를 예측하고 선제적으로 스케일링한다. 자원 사용량 40% 감소와 지연 시간 36% 감소를 동시에 달성하여 반응적(reactive) 오토스케일링의 한계를 극복한다.

## 핵심 기여

- 워크로드 학습 기반 예측적 오토스케일링
- 자원 사용량 40% 감소 달성
- 지연 시간 36% 감소 달성
- 반응적 스케일링 대비 선제적 스케일링의 우수성 입증

## Firecracker와의 관계

예측 기반 오토스케일링은 Firecracker 서버리스 환경에서 콜드 스타트 문제를 해결하는 데 핵심적이다. 워크로드 예측을 통해 microVM을 사전에 준비하면 콜드 스타트 지연을 크게 줄일 수 있다.

## 키워드

Madu, 예측 오토스케일링, 워크로드 학습, 마이크로서비스, 선제적 스케일링, 자원 효율
