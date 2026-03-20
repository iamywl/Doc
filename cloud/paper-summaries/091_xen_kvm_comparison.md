# Quantitative Comparison of Xen and KVM

- **참고문헌 번호:** 91
- **저자:** Deshane et al.
- **학회/출처:** Xen Summit, 2008
- **논문 링크:** https://scholar.google.com/scholar?q=Quantitative+Comparison+Xen+KVM+Deshane
- **분류:** 기반

## 요약

Xen과 KVM 하이퍼바이저의 정량적 성능 비교를 수행한 연구이다. CPU, 메모리, I/O, 네트워크 등 다양한 차원에서 두 하이퍼바이저의 성능을 체계적으로 측정하고 비교한다. 각 하이퍼바이저의 아키텍처적 차이가 성능에 미치는 영향을 분석한다.

## 핵심 기여

- Xen과 KVM의 체계적 성능 비교 프레임워크
- CPU, 메모리, I/O, 네트워크 성능 정량적 측정
- 아키텍처 차이(Type-1 vs Type-2)에 따른 성능 영향 분석
- 워크로드별 최적 하이퍼바이저 선택 가이드라인

## Firecracker와의 관계

Firecracker는 KVM을 기반으로 구현되었으며, 이 논문의 KVM 성능 분석 결과는 Firecracker의 성능 특성을 이해하는 기반이 된다. Xen 대비 KVM의 장단점 분석은 Firecracker가 KVM을 선택한 근거를 이해하는 데 도움이 된다.

## 키워드

Xen, KVM, 하이퍼바이저 비교, CPU 성능, I/O 성능, 네트워크 성능, 벤치마크
