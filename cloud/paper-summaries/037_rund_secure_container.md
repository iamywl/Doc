# RunD: Lightweight Secure Container Runtime

- **참고문헌 번호:** 37
- **저자:** Li et al.
- **학회/출처:** ATC, 2022
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=RunD+Lightweight+Secure+Container+Runtime)
- **분류:** 반대, 초경량 보안 컨테이너

## 요약

알리바바가 개발한 초경량 보안 컨테이너 런타임으로, 게스트 커널을 여러 컨테이너 간 공유하여 Kata Containers 대비 4배 빠른 시작 시간을 달성한다. 각 컨테이너마다 별도의 게스트 OS를 부팅하는 기존 방식과 달리, 공유 게스트 커널 위에서 경량 격리를 수행하여 자원 효율성을 극대화한다.

## 핵심 기여

- 게스트 커널 공유를 통한 Kata Containers 대비 4배 빠른 시작 달성
- 보안 격리와 성능 효율성 간의 새로운 트레이드오프 포인트 제시
- 대규모 클라우드 환경(알리바바)에서의 프로덕션 검증
- 최소화된 게스트 OS 이미지를 통한 메모리 오버헤드 감소

## Firecracker와의 관계

Firecracker가 각 microVM마다 독립적인 게스트 커널을 실행하는 것과 반대로, RunD는 게스트 커널을 공유하여 더 빠른 시작 시간과 낮은 오버헤드를 달성한다. 이는 Firecracker의 강한 격리 모델에 대한 대안적 설계로, 격리 강도와 성능 간의 트레이드오프를 보여준다.

## 키워드

보안 컨테이너, 게스트 커널 공유, Kata Containers, 경량 런타임, 알리바바
