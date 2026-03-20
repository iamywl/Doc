# Security Namespace: Making Linux Security Frameworks Available to Containers

- **참고문헌 번호:** 34
- **저자:** Sun et al.
- **학회/출처:** USENIX Security, 2018
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Security+Namespace+Making+Linux+Security+Frameworks+Available+to+Containers)
- **분류:** 보완, 컨테이너 보안 정책

## 요약

리눅스 컨테이너별로 독립적인 보안 정책(AppArmor, SELinux 등)을 적용할 수 있는 Security Namespace 개념을 제안한 논문이다. 기존에는 호스트 수준에서만 설정 가능했던 LSM(Linux Security Module) 정책을 네임스페이스화하여, 각 컨테이너가 자신만의 보안 정책을 독립적으로 관리할 수 있게 한다.

## 핵심 기여

- 리눅스 보안 프레임워크의 네임스페이스화 개념 제안
- 컨테이너별 독립적 보안 정책 적용 메커니즘 구현
- 기존 LSM 인프라와의 호환성을 유지하면서 격리 강화
- 멀티테넌트 환경에서의 세밀한 보안 정책 관리 지원

## Firecracker와의 관계

Firecracker가 VM 수준의 강력한 격리를 제공하는 반면, Security Namespace는 컨테이너 수준에서 보안 정책을 세밀하게 적용하는 보완적 접근이다. Firecracker microVM 내부에서 추가적인 보안 계층으로 활용될 수 있다.

## 키워드

리눅스 보안, 네임스페이스, LSM, AppArmor, SELinux, 컨테이너 보안
