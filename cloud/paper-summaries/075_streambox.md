# StreamBox: A Lightweight GPU Sandbox for Serverless Inference

- **참고문헌 번호:** 75
- **저자:** Wu et al.
- **학회/출처:** ATC 2024
- **논문 링크:** https://scholar.google.com/scholar?q=StreamBox+A+Lightweight+GPU+Sandbox+for+Serverless+Inference
- **분류:** 확장

## 요약

StreamBox는 CUDA 스트림을 GPU 샌드박스로 활용하여 서버리스 추론 워크로드의 GPU 공유를 효율화하는 시스템이다. 82%의 메모리 감소와 98%의 지연 시간 감소를 달성하며, 여러 서버리스 함수가 하나의 GPU를 안전하게 공유할 수 있게 한다.

## 핵심 기여

- CUDA 스트림 기반 경량 GPU 샌드박스 메커니즘
- 82% GPU 메모리 사용량 감소
- 98% 지연 시간 감소
- 다중 서버리스 함수 간 GPU 안전 공유

## Firecracker와의 관계

Firecracker microVM에서 GPU를 사용하는 것은 기술적으로 어려우며, StreamBox는 GPU 공유를 위한 경량 샌드박스라는 보완적 격리 메커니즘을 제시한다. Firecracker의 CPU 격리와 StreamBox의 GPU 샌드박스를 결합하면 이기종 자원에 대한 종합적인 격리를 제공할 수 있다.

## 키워드

GPU sandbox, CUDA streams, serverless inference, GPU sharing, memory reduction
