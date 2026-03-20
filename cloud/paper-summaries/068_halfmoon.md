# Halfmoon: Log-Optimal Fault-Tolerant Stateful Serverless Computing

- **참고문헌 번호:** 68
- **저자:** Qi et al.
- **학회/출처:** SOSP 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Halfmoon+Log-Optimal+Fault-Tolerant+Stateful+Serverless+Computing
- **분류:** 확장

## 요약

Halfmoon은 Boki의 후속 연구로, 로그 최적(log-optimal) 프로토콜을 통해 상태 유지 서버리스의 내결함성을 개선한다. Boki 대비 20-40%의 지연 시간 감소를 달성하면서도 이론적으로 최적의 로그 사용량을 보장한다.

## 핵심 기여

- 이론적으로 최적의 로그 기반 내결함성 프로토콜 설계
- Boki 대비 20-40% 지연 시간 감소
- 로그 사용량의 이론적 하한 도출 및 달성
- 상태 유지 서버리스의 내결함성과 성능 동시 최적화

## Firecracker와의 관계

Firecracker 기반 상태 유지 서버리스 시스템 구현 시, Halfmoon의 로그 최적 프로토콜을 적용하면 최소한의 로깅 오버헤드로 내결함성을 확보할 수 있다. Boki보다 효율적인 상태 관리 계층을 Firecracker microVM 위에 구축할 수 있다.

## 키워드

log-optimal, fault tolerance, stateful serverless, latency reduction, Boki improvement
