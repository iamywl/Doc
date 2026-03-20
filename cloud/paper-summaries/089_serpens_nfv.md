# Serpens: A Serverless Platform for NFV

- **참고문헌 번호:** 89
- **저자:** Shen et al.
- **학회/출처:** IWQoS, 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Serpens+NFV+Platform+Serverless+Shen
- **분류:** 확장

## 요약

Serpens는 서버리스 아키텍처를 기반으로 한 NFV 플랫폼 구현체이다. 네트워크 기능을 서버리스 함수로 패키징하고, 자동 스케일링과 서비스 체이닝을 지원한다. 실제 구현을 통해 서버리스 NFV의 실현 가능성을 검증하고, 기존 NFV 플랫폼 대비 자원 효율성과 운영 편의성을 개선한다.

## 핵심 기여

- 서버리스 NFV 플랫폼(Serpens)의 실제 구현
- 네트워크 함수의 서버리스 함수 변환 메커니즘
- 자동 스케일링 및 서비스 체이닝 지원
- 기존 NFV 플랫폼 대비 성능 및 효율성 비교 평가

## Firecracker와의 관계

Serpens와 같은 서버리스 NFV 플랫폼에서 Firecracker microVM을 실행 환경으로 사용하면, 네트워크 함수 간 강력한 격리를 보장하면서도 빠른 인스턴스 생성이 가능하다. 특히 멀티테넌트 NFV 환경에서의 보안 요구사항을 충족할 수 있다.

## 키워드

Serpens, 서버리스 NFV, 네트워크 기능 가상화, 서비스 체이닝, 자동 스케일링, 플랫폼
