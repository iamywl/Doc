# Unikernels: Library Operating Systems for the Cloud (MirageOS)

- **참고문헌 번호:** 9
- **저자:** Madhavapeddy et al.
- **학회/출처:** ASPLOS, 2013
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Unikernels+Library+Operating+Systems+for+the+Cloud+MirageOS)
- **분류:** 유사

## 요약

MirageOS는 OCaml로 작성된 유니커널 프레임워크로, 컴파일 타임에 애플리케이션에 필요한 OS 기능만을 선택적으로 포함시키는 Library OS 접근법을 제시했다. 불필요한 OS 계층을 제거하여 최소한의 이미지 크기와 작은 공격 표면을 달성하며, 클라우드 환경에서의 유니커널 활용 가능성을 제시한 선구적 연구이다.

## 핵심 기여

- OCaml 기반 유니커널 프레임워크 설계
- 컴파일 타임 특수화(compile-time specialization)를 통한 최소 이미지 생성
- Library OS 개념의 클라우드 환경 적용
- 타입 안전 언어(OCaml)를 활용한 보안 강화

## Firecracker와의 관계

유니커널은 Firecracker MicroVM의 게스트로 실행될 수 있는 대안적 워크로드 형태이다. MirageOS는 Firecracker보다 더 근본적인 수준에서 OS 추상화를 최소화하지만, 기존 애플리케이션 호환성 측면에서 Firecracker가 더 실용적이다.

## 키워드

MirageOS, Unikernel, Library OS, OCaml, Compile-time Specialization, Cloud
