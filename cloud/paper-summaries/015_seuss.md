# SEUSS: Skip Redundant Paths to Make Serverless Fast

- **참고문헌 번호:** 15
- **저자:** Cadden et al.
- **학회/출처:** EuroSys, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=SEUSS+Skip+Redundant+Paths+to+Make+Serverless+Fast)
- **분류:** 유사

## 요약

SEUSS는 유니커널과 x86 dirty bit 기반 스냅샷 기법을 결합하여 서버리스 함수의 시작 시간을 대폭 단축한다. 초기화된 유니커널의 메모리 스냅샷을 x86 페이지 테이블의 dirty bit를 활용하여 효율적으로 캡처하고 복원함으로써 중복 초기화 경로를 건너뛴다.

## 핵심 기여

- 유니커널 + x86 dirty bit 기반 스냅샷 메커니즘
- 중복 초기화 경로 제거를 통한 빠른 시작
- 효율적인 메모리 스냅샷 캡처 및 복원
- 서버리스 워크로드에 최적화된 유니커널 활용

## Firecracker와의 관계

Firecracker의 스냅샷/복원 기능과 유사한 목표를 가지지만, 유니커널 기반에서 x86 하드웨어 기능(dirty bit)을 직접 활용하는 점이 다르다. SEUSS의 스냅샷 기법은 Firecracker의 MicroVM 스냅샷을 보완하는 아이디어를 제공한다.

## 키워드

SEUSS, Unikernel, Snapshot, x86 Dirty Bit, Cold Start, Serverless Optimization
