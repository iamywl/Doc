# Nightcore: Efficient and Scalable Serverless Computing

- **참고문헌 번호:** 14
- **저자:** Jia, Witchel
- **학회/출처:** ASPLOS, 2021
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Nightcore+Efficient+and+Scalable+Serverless+Computing)
- **분류:** 유사

## 요약

Nightcore는 마이크로초 단위의 오버헤드로 서버리스 함수를 실행할 수 있는 고성능 런타임이다. 100마이크로초 미만의 함수 호출 오버헤드와 초당 100K 이상의 호출 처리량을 달성하여, 내부 마이크로서비스 간 통신과 같은 지연시간에 민감한 워크로드를 효율적으로 처리한다.

## 핵심 기여

- 100마이크로초 미만의 함수 호출 오버헤드
- 초당 100K 이상의 호출 처리량
- 효율적인 IPC 메커니즘 설계
- 마이크로서비스 워크로드에 최적화된 스케줄링

## Firecracker와의 관계

Firecracker가 격리와 보안에 초점을 맞추는 반면, Nightcore는 서버리스 함수 간 호출의 지연시간 최소화에 집중한다. Firecracker의 MicroVM 위에서 Nightcore 런타임을 실행하여 격리와 성능을 모두 확보하는 조합이 가능하다.

## 키워드

Nightcore, Microsecond Serverless, Low Latency, High Throughput, Function Invocation, ASPLOS
