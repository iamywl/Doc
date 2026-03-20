# RContainer: ARM CCA-based Secure Containers

- **참고문헌 번호:** 83
- **저자:** Zhou et al.
- **학회/출처:** NDSS 2025
- **논문 링크:** https://scholar.google.com/scholar?q=RContainer+ARM+CCA-based+Secure+Containers
- **분류:** 대안

## 요약

RContainer는 ARM CCA(Confidential Compute Architecture)를 활용하여 기밀 컨테이너를 구현하는 시스템이다. ARM의 하드웨어 기반 기밀 컴퓨팅 기능을 컨테이너 격리에 적용하여, 클라우드 제공자로부터도 데이터를 보호하는 강력한 기밀성을 제공한다.

## 핵심 기여

- ARM CCA를 활용한 기밀 컨테이너 아키텍처 설계
- 하드웨어 기반 기밀 컴퓨팅의 컨테이너 적용
- 클라우드 제공자로부터의 데이터 보호
- ARM 플랫폼에서의 기밀 컨테이너 실현

## Firecracker와의 관계

Firecracker가 KVM 기반 VM 격리를 제공하는 반면, RContainer는 ARM CCA라는 하드웨어 기밀 컴퓨팅 기술을 활용한 대안적 격리 방식을 제시한다. ARM 기반 서버에서 Firecracker의 격리 모델을 대체하거나 보완할 수 있는 기밀 컨테이너 기술이다.

## 키워드

ARM CCA, confidential computing, secure containers, hardware-based isolation, data confidentiality
