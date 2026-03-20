# Sparrow: Distributed, Low Latency Scheduling

- **참고문헌 번호:** 101
- **저자:** Ousterhout et al.
- **학회/출처:** SOSP, 2013
- **논문 링크:** https://scholar.google.com/scholar?q=Sparrow+Distributed+Low+Latency+Scheduling+Ousterhout
- **분류:** 확장

## 요약

Sparrow는 분산 스케줄링 시스템으로, 무작위 샘플링(random sampling)을 통한 분산 스케줄링 결정을 수행한다. 중앙 스케줄러의 병목을 제거하면서도 최적 대비 12% 이내의 성능을 달성한다. 밀리초 단위의 짧은 작업에도 낮은 스케줄링 지연을 보장하는 것이 핵심이다.

## 핵심 기여

- 무작위 샘플링 기반 분산 스케줄링 알고리즘 (Power of Two Choices)
- 중앙 스케줄러 없이 최적 대비 12% 이내 성능 달성
- 밀리초 단위 작업에 대한 낮은 스케줄링 오버헤드
- 배치 샘플링(batch sampling)을 통한 부하 분산

## Firecracker와의 관계

Sparrow의 저지연 분산 스케줄링은 서버리스 함수 호출 시 Firecracker microVM을 빠르게 배치하는 데 활용될 수 있다. 밀리초 단위의 빠른 스케줄링 결정은 Firecracker의 빠른 부팅 시간과 결합하여 종단 간 지연을 최소화한다.

## 키워드

Sparrow, 분산 스케줄링, 무작위 샘플링, 저지연, Power of Two Choices, 배치 샘플링
