# Cross-container eBPF Attacks

- **참고문헌 번호:** 85
- **저자:** He et al.
- **학회/출처:** USENIX Security 2023
- **논문 링크:** https://scholar.google.com/scholar?q=Cross-container+eBPF+Attacks
- **분류:** 보완

## 요약

eBPF의 크로스 컨테이너 공격 벡터를 체계적으로 분석하는 보안 연구이다. eBPF 프로그램이 컨테이너 경계를 넘어 다른 컨테이너의 정보를 유출하거나 동작을 방해할 수 있는 공격 시나리오를 발견하고, 이에 대한 방어 메커니즘을 논의한다.

## 핵심 기여

- eBPF 기반 크로스 컨테이너 공격 벡터의 체계적 발견 및 분류
- 컨테이너 격리 경계를 우회하는 eBPF 공격 시나리오 실증
- 정보 유출 및 서비스 방해 공격 가능성 입증
- 방어 메커니즘 및 대응 방안 제시

## Firecracker와의 관계

eBPF 공격이 컨테이너 경계를 넘을 수 있다는 발견은 Firecracker의 VM 기반 격리의 가치를 더욱 부각시킨다. 컨테이너 수준의 격리는 eBPF를 통해 우회될 수 있지만, Firecracker의 하이퍼바이저 기반 격리는 이러한 공격에 대해 더 강력한 방어선을 제공한다.

## 키워드

eBPF attacks, cross-container, container escape, security vulnerability, isolation bypass
