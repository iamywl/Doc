# Palette Load Balancing: Locality Hints for Serverless Functions

- **참고문헌 번호:** 59
- **저자:** Abdi et al.
- **학회/출처:** EuroSys 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Palette+Load+Balancing+Locality+Hints+for+Serverless+Functions
- **분류:** 확장

## 요약

Palette는 서버리스 함수에 "색상(color)" 기반 지역성 힌트를 부여하여 데이터 친화성을 고려한 로드 밸런싱을 수행하는 시스템이다. 동일 데이터에 접근하는 함수들을 같은 노드에 배치함으로써 데이터 이동 오버헤드를 줄이고 캐시 효율성을 높인다.

## 핵심 기여

- "색상(color)" 메타포를 활용한 직관적 지역성 힌트 메커니즘 설계
- FaaS 환경에서의 데이터 친화성 기반 함수 배치 전략
- 로드 밸런싱과 지역성 간의 균형 달성
- 기존 로드 밸런서에 통합 가능한 실용적 접근법

## Firecracker와의 관계

Firecracker microVM의 배치 시 데이터 지역성을 고려하면 캐시 히트율을 높이고 네트워크 데이터 전송을 줄일 수 있다. Palette의 지역성 힌트 메커니즘을 Firecracker 기반 서버리스 플랫폼의 스케줄러에 통합하면 전체 성능을 향상시킬 수 있다.

## 키워드

locality hints, load balancing, data affinity, function placement, color-based scheduling
