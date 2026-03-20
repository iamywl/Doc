# Tetris: Multi-Resource Packing for Cluster Schedulers

- **참고문헌 번호:** 103
- **저자:** Grandl et al.
- **학회/출처:** SIGCOMM, 2014
- **논문 링크:** https://scholar.google.com/scholar?q=Tetris+Multi-Resource+Packing+Cluster+Schedulers+Grandl
- **분류:** 확장

## 요약

Tetris는 다중 자원(CPU, 메모리, 디스크, 네트워크) 패킹을 위한 클러스터 스케줄러이다. 작업 완료 시간을 30-40% 개선하며, 다차원 빈 패킹(multi-dimensional bin packing) 문제에 대한 효율적인 휴리스틱을 제안한다. 자원 조각화(fragmentation)를 줄이면서 공정성도 유지한다.

## 핵심 기여

- 다중 자원 패킹 알고리즘 설계
- 작업 완료 시간 30-40% 개선
- 자원 조각화 감소와 공정성 동시 달성
- 실제 클러스터 트레이스 기반 평가

## Firecracker와의 관계

Tetris의 다중 자원 패킹 기법은 다양한 자원 요구량을 가진 Firecracker microVM을 효율적으로 배치하는 데 직접 적용 가능하다. microVM의 자원 프로파일에 따른 최적 배치는 호스트 자원 활용률을 극대화한다.

## 키워드

Tetris, 다중 자원 패킹, 빈 패킹, 자원 조각화, 스케줄링, 공정성, SIGCOMM
