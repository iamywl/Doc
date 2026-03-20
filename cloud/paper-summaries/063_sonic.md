# SONIC: Application-aware Data Passing for Chained Serverless Applications

- **참고문헌 번호:** 63
- **저자:** Mahgoub et al.
- **학회/출처:** ATC 2021
- **논문 링크:** https://scholar.google.com/scholar?q=SONIC+Application-aware+Data+Passing+for+Chained+Serverless+Applications
- **분류:** 확장

## 요약

SONIC은 체인된 서버리스 애플리케이션에서 데이터 전달 방식(직접 전달 vs 스토리지 경유)을 자동으로 최적화하는 시스템이다. 애플리케이션의 데이터 흐름 패턴을 분석하여 각 단계에 가장 적합한 데이터 전달 메커니즘을 자동으로 선택한다.

## 핵심 기여

- 서버리스 함수 체인에서의 데이터 전달 방식 자동 최적화
- 직접 전달(direct passing)과 스토리지 경유(storage-based) 방식 간 자동 선택
- 애플리케이션 특성 인식 기반 의사결정 프레임워크
- 다양한 데이터 전달 패턴에 대한 체계적 분석

## Firecracker와의 관계

Firecracker microVM 간 데이터 전달은 네트워크 또는 공유 스토리지를 통해 이루어지며, SONIC의 최적화 기법을 적용하면 microVM 간 데이터 흐름의 효율성을 높일 수 있다. 특히 서버리스 워크플로에서 각 Firecracker 인스턴스 간 최적 데이터 경로를 자동으로 결정하는 데 활용할 수 있다.

## 키워드

data passing, function chaining, direct transfer, storage-based, application-aware optimization
