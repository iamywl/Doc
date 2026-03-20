# Guarding Serverless Applications with Kalium

- **참고문헌 번호:** 72
- **저자:** Jegan et al.
- **학회/출처:** USENIX Security 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Guarding+Serverless+Applications+with+Kalium
- **분류:** 보완

## 요약

Kalium은 서버리스 애플리케이션에 제어 흐름 무결성(CFI)을 적용하는 보안 시스템이다. NFA(비결정적 유한 오토마타) 기반 흐름 그래프를 사용하여 허용된 함수 호출 순서만 실행되도록 보장하며, 약 2.4ms의 낮은 오버헤드로 동작한다.

## 핵심 기여

- 서버리스 환경에 특화된 CFI(Control Flow Integrity) 메커니즘 설계
- NFA 기반 함수 호출 흐름 그래프를 통한 보안 정책 적용
- 약 2.4ms의 낮은 성능 오버헤드
- 서버리스 워크플로의 무결성 보장

## Firecracker와의 관계

Firecracker가 VM 수준의 격리를 제공하는 반면, Kalium은 서버리스 애플리케이션의 제어 흐름 수준에서 보안을 보완한다. Firecracker의 격리와 Kalium의 CFI를 결합하면, VM 격리와 함수 흐름 무결성이라는 다층 보안 체계를 구축할 수 있다.

## 키워드

control flow integrity, serverless security, NFA, flow graph, low overhead
