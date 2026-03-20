# Dirigent: Lightweight Serverless Orchestration

- **참고문헌 번호:** 56
- **저자:** Cvetkovic et al.
- **학회/출처:** SOSP 2024
- **논문 링크:** https://scholar.google.com/scholar?q=Dirigent+Lightweight+Serverless+Orchestration
- **분류:** 확장

## 요약

Dirigent는 기존 서버리스 오케스트레이션 시스템의 한계를 극복하기 위해 처음부터 새로 설계된 경량 FaaS 오케스트레이션 시스템이다. AWS Lambda 대비 2.79배, Knative 대비 1250배 향상된 성능을 달성하며, 서버리스 제어 플레인의 효율성을 크게 개선한다.

## 핵심 기여

- Clean-slate FaaS 오케스트레이션 아키텍처 설계
- AWS Lambda 대비 2.79배 성능 향상
- Knative 대비 1250배 성능 향상
- 경량 제어 플레인을 통한 오케스트레이션 오버헤드 최소화

## Firecracker와의 관계

Firecracker가 데이터 플레인(VM 격리)에 집중하는 반면, Dirigent는 제어 플레인(함수 오케스트레이션)을 최적화한다. Firecracker microVM 위에 Dirigent의 경량 오케스트레이션을 결합하면 전체 서버리스 스택의 성능을 더욱 향상시킬 수 있다.

## 키워드

serverless orchestration, control plane, FaaS, lightweight, clean-slate design
