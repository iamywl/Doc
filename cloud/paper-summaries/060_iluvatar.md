# Iluvatar: A Fast Control Plane for Serverless Computing

- **참고문헌 번호:** 60
- **저자:** Fuerst et al.
- **학회/출처:** HPDC 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Iluvatar+A+Fast+Control+Plane+for+Serverless+Computing
- **분류:** 확장

## 요약

Iluvatar는 Rust로 구현된 고성능 FaaS 제어 플레인으로, 3ms의 지연 시간을 달성하며 이는 OpenWhisk 대비 100배 낮은 수치이다. 서버리스 제어 플레인의 성능 병목을 체계적으로 분석하고, 시스템 프로그래밍 언어를 활용한 효율적 구현으로 이를 해결한다.

## 핵심 기여

- Rust 기반 고성능 FaaS 제어 플레인 구현
- 3ms 지연 시간 달성 (OpenWhisk 대비 100배 개선)
- 서버리스 제어 플레인의 성능 병목 요인 체계적 분석
- 저수준 시스템 최적화를 통한 오버헤드 최소화

## Firecracker와의 관계

Firecracker 역시 Rust로 구현되어 안전성과 성능을 동시에 달성한 프로젝트이다. Iluvatar의 빠른 제어 플레인과 Firecracker의 빠른 microVM 부팅을 결합하면, 제어 플레인부터 데이터 플레인까지 전 구간에서 최적화된 서버리스 스택을 구성할 수 있다.

## 키워드

control plane, Rust, low latency, FaaS, OpenWhisk, performance optimization
