# WISEFUSE: Workload Characterization and DAG Transformation

- **참고문헌 번호:** 64
- **저자:** Mahgoub et al.
- **학회/출처:** SIGMETRICS 2022 (Best Paper)
- **논문 링크:** https://scholar.google.com/scholar?q=WISEFUSE+Workload+Characterization+and+DAG+Transformation
- **분류:** 확장

## 요약

WISEFUSE는 서버리스 워크플로의 DAG(Directed Acyclic Graph) 구조를 자동으로 변환하여 성능을 최적화하는 시스템이다. 함수 병합(function merging) 등의 DAG 변환 기법을 통해 67%의 지연 시간 개선을 달성하며, SIGMETRICS 2022 Best Paper로 선정되었다.

## 핵심 기여

- 서버리스 워크플로 DAG의 자동 변환 프레임워크
- 함수 병합을 통한 67% 지연 시간 개선
- 워크로드 특성 기반 최적 DAG 구조 탐색
- SIGMETRICS 2022 Best Paper 수상

## Firecracker와의 관계

WISEFUSE의 함수 병합 기법은 여러 Firecracker microVM에서 실행되던 함수들을 하나로 통합하여 VM 간 통신 오버헤드와 콜드 스타트를 줄일 수 있다. Firecracker 기반 서버리스 플랫폼의 워크플로 최적화 계층으로 활용할 수 있다.

## 키워드

DAG transformation, function merging, workflow optimization, workload characterization, latency reduction
