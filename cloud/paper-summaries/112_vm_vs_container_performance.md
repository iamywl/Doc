# An Updated Performance Comparison of Virtual Machines and Linux Containers

- **참고문헌 번호:** 112
- **저자:** Felter et al.
- **학회/출처:** IEEE ISPASS, 2015
- **논문 링크:** https://scholar.google.com/scholar?q=Updated+Performance+Comparison+Virtual+Machines+Linux+Containers+Felter
- **분류:** 기반

## 요약

KVM 가상 머신과 Docker 컨테이너의 성능을 체계적으로 비교한 연구이다. CPU, 메모리, 스토리지, 네트워크 등 다양한 차원에서 벤치마크를 수행하고, 각 가상화 기술의 성능 특성을 정량적으로 분석한다. 컨테이너가 대부분의 시나리오에서 네이티브에 가까운 성능을 보여주는 반면, VM은 I/O에서 상대적으로 높은 오버헤드를 보인다.

## 핵심 기여

- KVM vs Docker의 체계적 성능 비교
- CPU, 메모리, 스토리지, 네트워크 전 영역 벤치마크
- 각 가상화 기술의 성능 오버헤드 정량화
- 워크로드별 최적 가상화 기술 선택 근거 제공

## Firecracker와의 관계

이 논문의 KVM vs 컨테이너 성능 비교는 Firecracker가 해결하고자 하는 핵심 과제를 보여준다. Firecracker는 VM 수준의 격리를 유지하면서 컨테이너에 가까운 성능과 자원 효율을 달성하는 것을 목표로 한다.

## 키워드

KVM, Docker, 컨테이너, 가상 머신, 성능 비교, 벤치마크, I/O 오버헤드
