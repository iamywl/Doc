# ServerlessLLM: Locality-Enhanced Serverless Inference for Large Language Models

- **참고문헌 번호:** 77
- **저자:** Fu et al.
- **학회/출처:** OSDI 2024
- **논문 링크:** https://scholar.google.com/scholar?q=ServerlessLLM+Locality-Enhanced+Serverless+Inference+for+Large+Language+Models
- **분류:** 확장

## 요약

ServerlessLLM은 대규모 언어 모델(LLM) 서빙을 위한 지역성 강화 서버리스 추론 시스템이다. LLM의 거대한 모델 파라미터 로딩 문제를 지역성 기반 최적화로 해결하여 기존 대비 10-200배의 지연 시간 개선을 달성한다.

## 핵심 기여

- LLM 서빙에 특화된 서버리스 추론 아키텍처
- 10-200배 지연 시간 개선
- 모델 파라미터의 지역성 기반 로딩 최적화
- 대규모 모델의 서버리스 배포 가능성 실증

## Firecracker와의 관계

LLM 모델의 거대한 크기(수십~수백 GB)는 Firecracker microVM의 빠른 부팅과 경량 격리의 이점을 상쇄할 수 있다. ServerlessLLM의 지역성 최적화 기법은 Firecracker 기반 LLM 서빙에서 모델 로딩 병목을 해소하는 데 활용될 수 있다.

## 키워드

LLM serving, serverless inference, locality optimization, model loading, large language model
