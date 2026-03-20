# Kubernetes Scheduling: Taxonomy, Ongoing Issues and Challenges

- **참고문헌 번호:** 106
- **저자:** Carrion
- **학회/출처:** ACM Computing Surveys, 2022
- **논문 링크:** https://scholar.google.com/scholar?q=Kubernetes+Scheduling+Taxonomy+Ongoing+Issues+Challenges+Carrion
- **분류:** 보완

## 요약

96개의 문헌을 기반으로 Kubernetes 스케줄링 알고리즘의 분류 체계(taxonomy)를 제시한다. 현재 K8s 스케줄링의 주요 이슈와 도전 과제를 체계적으로 정리하며, 에너지 효율성, 지연 시간 최적화, 멀티 클러스터 스케줄링 등의 미해결 과제를 식별한다.

## 핵심 기여

- 96개 문헌 기반 K8s 스케줄링 분류 체계
- 스케줄링 전략의 체계적 분류 (휴리스틱, ML, 최적화 등)
- 미해결 과제 및 향후 연구 방향 제시
- K8s 스케줄링 연구의 포괄적 현황 분석

## Firecracker와의 관계

Kubernetes에서 Firecracker microVM을 오케스트레이션하는 경우(Kata Containers 등), 이 논문의 스케줄링 분류 체계는 microVM 배치 전략 선택에 참고가 된다. microVM 특유의 빠른 시작 시간을 활용한 스케줄링 최적화 방향을 탐색할 수 있다.

## 키워드

Kubernetes, 스케줄링, 분류 체계, 서베이, 에너지 효율, 멀티 클러스터, 최적화
