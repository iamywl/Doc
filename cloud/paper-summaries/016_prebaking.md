# Prebaking Functions to Warm the Serverless Cold Start

- **참고문헌 번호:** 16
- **저자:** Silva et al.
- **학회/출처:** Middleware, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Prebaking+Functions+to+Warm+the+Serverless+Cold+Start)
- **분류:** 유사

## 요약

이 연구는 JVM 기반 서버리스 함수의 콜드 스타트 문제를 프로세스 상태 영속화(process state persistence)를 통해 해결한다. JVM의 초기화된 상태를 사전에 저장(prebake)하고 함수 호출 시 이를 복원함으로써 JVM의 긴 시작 시간을 회피한다.

## 핵심 기여

- JVM 프로세스 상태 영속화 기법
- 사전 베이킹(prebaking)을 통한 콜드 스타트 제거
- JVM 서버리스 함수의 시작 시간 대폭 단축
- CRIU 기반 체크포인트/복원 활용

## Firecracker와의 관계

Firecracker의 VM 스냅샷이 전체 MicroVM 상태를 저장하는 반면, prebaking은 애플리케이션(JVM) 수준에서 상태를 영속화한다. 두 기법은 서로 다른 계층에서 콜드 스타트를 해결하며, Firecracker MicroVM 내에서 prebaking을 적용하여 시너지를 낼 수 있다.

## 키워드

Prebaking, JVM, Cold Start, Process State Persistence, CRIU, Serverless
