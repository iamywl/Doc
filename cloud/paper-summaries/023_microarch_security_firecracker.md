# Microarchitectural Security of AWS Firecracker VMM

- **참고문헌 번호:** 23
- **저자:** Weissman et al.
- **학회/출처:** ICISS, 2024
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Microarchitectural+Security+of+AWS+Firecracker+VMM)
- **분류:** 보완, 사이드 채널 분석

## 요약

Firecracker VMM에 대한 마이크로아키텍처 수준의 보안 분석을 수행한 논문이다. Spectre 및 MDS(Microarchitectural Data Sampling) 공격을 Firecracker 환경에서 실험하여, 특히 Spectre-PHT 변종이 악용 가능함을 발견하였다. 이는 Firecracker의 경량 가상화가 하드웨어 수준 사이드 채널 공격에 여전히 취약할 수 있음을 시사한다.

## 핵심 기여

- Firecracker VMM 환경에서 Spectre-PHT 공격 변종의 실제 악용 가능성을 입증
- MDS 공격에 대한 Firecracker의 방어 상태를 체계적으로 분석
- microVM 기반 격리 환경의 마이크로아키텍처 보안 한계를 규명
- 경량 VMM의 보안 강화를 위한 하드웨어/소프트웨어 완화 방안 제안

## Firecracker와의 관계

Firecracker의 보안 모델을 보완하는 연구로, Firecracker가 제공하는 소프트웨어 수준의 격리가 하드웨어 마이크로아키텍처 공격에 대해 충분하지 않을 수 있음을 보여준다. Firecracker의 보안을 강화하기 위해 Spectre 완화 기법의 적용 필요성을 강조한다.

## 키워드

Firecracker, Spectre, MDS, 마이크로아키텍처 보안, 사이드 채널 공격, microVM
