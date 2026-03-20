# Sage: Practical and Scalable ML-Driven Performance Debugging in Microservices

- **참고문헌 번호:** 124
- **저자:** Gan et al.
- **학회/출처:** ASPLOS, 2021
- **논문 링크:** https://scholar.google.com/scholar?q=Sage+Practical+Scalable+ML+Performance+Debugging+Microservices
- **분류:** 보완

## 요약

Sage는 Seer를 확장한 ML 기반 성능 디버깅 시스템으로, 대규모 마이크로서비스 환경에서의 확장성을 개선한다. 인과 추론(causal inference)과 ML을 결합하여 더 정확한 근본 원인 분석을 수행하며, 실제 생산 환경에 적용 가능한 실용적 시스템을 제공한다.

## 핵심 기여

- Seer를 확장한 확장 가능한 성능 디버깅
- 인과 추론과 ML의 결합
- 대규모 마이크로서비스 환경에서의 적용 가능성
- 실용적 생산 환경 배포 고려

## Firecracker와의 관계

Sage의 확장 가능한 성능 디버깅은 수천 개의 Firecracker microVM이 실행되는 대규모 서버리스 환경에서의 성능 문제 진단에 적합하다. microVM 간 복잡한 의존성에서의 성능 저하 원인을 자동으로 추적할 수 있다.

## 키워드

Sage, ML 디버깅, 성능 분석, 인과 추론, 마이크로서비스, 확장성, 근본 원인
