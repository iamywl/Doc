# Boki: Stateful Serverless Computing with Shared Logs

- **참고문헌 번호:** 67
- **저자:** Jia, Witchel
- **학회/출처:** SOSP 2021
- **논문 링크:** https://scholar.google.com/scholar?q=Boki+Stateful+Serverless+Computing+with+Shared+Logs
- **분류:** 확장

## 요약

Boki는 공유 로그(shared log) API와 메타로그(metalog) 메커니즘을 활용하여 서버리스 함수에 상태 유지 기능을 제공하는 시스템이다. 기존 무상태 서버리스의 한계를 극복하여 4.7배의 성능 가속을 달성하며, 상태 관리의 일관성과 내결함성을 보장한다.

## 핵심 기여

- 공유 로그 기반 상태 유지 서버리스 컴퓨팅 프레임워크
- 메타로그를 통한 로그 간 일관성 보장 메커니즘
- 기존 대비 4.7배 성능 가속
- 상태 유지와 서버리스의 탄력성을 동시에 달성

## Firecracker와의 관계

Firecracker microVM은 기본적으로 무상태로 설계되어 있어, 함수 간 상태 공유가 어렵다. Boki의 공유 로그 메커니즘을 Firecracker 기반 서버리스 플랫폼에 통합하면, microVM의 격리성을 유지하면서도 효율적인 상태 관리가 가능해진다.

## 키워드

stateful serverless, shared log, metalog, consistency, fault tolerance
