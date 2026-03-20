# seL4 as VMM: Microkernel-based Virtual Machine Monitor

- **참고문헌 번호:** 90
- **저자:** de Matos, Ahvenjarvi
- **학회/출처:** MDPI, 2022
- **논문 링크:** https://scholar.google.com/scholar?q=seL4+VMM+Microkernel+Virtual+Machine+Monitor
- **분류:** 대안

## 요약

형식 검증(formally verified)된 마이크로커널 seL4를 가상 머신 모니터(VMM)로 활용하는 방안을 연구한다. seL4의 수학적으로 증명된 보안 속성을 VMM에 적용하여, 기존 하이퍼바이저보다 더 높은 수준의 보안 보장을 제공한다. 마이크로커널의 최소 TCB(Trusted Computing Base)가 공격 표면을 줄인다.

## 핵심 기여

- 형식 검증된 마이크로커널 기반 VMM 설계
- seL4의 보안 증명을 VMM 수준으로 확장
- 최소 TCB를 통한 공격 표면 축소
- 기존 하이퍼바이저(KVM, Xen) 대비 보안성 분석

## Firecracker와의 관계

seL4 기반 VMM은 Firecracker의 대안적 접근법이다. Firecracker가 KVM 위에서 최소 VMM을 구현한 반면, seL4는 마이크로커널 자체를 VMM으로 활용한다. seL4의 형식 검증은 Firecracker보다 더 강력한 보안 보장을 제공하지만, 실용성과 생태계 측면에서 차이가 있다.

## 키워드

seL4, 마이크로커널, VMM, 형식 검증, TCB, 보안 증명, 하이퍼바이저
