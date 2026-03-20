# Lightweight Sidecar for Serverless Service Mesh

- **참고문헌 번호:** 128
- **저자:** (Not specified)
- **학회/출처:** SoCC, 2025
- **논문 링크:** https://scholar.google.com/scholar?q=Lightweight+Sidecar+Serverless+Service+Mesh
- **분류:** 보완

## 요약

서버리스 환경에 최적화된 경량 서비스 메시 프록시를 제안한다. 기존 Envoy 등의 사이드카 프록시가 서버리스 함수의 빠른 시작 시간과 짧은 실행 시간에 비해 과도한 오버헤드를 발생시키는 문제를 해결한다. 경량화된 사이드카를 통해 서버리스 환경에서도 서비스 메시의 이점을 유지한다.

## 핵심 기여

- 서버리스 환경용 경량 서비스 메시 프록시 설계
- 기존 사이드카 대비 오버헤드 감소
- 서버리스 함수의 빠른 시작 시간과 호환
- 서비스 메시 기능(라우팅, 모니터링, 보안) 유지

## Firecracker와의 관계

Firecracker microVM 위에서 실행되는 서버리스 함수에 서비스 메시를 적용할 때, 경량 사이드카는 microVM의 빠른 부팅 시간을 저해하지 않으면서 네트워크 정책을 적용할 수 있게 한다.

## 키워드

사이드카, 서비스 메시, 서버리스, 경량 프록시, Envoy, 네트워크 정책, 오버헤드
