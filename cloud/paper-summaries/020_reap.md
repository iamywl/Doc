# REAP: Benchmarking, Analysis, and Optimization of Serverless Function Snapshots (vHive)

- **참고문헌 번호:** 20
- **저자:** Ustiugov et al.
- **학회/출처:** ASPLOS, 2021
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=REAP+Benchmarking+Analysis+Optimization+Serverless+Function+Snapshots+vHive)
- **분류:** 확장

## 요약

REAP는 서버리스 함수 스냅샷의 Record-and-Prefetch 기법을 통해 콜드 스타트 시간을 3.7배 단축하는 연구이다. vHive 벤치마크 프레임워크를 통해 스냅샷 기반 복원의 병목을 체계적으로 분석하고, 페이지 접근 패턴을 기록하여 필요한 페이지를 사전에 프리페치한다.

## 핵심 기여

- Record-and-Prefetch 기법으로 3.7배 콜드 스타트 감소
- vHive 벤치마크 프레임워크 개발
- 스냅샷 복원 병목 지점의 체계적 분석
- 페이지 접근 패턴 기록 및 사전 프리페치

## Firecracker와의 관계

Firecracker의 스냅샷/복원 기능을 직접 확장한 연구이다. Firecracker MicroVM의 스냅샷 복원 시 발생하는 페이지 폴트를 Record-and-Prefetch로 최적화하여 콜드 스타트를 대폭 줄이며, vHive는 Firecracker 기반의 서버리스 벤치마크 도구이다.

## 키워드

REAP, vHive, Snapshot, Record-and-Prefetch, Cold Start, Page Fault, Firecracker
