# XFaaS: Hyperscale and Low Cost Serverless Functions at Meta

- **참고문헌 번호:** 55
- **저자:** Sahraei et al.
- **학회/출처:** SOSP 2023
- **논문 링크:** https://scholar.google.com/scholar?q=XFaaS+Hyperscale+and+Low+Cost+Serverless+Functions+at+Meta
- **분류:** 대안

## 요약

Meta의 프로덕션 서버리스 플랫폼 XFaaS를 소개하는 논문으로, 수조 건의 함수 호출을 처리하며 평균 CPU 사용률 66%를 달성한다. 하이퍼스케일 환경에서의 서버리스 설계 경험과 비용 최적화 전략을 상세히 기술하며, AWS Lambda와는 다른 설계 철학을 보여준다.

## 핵심 기여

- 수조 건 규모의 프로덕션 서버리스 시스템 설계 및 운영 경험 공유
- 평균 66% CPU 사용률 달성을 통한 높은 자원 효율성
- 하이퍼스케일 환경에 특화된 스케줄링 및 자원 관리 전략
- Meta 내부 워크로드 특성 분석 및 최적화 사례

## Firecracker와의 관계

AWS Lambda가 Firecracker microVM을 격리 단위로 사용하는 반면, Meta의 XFaaS는 다른 격리 메커니즘과 아키텍처를 채택한 대안적 서버리스 플랫폼이다. 특히 내부 신뢰 환경에서의 높은 자원 활용률 달성 방식은 Firecracker의 강력한 멀티 테넌트 격리와 대조적인 설계 트레이드오프를 보여준다.

## 키워드

hyperscale serverless, Meta, resource utilization, production FaaS, cost optimization
