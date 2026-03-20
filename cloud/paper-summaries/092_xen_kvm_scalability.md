# Scalability Comparison of Xen and KVM

- **참고문헌 번호:** 92
- **저자:** Soriga, Barbulescu
- **학회/출처:** IEEE ECAI, 2013
- **논문 링크:** https://scholar.google.com/scholar?q=Scalability+Comparison+Xen+KVM+Soriga+Barbulescu
- **분류:** 기반

## 요약

VM 수가 증가함에 따른 Xen과 KVM의 확장성을 비교 분석한다. 동시 실행 VM 수를 늘려가며 CPU, 메모리, 네트워크 성능의 변화를 측정하고, 각 하이퍼바이저의 확장성 한계를 식별한다. 대규모 가상화 환경에서의 하이퍼바이저 선택에 대한 실증적 근거를 제공한다.

## 핵심 기여

- VM 수 증가에 따른 확장성 체계적 비교
- Xen과 KVM의 확장성 한계점 식별
- 리소스 경합 패턴 분석
- 대규모 배포 환경에서의 하이퍼바이저 선택 근거 제공

## Firecracker와의 관계

Firecracker는 수천 개의 microVM을 단일 호스트에서 실행하는 것을 목표로 하므로, KVM의 확장성 특성은 직접적으로 관련된다. 이 논문의 KVM 확장성 분석은 Firecracker의 고밀도 배포 가능성의 기반을 이해하는 데 중요하다.

## 키워드

Xen, KVM, 확장성, VM 밀도, 리소스 경합, 대규모 가상화, 성능 저하
