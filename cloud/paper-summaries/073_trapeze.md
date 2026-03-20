# Secure Serverless Computing Using Dynamic Information Flow Control (Trapeze)

- **참고문헌 번호:** 73
- **저자:** Alpernas et al.
- **학회/출처:** OOPSLA 2018
- **논문 링크:** https://scholar.google.com/scholar?q=Secure+Serverless+Computing+Using+Dynamic+Information+Flow+Control+Trapeze
- **분류:** 보완

## 요약

Trapeze는 동적 정보 흐름 제어(Dynamic IFC)를 서버리스 환경에 적용하여 데이터 기밀성을 보장하는 시스템이다. 각 함수와 데이터에 보안 레이블을 동적으로 부여하고, 정보 흐름 정책에 따라 데이터 접근을 제어하여 민감한 데이터의 유출을 방지한다.

## 핵심 기여

- 서버리스 환경을 위한 동적 IFC 메커니즘 설계
- 데이터 기밀성을 위한 보안 레이블 시스템
- 함수 간 정보 흐름의 동적 추적 및 제어
- 서버리스 특성에 맞는 세밀한 데이터 접근 제어

## Firecracker와의 관계

Firecracker의 VM 격리가 실행 환경 수준의 보안을 제공하는 반면, Trapeze는 데이터 수준의 기밀성 보호를 제공한다. 두 기술을 결합하면 Firecracker의 격리 경계 내에서도 데이터 흐름을 세밀하게 제어하여 보다 강력한 보안 체계를 구축할 수 있다.

## 키워드

dynamic information flow control, data confidentiality, security labels, serverless security, data protection
