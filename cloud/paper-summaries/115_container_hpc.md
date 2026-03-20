# Performance Evaluation of Container-based Virtualization for High Performance Computing Environments

- **참고문헌 번호:** 115
- **저자:** Xavier et al.
- **학회/출처:** IEEE PDP, 2013
- **논문 링크:** https://scholar.google.com/scholar?q=Performance+Evaluation+Container+Virtualization+High+Performance+Computing+Xavier
- **분류:** 기반

## 요약

HPC 환경에서 LXC 컨테이너와 Xen 가상 머신의 성능을 비교 평가한다. Xen이 31%의 성능 오버헤드를 보이는 반면, LXC 컨테이너는 네이티브에 근접한 성능을 달성한다. HPC 워크로드 특성에 따른 가상화 기술의 성능 영향을 분석한다.

## 핵심 기여

- HPC 환경에서의 LXC vs Xen 성능 비교
- Xen의 31% 성능 오버헤드 정량화
- HPC 워크로드별 가상화 영향 분석
- 컨테이너의 HPC 적합성 입증

## Firecracker와의 관계

이 논문이 보여주는 전통적 VM의 HPC 성능 오버헤드는 Firecracker와 같은 경량 VM의 필요성을 뒷받침한다. Firecracker의 최소 디바이스 모델은 VM의 성능 오버헤드를 줄여 컨테이너에 가까운 성능을 달성하는 데 기여한다.

## 키워드

HPC, LXC, Xen, 컨테이너, 성능 평가, 오버헤드, 고성능 컴퓨팅
