# SAND: Towards High-Performance Serverless Computing

- **참고문헌 번호:** 39
- **저자:** Akkus et al.
- **학회/출처:** ATC, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=SAND+Towards+High-Performance+Serverless+Computing)
- **분류:** 반대, 프로세스 수준 격리

## 요약

애플리케이션 수준의 샌드박싱과 프로세스 격리를 활용하여 고성능 서버리스 컴퓨팅을 구현한 시스템이다. VM 기반 격리 대신 프로세스 수준 격리를 사용하여 함수 간 통신 오버헤드를 줄이고, 같은 애플리케이션 내 함수들의 상태 공유를 효율적으로 지원한다.

## 핵심 기여

- 프로세스 수준 격리 기반의 경량 서버리스 실행 환경 설계
- 애플리케이션 내 함수 간 효율적 데이터 공유 메커니즘 구현
- VM 격리 대비 낮은 오버헤드와 빠른 함수 호출 달성
- 함수 체이닝 및 파이프라인 실행의 최적화

## Firecracker와의 관계

Firecracker의 VM 기반 강격리와 반대되는 프로세스 수준 경량 격리 접근을 채택한다. SAND는 보안보다 성능을 우선시하여 동일 프로세스 내 함수 실행을 허용하며, 이는 Firecracker가 추구하는 강력한 격리 모델과 대비되는 설계 철학을 보여준다.

## 키워드

서버리스, 프로세스 격리, 샌드박싱, 함수 체이닝, 고성능
