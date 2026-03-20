# HFI: Hardware-enforced Function Isolation

- **참고문헌 번호:** 50
- **저자:** Narayan et al.
- **학회/출처:** ASPLOS, 2023
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=HFI+Hardware-enforced+Function+Isolation)
- **분류:** 반대, HW 함수 격리

## 요약

함수 수준의 격리를 하드웨어로 강제하는 CPU 확장을 제안한 논문이다. 기존의 프로세스나 VM 단위의 격리보다 훨씬 세밀한 함수 단위에서 하드웨어 기반 메모리 격리를 제공하여, 소프트웨어 SFI의 성능 오버헤드 없이 강력한 격리를 달성한다.

## 핵심 기여

- 함수 수준 하드웨어 격리를 위한 CPU 확장(ISA 변경) 설계
- SFI의 소프트웨어 오버헤드 없이 하드웨어 기반 함수 격리 달성
- 기존 코드의 최소한의 수정으로 함수 격리 적용 가능
- 프로토타입 구현 및 성능/보안 평가

## Firecracker와의 관계

Firecracker의 VM 수준 격리와 반대로 함수 수준의 극도로 세밀한 하드웨어 격리를 제안한다. HFI가 상용 CPU에 도입되면, Firecracker VMM 내부에서 함수 단위 격리를 추가하거나, 특정 워크로드에서 VM 격리를 대체하는 경량 대안으로 활용될 수 있다.

## 키워드

하드웨어 격리, 함수 격리, CPU 확장, ISA, Software Fault Isolation 대안
