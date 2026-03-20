# Fifer: Tackling Resource Underutilization in the Serverless Era

- **참고문헌 번호:** 58
- **저자:** Gunasekaran et al.
- **학회/출처:** Middleware 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Fifer+Tackling+Resource+Underutilization+in+the+Serverless+Era
- **분류:** 확장

## 요약

Fifer는 서버리스 환경에서의 자원 과소 활용 문제를 해결하기 위해 함수 인식(function-aware) 컨테이너 스케일링과 빈 패킹(bin packing) 기법을 제안한다. 이를 통해 기존 대비 4배의 자원 활용률 향상을 달성하며, 서버리스 플랫폼의 비용 효율성을 크게 개선한다.

## 핵심 기여

- 서버리스 환경의 자원 과소 활용 문제에 대한 체계적 분석
- 함수 특성을 고려한 컨테이너 스케일링 알고리즘 설계
- 빈 패킹 기반 자원 통합으로 4배 활용률 향상
- 서버리스 자원 관리를 위한 실용적 프레임워크 제시

## Firecracker와의 관계

Firecracker microVM 환경에서도 유휴 VM 인스턴스에 의한 자원 낭비가 발생할 수 있으며, Fifer의 함수 인식 스케일링 및 빈 패킹 기법을 적용하여 호스트당 microVM 밀도를 높이고 자원 활용률을 개선할 수 있다.

## 키워드

resource utilization, bin packing, container scaling, function-aware, serverless efficiency
