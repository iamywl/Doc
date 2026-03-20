# Acto: Automatic End-to-End Testing for Operation Correctness of Cloud System

- **참고문헌 번호:** 108
- **저자:** (Not specified)
- **학회/출처:** SOSP, 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Acto+Automatic+End-to-End+Testing+Operation+Correctness+Cloud
- **분류:** 보완

## 요약

Acto는 Kubernetes 오퍼레이터의 운영 정확성을 자동으로 테스트하는 엔드투엔드 테스트 프레임워크이다. 커스텀 리소스 정의(CRD)의 스키마를 분석하여 테스트 입력을 자동 생성하고, 상태 변이를 추적하여 오퍼레이터의 정확성을 검증한다. 실제 K8s 오퍼레이터에서 다수의 버그를 발견했다.

## 핵심 기여

- K8s 오퍼레이터 자동 테스트 프레임워크 설계
- CRD 스키마 기반 테스트 입력 자동 생성
- 상태 변이 추적을 통한 정확성 검증
- 실제 오퍼레이터에서 다수의 신규 버그 발견

## Firecracker와의 관계

Firecracker를 K8s에서 관리하는 오퍼레이터(예: Kata Containers 오퍼레이터)의 정확성 검증에 Acto를 활용할 수 있다. microVM 라이프사이클을 관리하는 오퍼레이터의 안정성 확보는 생산 환경에서 중요하다.

## 키워드

Acto, K8s 오퍼레이터, 자동 테스트, CRD, 정확성 검증, 상태 변이, 버그 발견
