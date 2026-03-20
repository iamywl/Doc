# LightVM: My VM is Lighter (and Safer) than your Container

- **참고문헌 번호:** 2
- **저자:** Manco et al.
- **학회/출처:** SOSP, 2017
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=LightVM+My+VM+is+Lighter+and+Safer+than+your+Container)
- **분류:** 유사

## 요약

LightVM은 Xen 하이퍼바이저를 재설계하여 VM의 부팅 시간을 2.3ms까지 단축한 연구이다. 분산 제어 플레인을 도입하여 Xen의 병목인 toolstack을 제거하고, 수천 개의 경량 VM을 동시에 실행할 수 있도록 하였다. 컨테이너 수준의 민첩성과 VM 수준의 격리를 동시에 제공한다.

## 핵심 기여

- Xen 하이퍼바이저의 아키텍처 재설계
- 2.3ms의 극초단 VM 부팅 시간 달성
- 분산 제어 플레인(split toolstack) 도입으로 확장성 확보
- Tinyx 등 경량 게스트 이미지 생성 도구 개발

## Firecracker와의 관계

Firecracker와 동일한 목표(경량 VM)를 추구하지만 Xen 기반으로 접근한 유사 연구이다. Firecracker가 KVM 기반인 반면, LightVM은 Xen을 재설계하여 더 빠른 부팅 시간을 달성했으나, 프로덕션 적용 측면에서는 Firecracker가 더 널리 채택되었다.

## 키워드

LightVM, Xen, Lightweight VM, Fast Boot, Distributed Control Plane, Tinyx
