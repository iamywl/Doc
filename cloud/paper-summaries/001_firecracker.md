# Firecracker: Lightweight Virtualization for Serverless Applications

- **참고문헌 번호:** 1
- **저자:** Agache et al.
- **학회/출처:** NSDI, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Firecracker+Lightweight+Virtualization+for+Serverless+Applications)
- **분류:** 기준 논문

## 요약

Firecracker는 AWS Lambda와 Fargate를 위해 개발된 경량 VMM(Virtual Machine Monitor)으로, KVM 위에서 Rust로 구현되었다. MicroVM당 5MB 미만의 메모리를 사용하며 125ms 이내에 부팅이 가능하여, 서버리스 워크로드에 필요한 강력한 격리와 빠른 시작 시간을 동시에 달성한다.

## 핵심 기여

- KVM 기반의 최소한의 디바이스 모델을 갖춘 경량 VMM 설계
- MicroVM당 5MB 미만의 메모리 오버헤드 달성
- 125ms 이내의 빠른 부팅 시간
- Rust 언어를 활용한 메모리 안전성 확보
- AWS Lambda 프로덕션 환경에서의 대규모 운영 검증

## Firecracker와의 관계

본 논문이 Firecracker의 기준 논문이다. Firecracker의 설계 철학, 아키텍처, 성능 특성을 정의하며, 이후 모든 관련 연구의 비교 기준점이 된다.

## 키워드

Firecracker, MicroVM, KVM, Rust, Serverless, Lightweight Virtualization, AWS Lambda
