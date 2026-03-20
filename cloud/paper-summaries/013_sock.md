# SOCK: Rapid Task Provisioning with Serverless-Optimized Containers

- **참고문헌 번호:** 13
- **저자:** Oakes et al.
- **학회/출처:** ATC, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=SOCK+Rapid+Task+Provisioning+with+Serverless+Optimized+Containers)
- **분류:** 유사

## 요약

SOCK는 서버리스 환경에 최적화된 경량 컨테이너 시스템으로, Zygote 프로비저닝 기법을 활용하여 45배의 시작 속도 향상을 달성한다. Python 런타임의 import 캐싱, lean 컨테이너 설계 등을 통해 서버리스 함수의 콜드 스타트 문제를 효과적으로 해결한다.

## 핵심 기여

- 서버리스 최적화 경량 컨테이너 아키텍처
- Zygote 프로비저닝을 통한 45배 시작 속도 향상
- Python 패키지 import 캐싱 메커니즘
- 콜드 스타트 원인 분석 및 체계적 해결

## Firecracker와의 관계

Firecracker가 VM 수준의 격리로 콜드 스타트를 해결하는 반면, SOCK는 컨테이너 수준에서 프로비저닝을 최적화한다. SOCK의 Zygote 기법은 Firecracker의 스냅샷 기반 복원과 유사한 목표를 가지지만, 구현 계층이 다르다.

## 키워드

SOCK, Serverless, Container Optimization, Zygote Provisioning, Cold Start, Python
