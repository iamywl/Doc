# Twine: A Unified Cluster Management System for Shared Infrastructure

- **참고문헌 번호:** 100
- **저자:** Tang et al.
- **학회/출처:** OSDI, 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Twine+Unified+Cluster+Management+System+Shared+Infrastructure
- **분류:** 기반

## 요약

Meta(Facebook)의 클러스터 관리 시스템 Twine을 소개한다. 리전당 100만 대 이상의 서버를 관리하며, 공유 인프라에서 다양한 워크로드(웹 서비스, ML 학습, 스토리지 등)를 통합적으로 운영한다. 유연한 자원 추상화와 효율적인 스케줄링을 통해 대규모 공유 인프라의 활용률을 극대화한다.

## 핵심 기여

- 리전당 100만 대 이상 서버 관리 규모 달성
- 다양한 워크로드의 통합 관리 메커니즘
- 유연한 자원 추상화 계층 설계
- 공유 인프라에서의 효율적 자원 활용 전략

## Firecracker와의 관계

Twine과 같은 대규모 클러스터 관리 시스템은 Firecracker microVM을 생산 환경에서 대규모로 배포하고 관리하는 상위 레벨 시스템이다. microVM의 경량성은 이러한 대규모 클러스터에서의 자원 효율성을 높이는 데 직접적으로 기여한다.

## 키워드

Twine, Meta, 클러스터 관리, 공유 인프라, 대규모 시스템, 자원 추상화, 통합 관리
