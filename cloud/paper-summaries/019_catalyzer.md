# Catalyzer: Sub-millisecond Startup for Serverless Computing

- **참고문헌 번호:** 19
- **저자:** Du et al.
- **학회/출처:** ASPLOS, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Catalyzer+Sub-millisecond+Startup+for+Serverless+Computing)
- **분류:** 확장

## 요약

Catalyzer는 gVisor 기반의 init-less 부팅과 sfork 메커니즘을 통해 1ms 미만의 서버리스 함수 시작 시간을 달성한다. 기존 초기화 과정의 중복을 제거하고, 사전 초기화된 샌드박스 상태를 효율적으로 복제하는 sfork를 도입하여 극초단 시작 시간을 실현한다.

## 핵심 기여

- gVisor 기반 init-less 부팅 메커니즘
- sfork를 통한 사전 초기화 상태 복제
- 1ms 미만의 서버리스 함수 시작 시간
- 초기화 경로 중복 제거

## Firecracker와의 관계

Firecracker의 스냅샷/복원 개념을 gVisor 환경에서 확장한 연구이다. Firecracker의 ~125ms 부팅 시간을 훨씬 뛰어넘는 <1ms 시작을 달성하며, init-less 부팅과 sfork 개념은 Firecracker의 MicroVM 시작 최적화에도 영감을 줄 수 있다.

## 키워드

Catalyzer, Init-less Boot, sfork, Sub-millisecond, gVisor, Serverless Startup
