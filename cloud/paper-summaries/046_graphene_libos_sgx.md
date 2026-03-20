# Graphene: A Library OS / Graphene-SGX

- **참고문헌 번호:** 46
- **저자:** Tsai et al.
- **학회/출처:** EuroSys 2014 / ATC 2017
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Graphene+Library+OS+SGX)
- **분류:** 대안, Library OS + SGX

## 요약

리눅스 호환 Library OS인 Graphene과 그 SGX 확장판을 제시한 논문이다. Graphene은 호스트 OS와의 인터페이스를 최소 PAL(Platform Adaptation Layer)로 제한하여 이식성과 격리를 제공하고, Graphene-SGX는 이를 Intel SGX 엔클레이브 내에서 실행하여 기밀 실행 환경을 구현한다.

## 핵심 기여

- 리눅스 호환 Library OS로 기존 애플리케이션의 투명한 격리 실행
- 최소 PAL 인터페이스를 통한 호스트 OS 공격 표면 축소
- SGX 엔클레이브 내 Library OS 실행으로 기밀 컴퓨팅 지원
- 멀티프로세스 지원 및 프로세스 간 통신 메커니즘 구현

## Firecracker와의 관계

Firecracker가 하드웨어 가상화 기반 격리를 사용하는 것과 달리, Graphene은 Library OS 기반의 대안적 격리를 제공한다. 특히 Graphene-SGX는 TEE 기반 격리를 추가하여, Firecracker + 기밀 컴퓨팅의 결합과 유사한 보안 수준을 다른 아키텍처로 달성한다.

## 키워드

Library OS, Intel SGX, Graphene, PAL, 기밀 컴퓨팅, 격리
