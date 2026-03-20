# Isolation Without Taxation: Near-Zero-Cost Transitions for WebAssembly and SFI

- **참고문헌 번호:** 44
- **저자:** Kolosick et al.
- **학회/출처:** POPL, 2022
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Isolation+Without+Taxation+Near-Zero-Cost+Transitions+for+WebAssembly+and+SFI)
- **분류:** 반대, SFI 제로 비용

## 요약

WebAssembly 및 SFI(Software Fault Isolation) 환경에서 격리 영역 간 전환 비용을 거의 제로로 줄이는 기법을 제안한 논문이다. 기존 SFI 전환에서 발생하는 레지스터 저장/복원, 스택 전환 등의 오버헤드를 제거하여 빈번한 격리 경계 전환에서도 성능 저하가 거의 없음을 보장한다.

## 핵심 기여

- SFI 격리 영역 전환 비용을 거의 제로로 감소시키는 기법 제안
- Wasm 및 SFI 전환 메커니즘의 형식적 안전성 증명
- 기존 SFI 전환 오버헤드의 근본 원인 분석 및 해결
- 빈번한 샌드박스 전환이 필요한 워크로드에서의 성능 개선

## Firecracker와의 관계

Firecracker의 VM 진입/퇴출(VM exit) 오버헤드와 비교하여, SFI 기반 격리의 전환 비용이 극도로 낮을 수 있음을 보여준다. 이는 경량 격리가 필요한 시나리오에서 VM 기반 격리 대신 SFI를 선택할 수 있는 근거를 제공한다.

## 키워드

Software Fault Isolation, WebAssembly, 제로 비용 전환, 격리, 형식 검증
