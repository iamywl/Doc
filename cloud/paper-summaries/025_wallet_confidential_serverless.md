# Wallet: Confidential Serverless Computing

- **참고문헌 번호:** 25
- **저자:** Sabanic et al.
- **학회/출처:** NSDI, 2026
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Wallet+Confidential+Serverless+Computing)
- **분류:** 확장, 기밀 서버리스

## 요약

CVM(Confidential Virtual Machine) 내에 중첩된 기밀 실행 환경(trustlets)을 구현하여 서버리스 컴퓨팅에 기밀성을 부여하는 시스템이다. AMD SEV-SNP 및 Intel TDX를 활용하여 클라우드 제공자조차 사용자 데이터에 접근할 수 없는 서버리스 실행 환경을 제공한다. 경량 trustlet 단위로 함수를 격리하여 기밀 컴퓨팅의 오버헤드를 최소화한다.

## 핵심 기여

- CVM 내 중첩 기밀 실행을 위한 trustlet 아키텍처 설계
- AMD SEV-SNP와 Intel TDX를 활용한 하드웨어 기반 기밀성 보장
- 서버리스 워크로드에 최적화된 경량 기밀 실행 환경 구현
- 기존 서버리스 플랫폼 대비 합리적인 성능 오버헤드 달성

## Firecracker와의 관계

Firecracker의 microVM 격리 모델을 기밀 컴퓨팅으로 확장한 연구이다. Firecracker가 제공하는 격리에 하드웨어 수준의 메모리 암호화와 기밀성을 추가하여, 클라우드 제공자를 신뢰하지 않는 위협 모델에서도 서버리스 함수를 안전하게 실행할 수 있게 한다.

## 키워드

Confidential Computing, 서버리스, AMD SEV-SNP, Intel TDX, CVM, trustlet
