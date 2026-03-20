# Firmament: Fast, Centralized Cluster Scheduling at Scale

- **참고문헌 번호:** 102
- **저자:** Gog et al.
- **학회/출처:** OSDI, 2016
- **논문 링크:** https://scholar.google.com/scholar?q=Firmament+Fast+Centralized+Cluster+Scheduling+Scale
- **분류:** 확장

## 요약

Firmament는 최소 비용 최대 유량(MCMF, Min-Cost Max-Flow) 최적화를 사용하는 중앙 집중식 클러스터 스케줄러이다. 서브초(sub-second) 단위의 배치 결정을 달성하며, Quincy 대비 20배 이상의 성능 향상을 보여준다. 대규모 클러스터에서도 최적에 가까운 배치 품질과 빠른 스케줄링 속도를 동시에 달성한다.

## 핵심 기여

- MCMF 기반 최적 스케줄링 알고리즘
- 서브초 단위 배치 결정 달성
- Quincy 대비 20배 이상 성능 향상
- 점진적(incremental) 그래프 업데이트로 효율성 확보

## Firecracker와의 관계

Firmament의 빠르고 최적에 가까운 스케줄링은 대규모 Firecracker microVM 배치에 활용될 수 있다. 서브초 단위 스케줄링 결정은 서버리스 환경에서의 빠른 함수 실행 요구사항과 일치한다.

## 키워드

Firmament, MCMF, 중앙 스케줄링, 서브초 배치, 최적화, Quincy, 그래프 기반
