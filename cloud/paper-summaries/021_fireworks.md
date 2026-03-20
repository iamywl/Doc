# Fireworks: VM-level post-JIT Snapshot

- **참고문헌 번호:** 21
- **저자:** Shin et al.
- **학회/출처:** EuroSys, 2022
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Fireworks+VM+level+post+JIT+Snapshot)
- **분류:** 확장

## 요약

Fireworks는 JIT 컴파일이 완료된 후의 VM 상태를 스냅샷으로 캡처하여 서버리스 함수의 시작 성능을 극대화한다. Post-JIT 스냅샷을 통해 20.6배의 속도 향상과 7.3배의 메모리 절감을 달성하며, JVM이나 V8 같은 JIT 기반 런타임의 웜업 비용을 완전히 제거한다.

## 핵심 기여

- VM 수준의 Post-JIT 스냅샷 메커니즘
- 20.6배 시작 속도 향상
- 7.3배 메모리 사용량 감소
- JIT 컴파일 결과를 포함한 최적화된 스냅샷 생성

## Firecracker와의 관계

Firecracker의 VM 스냅샷 기능을 JIT 컴파일러와 결합하여 확장한 연구이다. Firecracker MicroVM에서 JIT 컴파일이 완료된 시점의 스냅샷을 생성함으로써, 기본 스냅샷/복원보다 훨씬 효율적인 함수 시작이 가능하다.

## 키워드

Fireworks, Post-JIT Snapshot, VM Snapshot, JIT Compilation, Cold Start, EuroSys
