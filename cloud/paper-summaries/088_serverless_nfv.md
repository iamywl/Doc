# Serverless for Network Function Virtualization

- **참고문헌 번호:** 88
- **저자:** Aditya et al.
- **학회/출처:** Proc. IEEE, 2019
- **논문 링크:** https://scholar.google.com/scholar?q=Serverless+Network+Function+Virtualization+Aditya
- **분류:** 확장

## 요약

서버리스 컴퓨팅 모델을 네트워크 기능 가상화(NFV)에 적용하는 방안을 제시한다. 기존 NFV의 정적 자원 할당 문제를 서버리스의 이벤트 기반, 자동 스케일링 모델로 해결하고자 한다. 네트워크 함수를 서버리스 함수로 배포하여 탄력적이고 비용 효율적인 네트워크 서비스 체인을 구현한다.

## 핵심 기여

- 서버리스 모델의 NFV 적용 비전 및 아키텍처 제시
- NFV 워크로드의 서버리스 함수화 가능성 분석
- 상태 관리, 지연 시간 등 핵심 과제 식별
- 이벤트 기반 네트워크 함수 실행 모델 설계

## Firecracker와의 관계

Firecracker의 빠른 부팅 시간과 경량 격리는 서버리스 NFV의 핵심 요구사항과 일치한다. 네트워크 함수를 microVM에서 실행하면 강력한 격리와 빠른 스케일링을 동시에 달성할 수 있어, 서버리스 NFV 플랫폼의 실행 엔진으로 적합하다.

## 키워드

서버리스, NFV, 네트워크 기능 가상화, 이벤트 기반, 자동 스케일링, 서비스 체인
