# Faastlane: Accelerating Function-as-a-Service Workflows

- **참고문헌 번호:** 62
- **저자:** Kotni et al.
- **학회/출처:** ATC 2021
- **논문 링크:** https://scholar.google.com/scholar?q=Faastlane+Accelerating+Function-as-a-Service+Workflows
- **분류:** 반대

## 요약

Faastlane은 워크플로 내 함수들을 개별 컨테이너나 VM이 아닌 동일 프로세스 내 스레드로 실행하여 15배의 속도 향상을 달성하는 시스템이다. 함수 간 통신 오버헤드를 제거하고 메모리 공유를 통해 데이터 전달을 가속화하는 급진적 접근법을 제시한다.

## 핵심 기여

- 동일 프로세스 내 스레드 기반 함수 실행으로 15배 속도 향상
- 함수 간 직접 메모리 공유를 통한 데이터 전달 가속화
- 워크플로 함수 간 통신 오버헤드 제거
- 격리와 성능 간 트레이드오프에 대한 새로운 관점 제시

## Firecracker와의 관계

Firecracker가 각 함수를 개별 microVM에서 격리 실행하는 것과 정반대의 접근법이다. Faastlane은 격리 수준을 낮추는 대신 성능을 극대화하며, 이는 Firecracker의 강력한 격리 모델과 근본적으로 상충한다. 신뢰할 수 있는 워크플로에서는 Faastlane 방식이 유리할 수 있다.

## 키워드

in-process execution, workflow acceleration, thread-based isolation, zero-copy, function chaining
