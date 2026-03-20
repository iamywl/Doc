# Durable Functions: Semantics for Stateful Serverless

- **참고문헌 번호:** 70
- **저자:** Burckhardt et al.
- **학회/출처:** OOPSLA 2021
- **논문 링크:** https://scholar.google.com/scholar?q=Durable+Functions+Semantics+for+Stateful+Serverless
- **분류:** 확장

## 요약

Azure Durable Functions의 상태 유지 서버리스 모델에 대한 형식적 의미론(formal semantics)을 제시하는 논문이다. 상태 유지 서버리스의 실행 모델을 수학적으로 정의하여 정확성 증명과 최적화의 기반을 제공한다.

## 핵심 기여

- Azure Durable Functions에 대한 형식적 의미론 정의
- 상태 유지 서버리스의 수학적 실행 모델 제시
- 형식적 기반을 통한 정확성 증명 가능
- 의미론에 기반한 체계적 최적화 방향 도출

## Firecracker와의 관계

Firecracker 기반 서버리스 플랫폼에서 상태 유지 함수를 지원할 때, Durable Functions의 형식적 의미론을 참고하여 정확한 실행 보장과 상태 관리 모델을 설계할 수 있다. 특히 microVM 재시작 시의 상태 복구 정확성을 형식적으로 검증하는 데 활용할 수 있다.

## 키워드

formal semantics, Azure Durable Functions, stateful serverless, correctness, execution model
