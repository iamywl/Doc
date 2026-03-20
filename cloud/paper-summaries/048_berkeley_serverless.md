# Berkeley View on Serverless Computing

- **참고문헌 번호:** 48
- **저자:** Jonas et al.
- **학회/출처:** UCB Tech Report, 2019
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Cloud+Programming+Simplified+A+Berkeley+View+on+Serverless+Computing)
- **분류:** 비판, 서버리스 한계

## 요약

서버리스 컴퓨팅의 현재 상태와 구조적 한계를 포괄적으로 분석한 UC Berkeley의 기술 보고서이다. 서버리스의 장점을 인정하면서도, 상태 관리의 어려움, 함수 간 통신 비효율, 하드웨어 이기종성 활용 제한, 표준화 부재 등 근본적인 한계점을 체계적으로 지적한다.

## 핵심 기여

- 서버리스 컴퓨팅의 현황과 미래에 대한 포괄적 분석
- 상태 관리, 통신, 표준화 등 구조적 한계의 체계적 식별
- 서버리스가 부적합한 워크로드 유형 분류
- 서버리스 컴퓨팅의 발전을 위한 연구 과제 및 로드맵 제시

## Firecracker와의 관계

Firecracker가 AWS Lambda의 기반으로 서버리스 컴퓨팅을 구현하는 핵심 기술이므로, 서버리스의 구조적 한계는 Firecracker의 활용 범위에도 직접적 영향을 미친다. 이 논문이 지적하는 한계들은 Firecracker 기반 플랫폼이 해결해야 할 과제이기도 하다.

## 키워드

서버리스, 클라우드 컴퓨팅, 구조적 한계, 상태 관리, Berkeley
