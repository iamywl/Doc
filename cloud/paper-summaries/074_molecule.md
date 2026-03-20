# Molecule: Serverless Computing on Heterogeneous Computers

- **참고문헌 번호:** 74
- **저자:** Du et al.
- **학회/출처:** ASPLOS 2022
- **논문 링크:** https://scholar.google.com/scholar?q=Molecule+Serverless+Computing+on+Heterogeneous+Computers
- **분류:** 확장

## 요약

Molecule은 GPU, FPGA 등 이기종 가속기를 서버리스 환경에서 활용하기 위한 시스템이다. XPU-Shim과 Vectorized Sandbox를 통해 다양한 가속기에 대한 통합 추상화를 제공하며, 50% 높은 함수 밀도와 34.6배의 성능 향상을 달성한다.

## 핵심 기여

- XPU-Shim을 통한 이기종 가속기 통합 추상화
- Vectorized Sandbox를 통한 가속기 샌드박싱
- 50% 높은 함수 밀도 달성
- 34.6배 성능 향상

## Firecracker와의 관계

Firecracker는 CPU 기반 워크로드에 최적화되어 있으며 GPU/FPGA 등의 이기종 가속기 지원이 제한적이다. Molecule의 XPU-Shim 접근법은 Firecracker microVM에서 이기종 가속기에 접근하는 방법을 제시하며, 서버리스 환경에서 가속기 활용의 가능성을 확장한다.

## 키워드

heterogeneous computing, GPU, FPGA, XPU-Shim, vectorized sandbox, accelerator
