# Shenango: Achieving High CPU Efficiency for Latency-sensitive Datacenter Workloads

- **참고문헌 번호:** 121
- **저자:** Ousterhout et al.
- **학회/출처:** NSDI, 2019
- **논문 링크:** https://scholar.google.com/scholar?q=Shenango+High+CPU+Efficiency+Latency-sensitive+Datacenter+Workloads
- **분류:** 확장

## 요약

Shenango는 마이크로초(microsecond) 단위로 CPU 코어를 재할당하여 지연 시간에 민감한 데이터센터 워크로드의 CPU 효율을 극대화하는 시스템이다. 전용 IOKernel이 5마이크로초마다 코어 할당을 결정하여, 유휴 코어를 최소화하면서도 테일 지연 시간을 보장한다.

## 핵심 기여

- 마이크로초 단위 코어 재할당 메커니즘
- 전용 IOKernel을 통한 초고속 스케줄링
- CPU 효율과 테일 지연 시간의 동시 최적화
- 혼합 워크로드 환경에서의 성능 검증

## Firecracker와의 관계

Shenango의 마이크로초 단위 코어 재할당은 Firecracker microVM의 CPU 자원을 더 효율적으로 관리하는 데 영감을 줄 수 있다. 서버리스 워크로드의 간헐적 특성에 맞춘 초고속 자원 재할당은 microVM 밀도를 높이는 데 기여한다.

## 키워드

Shenango, 마이크로초 코어 재할당, IOKernel, CPU 효율, 테일 지연, 데이터센터
