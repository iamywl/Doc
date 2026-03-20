# Borg, Omega, and Kubernetes: Lessons Learned

- **참고문헌 번호:** 95
- **저자:** Burns et al.
- **학회/출처:** CACM, 2016
- **논문 링크:** https://scholar.google.com/scholar?q=Borg+Omega+Kubernetes+Lessons+Learned+Burns
- **분류:** 기반

## 요약

Google에서 10년 이상 컨테이너 관리 시스템을 운영하며 얻은 교훈을 정리한 논문이다. Borg에서 Omega를 거쳐 Kubernetes에 이르기까지의 진화 과정과 각 단계에서 학습한 설계 원칙을 공유한다. 컨테이너화, API 중심 설계, 선언적 구성 관리 등의 핵심 교훈을 제시한다.

## 핵심 기여

- 10년간의 컨테이너 관리 시스템 진화 과정 정리
- Borg → Omega → Kubernetes의 설계 결정 배경 설명
- 선언적 구성, API 중심, 라벨 기반 관리의 중요성 강조
- 대규모 시스템 운영에서의 실전 교훈 공유

## Firecracker와의 관계

Kubernetes는 Firecracker microVM을 오케스트레이션하는 주요 플랫폼 중 하나이다(Kata Containers 등을 통해). 이 논문의 교훈은 Firecracker 기반 워크로드를 효과적으로 관리하기 위한 오케스트레이션 설계 원칙을 이해하는 데 중요하다.

## 키워드

Borg, Omega, Kubernetes, 컨테이너 관리, 교훈, 선언적 구성, API 중심 설계
