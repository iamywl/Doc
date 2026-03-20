# Amazon Prime Video: Monolith Migration

- **참고문헌 번호:** 53
- **저자:** Kolny
- **학회/출처:** Amazon 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Amazon+Prime+Video+Monolith+Migration
- **분류:** 비판

## 요약

Amazon Prime Video 팀이 마이크로서비스 기반 아키텍처에서 모놀리스로 전환하여 인프라 비용을 90% 절감한 사례를 보고한다. 서버리스 및 마이크로서비스 아키텍처가 항상 최적의 선택이 아님을 실증적으로 보여주며, 워크로드 특성에 따라 모놀리식 설계가 더 효율적일 수 있음을 시사한다.

## 핵심 기여

- 마이크로서비스에서 모놀리스로의 역전환 실제 사례 제시
- 90%의 비용 절감이라는 극적인 결과 보고
- 서버리스 아키텍처의 과도한 적용에 대한 실증적 비판
- 워크로드 특성에 맞는 아키텍처 선택의 중요성 강조

## Firecracker와의 관계

Firecracker가 서버리스 컴퓨팅의 핵심 인프라인 AWS Lambda의 기반 기술인 점에서, 이 사례는 서버리스가 모든 워크로드에 적합하지 않다는 반례를 제시한다. 지속적으로 높은 처리량이 필요한 워크로드에서는 Firecracker 기반 서버리스보다 모놀리식 배포가 비용 효율적일 수 있음을 보여준다.

## 키워드

microservices, monolith, cost reduction, serverless criticism, architecture migration
