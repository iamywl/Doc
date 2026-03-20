# Beldi: Fault-tolerant and Transactional Stateful Serverless Workflows

- **참고문헌 번호:** 69
- **저자:** Zhang et al.
- **학회/출처:** OSDI 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Beldi+Fault-tolerant+and+Transactional+Stateful+Serverless+Workflows
- **분류:** 확장

## 요약

Beldi는 로그 기반 exactly-once 실행 의미론을 제공하는 상태 유지 서버리스 워크플로 시스템이다. 서버리스 환경에서 트랜잭션과 내결함성을 보장하며, 재실행 시에도 정확히 한 번 실행을 보장하는 프로토콜을 제시한다.

## 핵심 기여

- 서버리스 환경에서의 exactly-once 실행 보장 프로토콜
- 로그 기반 내결함성 및 트랜잭션 지원
- 상태 유지 서버리스 워크플로의 정확성 보장
- 장애 복구 시에도 일관된 상태 유지

## Firecracker와의 관계

Firecracker microVM에서 실행되는 서버리스 함수가 장애로 중단될 경우, Beldi의 로그 기반 exactly-once 프로토콜을 통해 정확한 복구가 가능하다. Firecracker의 빠른 VM 재시작과 결합하여 신뢰성 높은 트랜잭션 서버리스 시스템을 구축할 수 있다.

## 키워드

exactly-once execution, fault tolerance, transactional serverless, log-based recovery, stateful workflow
