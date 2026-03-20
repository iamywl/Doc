# Photons: Lambdas on a Diet

- **참고문헌 번호:** 17
- **저자:** Dukic et al.
- **학회/출처:** SoCC, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Photons+Lambdas+on+a+Diet)
- **분류:** 유사

## 요약

Photons는 서버리스 함수 인스턴스를 공동 배치(co-locate)하여 메모리 사용량을 25~98% 절감하는 기법을 제안한다. 동일한 런타임 환경을 공유하는 함수들의 중복 메모리 페이지를 식별하고 공유함으로써 서버리스 플랫폼의 전체 메모리 효율성을 대폭 향상시킨다.

## 핵심 기여

- 함수 인스턴스 공동 배치를 통한 25~98% 메모리 절감
- 중복 메모리 페이지 공유 메커니즘
- 서버리스 플랫폼의 메모리 효율성 향상
- 격리를 유지하면서도 리소스 공유 달성

## Firecracker와의 관계

Firecracker의 MicroVM은 각 함수에 독립적인 VM을 할당하여 강력한 격리를 제공하지만, 메모리 오버헤드가 발생한다. Photons의 공동 배치 기법은 Firecracker 환경에서도 메모리 효율성을 개선하는 데 활용될 수 있는 보완적 연구이다.

## 키워드

Photons, Co-location, Memory Sharing, Memory Reduction, Serverless, Lambda
