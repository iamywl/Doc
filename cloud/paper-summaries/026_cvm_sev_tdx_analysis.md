# Confidential VMs Explained: An Empirical Analysis of AMD SEV-SNP and Intel TDX

- **참고문헌 번호:** 26
- **저자:** Misono et al.
- **학회/출처:** SIGMETRICS, 2025
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Confidential+VMs+Explained+An+Empirical+Analysis+of+AMD+SEV-SNP+and+Intel+TDX)
- **분류:** 보완, SEV/TDX 분석

## 요약

AMD SEV-SNP와 Intel TDX 두 가지 주요 기밀 VM 기술의 성능을 실증적으로 비교 분석한 논문이다. 하드웨어 암호화 메모리의 오버헤드, 컨텍스트 전환 비용, I/O 성능 등을 체계적으로 측정하여, 각 기술의 성능 특성과 트레이드오프를 규명한다.

## 핵심 기여

- AMD SEV-SNP와 Intel TDX의 포괄적인 성능 비교 벤치마크 제공
- 하드웨어 메모리 암호화가 다양한 워크로드에 미치는 성능 영향 정량화
- 두 플랫폼 간 아키텍처적 차이가 성능에 미치는 영향 분석
- 기밀 VM 도입을 위한 실용적 가이드라인 제시

## Firecracker와의 관계

Firecracker microVM에 기밀 컴퓨팅 기능을 통합할 때 참고할 수 있는 성능 데이터를 제공한다. SEV-SNP/TDX 기반 기밀 microVM 구현 시 예상되는 오버헤드를 이해하고, 최적의 하드웨어 플랫폼을 선택하는 데 도움을 준다.

## 키워드

AMD SEV-SNP, Intel TDX, Confidential VM, 성능 분석, 메모리 암호화, 벤치마크
