# YARN: Yet Another Resource Negotiator

- **참고문헌 번호:** 99
- **저자:** Vavilapalli et al.
- **학회/출처:** SoCC, 2013
- **논문 링크:** https://scholar.google.com/scholar?q=YARN+Yet+Another+Resource+Negotiator+Vavilapalli
- **분류:** 기반

## 요약

YARN은 Hadoop의 차세대 자원 관리 시스템으로, 기존 MapReduce의 자원 관리와 작업 스케줄링을 분리(separation of concerns)한다. ResourceManager와 ApplicationMaster의 분리를 통해 MapReduce 이외의 다양한 프로그래밍 모델을 지원하며, 클러스터 자원 관리의 유연성을 크게 향상시킨다.

## 핵심 기여

- 자원 관리와 작업 스케줄링의 관심사 분리
- ResourceManager/ApplicationMaster 아키텍처 설계
- MapReduce를 넘어선 다양한 프로그래밍 모델 지원
- Hadoop 생태계의 범용 자원 관리 플랫폼으로의 진화

## Firecracker와의 관계

YARN의 관심사 분리 원칙은 Firecracker 기반 서버리스 플랫폼의 아키텍처 설계에 참고가 된다. 자원 관리(microVM 할당)와 워크로드 스케줄링(함수 실행)의 분리는 시스템 유연성을 높이는 핵심 설계 원칙이다.

## 키워드

YARN, Hadoop, 자원 관리, 관심사 분리, ResourceManager, ApplicationMaster, 스케줄링
