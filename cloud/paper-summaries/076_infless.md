# INFless: A Native Serverless System for Low-Latency, High-Throughput Inference

- **참고문헌 번호:** 76
- **저자:** Yang et al.
- **학회/출처:** ASPLOS 2022
- **논문 링크:** https://scholar.google.com/scholar?q=INFless+A+Native+Serverless+System+for+Low-Latency+High-Throughput+Inference
- **분류:** 확장

## 요약

INFless는 ML 추론에 특화된 네이티브 서버리스 시스템으로, 내장 배칭(batching) 메커니즘을 통해 저지연 고처리량 추론을 달성한다. 범용 서버리스 플랫폼의 한계를 극복하고 ML 추론 워크로드의 특성에 맞춘 최적화를 수행한다.

## 핵심 기여

- ML 추론에 특화된 네이티브 서버리스 아키텍처
- 내장 배칭을 통한 처리량 극대화
- 저지연과 고처리량의 동시 달성
- ML 추론 워크로드 특성에 맞춘 자원 관리

## Firecracker와의 관계

Firecracker의 범용 microVM은 ML 추론의 배칭이나 GPU 활용에 최적화되어 있지 않다. INFless는 ML 추론에 특화된 서버리스 실행 환경을 제공하며, Firecracker 기반 플랫폼에 ML 추론 최적화 계층을 추가하는 방향으로 통합할 수 있다.

## 키워드

ML inference, serverless, batching, low latency, high throughput, native serverless
