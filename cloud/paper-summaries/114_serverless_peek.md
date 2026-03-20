# Peeking Behind the Curtains of Serverless Platforms

- **참고문헌 번호:** 114
- **저자:** Wang et al.
- **학회/출처:** ATC, 2018
- **논문 링크:** https://scholar.google.com/scholar?q=Peeking+Behind+Curtains+Serverless+Platforms+Wang
- **분류:** 기반

## 요약

AWS Lambda를 중심으로 서버리스 플랫폼의 내부 동작을 5만 개 이상의 Lambda 인스턴스를 생성하여 측정한 연구이다. 인스턴스 라이프사이클, 자원 할당, 격리 메커니즘, 콜드 스타트 특성 등을 블랙박스 테스트를 통해 분석한다. 서버리스 플랫폼의 구현 세부사항에 대한 중요한 통찰을 제공한다.

## 핵심 기여

- 50,000+ Lambda 인스턴스 기반 대규모 측정 연구
- 서버리스 인스턴스 라이프사이클 특성 분석
- 콜드 스타트 패턴 및 인스턴스 재사용 정책 발견
- 격리 메커니즘 및 자원 할당 전략 추론

## Firecracker와의 관계

이 논문은 Firecracker가 AWS Lambda에 도입되기 전의 서버리스 플랫폼을 분석한 것으로, Firecracker 도입의 배경과 동기를 이해하는 데 중요하다. 발견된 격리 및 성능 과제들이 Firecracker 설계에 반영되었다.

## 키워드

서버리스, AWS Lambda, 측정 연구, 콜드 스타트, 인스턴스 라이프사이클, 격리, 블랙박스
