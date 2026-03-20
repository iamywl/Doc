# Design Patterns for Container-based Distributed Systems

- **참고문헌 번호:** 116
- **저자:** Burns, Oppenheimer
- **학회/출처:** HotCloud, 2016
- **논문 링크:** https://scholar.google.com/scholar?q=Design+Patterns+Container+Distributed+Systems+Burns+Oppenheimer
- **분류:** 기반

## 요약

컨테이너 기반 분산 시스템의 설계 패턴을 제시한다. 사이드카(sidecar), 앰배서더(ambassador), 어댑터(adapter) 패턴 등 컨테이너 조합을 통한 시스템 구성 패턴을 정의한다. 객체지향 프로그래밍의 설계 패턴처럼, 컨테이너 기반 시스템의 모범 사례를 체계화한다.

## 핵심 기여

- 사이드카, 앰배서더, 어댑터 패턴 정의
- 단일 노드 및 다중 노드 패턴 분류
- 컨테이너 조합을 통한 시스템 구성 모범 사례
- 컨테이너 생태계의 설계 언어 확립

## Firecracker와의 관계

이러한 컨테이너 설계 패턴은 Firecracker microVM 기반 시스템에도 적용 가능하다. 특히 사이드카 패턴은 microVM에 보안 에이전트나 모니터링 도구를 배치하는 데 활용될 수 있다.

## 키워드

설계 패턴, 사이드카, 앰배서더, 어댑터, 컨테이너, 분산 시스템, 모범 사례
