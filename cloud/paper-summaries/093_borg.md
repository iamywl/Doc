# Large-scale Cluster Management at Google with Borg

- **참고문헌 번호:** 93
- **저자:** Verma et al.
- **학회/출처:** EuroSys, 2015
- **논문 링크:** https://scholar.google.com/scholar?q=Large-scale+Cluster+Management+Google+Borg+Verma
- **분류:** 기반

## 요약

Google의 Borg 클러스터 관리 시스템을 소개하는 논문이다. 수만 대의 서버에서 수십만 개의 작업을 관리하며, 태스크 패킹(task packing)과 오버커밋(overcommit) 전략을 통해 자원 활용률을 극대화한다. Borg는 높은 가용성, 효율적인 자원 활용, 다양한 워크로드 지원을 동시에 달성한다.

## 핵심 기여

- Google 규모의 클러스터 관리 시스템 상세 공개
- 태스크 패킹과 자원 오버커밋을 통한 효율성 극대화
- 우선순위 기반 스케줄링과 선점(preemption) 메커니즘
- 높은 가용성을 위한 장애 도메인 분산 배치

## Firecracker와의 관계

Borg는 Firecracker가 사용되는 클라우드 인프라의 상위 레벨 오케스트레이션 시스템이다. Firecracker의 경량 microVM은 Borg와 같은 클러스터 관리자의 태스크 패킹 효율을 높이는 데 기여하며, 빠른 부팅 시간은 스케줄링 지연을 줄인다.

## 키워드

Borg, Google, 클러스터 관리, 태스크 패킹, 오버커밋, 스케줄링, 대규모 시스템
