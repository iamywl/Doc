# CNTR: Lightweight OS Containers

- **참고문헌 번호:** 33
- **저자:** Thalheim et al.
- **학회/출처:** ATC, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=CNTR+Lightweight+OS+Containers)
- **분류:** 보완, 컨테이너 축소

## 요약

컨테이너 이미지를 slim(최소 실행 이미지)과 fat(디버깅/개발 도구 포함) 이미지로 분리하여 컨테이너 크기를 66.6% 줄이는 기법을 제안한 논문이다. 운영 시에는 slim 이미지를 사용하고 디버깅이 필요할 때만 fat 이미지를 동적으로 연결하여, 보안 공격 표면 축소와 배포 효율성을 동시에 달성한다.

## 핵심 기여

- slim/fat 이미지 분리를 통한 66.6% 컨테이너 크기 감소
- 디버깅 도구의 동적 연결 메커니즘 구현
- 컨테이너 공격 표면 축소와 운영 편의성의 양립
- 기존 컨테이너 워크플로우와의 호환성 유지

## Firecracker와의 관계

Firecracker microVM에서 실행되는 컨테이너 이미지의 크기를 줄여 부팅 시간과 메모리 사용량을 개선할 수 있는 보완적 기술이다. Firecracker의 최소화 철학과 일맥상통하며, microVM의 게스트 이미지 최적화에 적용 가능하다.

## 키워드

컨테이너, 이미지 최적화, slim/fat 분리, 공격 표면 축소, 경량화
