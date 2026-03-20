# gVisor: Container Runtime Sandbox

- **참고문헌 번호:** 7
- **저자:** Google
- **학회/출처:** Google, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=gVisor+Container+Runtime+Sandbox)
- **분류:** 유사

## 요약

gVisor는 Google이 개발한 사용자 공간 커널로, Go 언어로 Linux 시스템 콜의 약 70%를 재구현한다. 컨테이너 워크로드가 호스트 커널에 직접 접근하지 않고 gVisor의 Sentry를 통해 약 20개의 호스트 syscall만 사용하도록 하여 공격 표면을 대폭 줄인다.

## 핵심 기여

- Go로 구현된 사용자 공간 Linux 커널(Sentry)
- ~70% Linux syscall 재구현으로 높은 호환성
- 호스트 커널 접촉을 ~20개 syscall로 제한
- 하드웨어 가상화 없이 강력한 격리 제공

## Firecracker와의 관계

Firecracker가 하드웨어 가상화(KVM)를 통해 격리를 달성하는 반면, gVisor는 사용자 공간 커널을 통해 소프트웨어적으로 격리를 구현한다. 두 기술 모두 멀티테넌트 서버리스 환경의 격리 솔루션이지만, 성능-보안 트레이드오프가 다르다.

## 키워드

gVisor, Sentry, User-space Kernel, Go, Container Sandbox, Syscall Interception, Google
