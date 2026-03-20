# Drawbridge: A New Form of Virtualization for Application Sandboxing

- **참고문헌 번호:** 45
- **저자:** Porter et al.
- **학회/출처:** ASPLOS, 2011
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Drawbridge+A+New+Form+of+Virtualization+for+Application+Sandboxing)
- **분류:** 반대, picoprocess + LibOS

## 요약

picoprocess와 Library OS를 결합한 새로운 형태의 애플리케이션 샌드박싱을 제안한 논문이다. 약 16MB의 경량 Windows 애플리케이션 샌드박스를 구현하여, 전체 VM 부팅 없이도 강력한 격리를 제공한다. picoprocess는 극도로 제한된 시스템 호출 인터페이스만 노출하고, Library OS가 나머지 OS 기능을 사용자 공간에서 제공한다.

## 핵심 기여

- picoprocess + Library OS 기반의 경량 애플리케이션 샌드박스(~16MB) 구현
- 극도로 제한된 ABI로 호스트 OS 공격 표면 최소화
- Library OS를 통한 기존 애플리케이션 호환성 유지
- VM 대비 훨씬 가벼운 격리로 유사한 보안 수준 달성

## Firecracker와의 관계

Firecracker가 KVM 기반 하드웨어 가상화로 격리하는 것과 달리, Drawbridge는 picoprocess와 Library OS로 하드웨어 가상화 없이 격리를 달성한다. 두 접근 모두 최소 공격 표면을 추구하지만, 격리 메커니즘과 성능 특성이 다르다.

## 키워드

picoprocess, Library OS, 샌드박싱, 가상화, 공격 표면 최소화
