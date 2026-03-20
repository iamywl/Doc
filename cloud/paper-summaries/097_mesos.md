# Mesos: A Platform for Fine-Grained Resource Sharing in the Data Center

- **참고문헌 번호:** 97
- **저자:** Hindman et al.
- **학회/출처:** NSDI, 2011
- **논문 링크:** https://scholar.google.com/scholar?q=Mesos+Platform+Fine-Grained+Resource+Sharing+Hindman
- **분류:** 기반

## 요약

Mesos는 데이터센터에서 세밀한 자원 공유를 가능하게 하는 플랫폼이다. 리소스 오퍼(resource offers) 기반의 2-레벨 스케줄링을 통해 다양한 프레임워크(Hadoop, MPI 등)가 클러스터 자원을 효율적으로 공유할 수 있게 한다. 5만 노드 규모까지 확장 가능함을 보여준다.

## 핵심 기여

- 리소스 오퍼 기반 2-레벨 스케줄링 아키텍처
- 다양한 프레임워크 간 자원 공유 메커니즘
- 50,000 노드 규모의 확장성 검증
- 최소한의 인터페이스로 유연한 자원 관리 실현

## Firecracker와의 관계

Mesos의 2-레벨 스케줄링 모델은 Firecracker microVM을 다양한 워크로드 프레임워크에서 공유하는 환경에 적용 가능하다. 리소스 오퍼 메커니즘은 microVM의 동적 할당과 해제에 효율적인 추상화를 제공한다.

## 키워드

Mesos, 리소스 오퍼, 2-레벨 스케줄링, 자원 공유, 분산 시스템, 확장성, 데이터센터
