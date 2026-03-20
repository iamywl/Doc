# Unikraft: Fast, Specialized Unikernels the Easy Way

- **참고문헌 번호:** 10
- **저자:** Kuenzer et al.
- **학회/출처:** EuroSys, 2021 (Best Paper)
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Unikraft+Fast+Specialized+Unikernels+the+Easy+Way)
- **분류:** 유사

## 요약

Unikraft는 모듈러 마이크로 라이브러리 OS 아키텍처를 통해 유니커널의 구축과 배포를 용이하게 만든 프레임워크이다. 기존 유니커널의 이식성 문제를 해결하면서 1.7~2.7배의 성능 향상, ~1MB의 이미지 크기, ~1ms의 부팅 시간을 달성했다. EuroSys 2021 Best Paper로 선정되었다.

## 핵심 기여

- 모듈러 마이크로 라이브러리 OS 아키텍처
- 1.7~2.7배 성능 향상 달성
- ~1MB의 최소 이미지 크기
- ~1ms의 초고속 부팅 시간
- POSIX 호환성을 통한 기존 애플리케이션 지원

## Firecracker와의 관계

Unikraft는 Firecracker의 게스트로 실행될 수 있는 초경량 유니커널을 제공한다. Firecracker의 ~125ms 부팅 시간 대비 ~1ms를 달성하며, 두 기술을 결합하면 서버리스 환경에서 최적의 시작 시간과 격리를 동시에 달성할 수 있다.

## 키워드

Unikraft, Unikernel, Modular, Micro-library OS, Fast Boot, POSIX Compatibility, EuroSys
