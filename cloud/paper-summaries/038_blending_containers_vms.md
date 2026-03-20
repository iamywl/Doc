# Blending Containers and Virtual Machines

- **참고문헌 번호:** 38
- **저자:** Anjali et al.
- **학회/출처:** VEE, 2020
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Blending+Containers+and+Virtual+Machines)
- **분류:** 반대, FC/gVisor 비교

## 요약

Firecracker와 gVisor의 코드 커버리지를 분석하여 두 격리 기술의 보안 특성을 비교한 논문이다. 컨테이너와 가상 머신의 경계를 혼합하는 접근 방식들의 실제 커널 코드 노출 정도를 측정하여, 각 기술이 제공하는 격리의 실질적 강도를 정량적으로 평가한다.

## 핵심 기여

- Firecracker와 gVisor의 호스트 커널 코드 커버리지 비교 분석
- 컨테이너/VM 혼합 기술들의 공격 표면 정량적 측정
- 각 격리 기술의 보안 트레이드오프에 대한 실증적 데이터 제공
- 격리 기술 선택을 위한 체계적 비교 프레임워크 제시

## Firecracker와의 관계

Firecracker를 직접 분석 대상으로 삼아 gVisor와 비교한 연구로, Firecracker의 격리 강도를 실증적으로 평가한다. Firecracker가 노출하는 호스트 커널 공격 표면의 범위를 정량화하여, Firecracker 보안 모델의 실제 효과를 검증한다.

## 키워드

Firecracker, gVisor, 코드 커버리지, 공격 표면, 컨테이너, VM, 격리 비교
