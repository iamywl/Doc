# Cloudburst: Stateful Functions-as-a-Service

- **참고문헌 번호:** 71
- **저자:** Sreekanti et al.
- **학회/출처:** VLDB 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Cloudburst+Stateful+Functions-as-a-Service
- **분류:** 확장

## 요약

Cloudburst는 Anna KVS(Key-Value Store)를 기반으로 상태 유지 FaaS를 구현하는 시스템이다. 함수 실행 노드에 캐시를 공동 배치(co-location)하여 상태 접근 지연 시간을 최소화하며, 무상태 FaaS의 한계를 극복하면서도 탄력적 확장성을 유지한다.

## 핵심 기여

- Anna KVS 기반 상태 유지 FaaS 아키텍처 설계
- 캐시 공동 배치를 통한 상태 접근 지연 시간 최소화
- 탄력적 확장성과 상태 유지의 동시 달성
- 다양한 일관성 수준 지원

## Firecracker와의 관계

Firecracker microVM의 무상태 특성을 보완하기 위해 Cloudburst의 캐시 공동 배치 전략을 활용할 수 있다. 각 microVM 옆에 로컬 KVS 캐시를 배치하여 상태 접근 성능을 높이면서도 Firecracker의 격리 보장을 유지할 수 있다.

## 키워드

stateful FaaS, Anna KVS, cache co-location, elastic scaling, key-value store
