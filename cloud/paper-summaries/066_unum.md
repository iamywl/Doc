# Unum: Cloud Serverless Application Coordination

- **참고문헌 번호:** 66
- **저자:** Liu et al.
- **학회/출처:** NSDI 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Unum+Cloud+Serverless+Application+Coordination
- **분류:** 확장

## 요약

Unum은 중앙 오케스트레이터 없이 서버리스 워크플로를 조율하는 탈중앙화 시스템이다. 각 함수가 자체적으로 다음 실행 단계를 결정하는 분산 조율 프로토콜을 통해 중앙 오케스트레이터의 병목과 단일 장애점 문제를 해결한다.

## 핵심 기여

- 중앙 오케스트레이터 없는 탈중앙화 워크플로 조율 메커니즘
- 분산 조율 프로토콜을 통한 확장성 향상
- 중앙 병목 및 단일 장애점 제거
- 기존 중앙 집중식 오케스트레이션 대비 확장성과 내결함성 개선

## Firecracker와의 관계

Firecracker 기반 서버리스 플랫폼에서 AWS Step Functions 같은 중앙 오케스트레이터의 병목을 해소하는 보완적 접근법이다. 각 Firecracker microVM이 자율적으로 워크플로를 조율함으로써 대규모 워크플로의 확장성을 높일 수 있다.

## 키워드

decentralized coordination, serverless workflow, orchestrator-free, distributed protocol, scalability
