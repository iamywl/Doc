# Namespace-Cgroup Desynchronization in Containers

- **참고문헌 번호:** 118
- **저자:** (Not specified)
- **학회/출처:** NDSS
- **논문 링크:** https://scholar.google.com/scholar?q=Namespace+Cgroup+Desynchronization+Containers+NDSS
- **분류:** 보완

## 요약

컨테이너의 네임스페이스와 cgroup 간 비동기화(desynchronization) 취약점을 발견하고 분석한 연구이다. 네임스페이스와 cgroup의 불일치가 컨테이너 탈출(escape) 등의 보안 위협으로 이어질 수 있음을 보여주고, 이를 악용한 공격 시나리오를 제시한다.

## 핵심 기여

- 네임스페이스-cgroup 비동기화 취약점 발견
- 취약점을 이용한 공격 시나리오 시연
- 컨테이너 격리 메커니즘의 근본적 한계 분석
- 대응 방안 및 완화 전략 제시

## Firecracker와의 관계

이 논문이 보여주는 컨테이너 격리의 근본적 취약점은 Firecracker microVM의 존재 이유를 직접적으로 뒷받침한다. Firecracker는 하드웨어 가상화를 통해 이러한 OS 수준 격리 취약점에 영향받지 않는 강력한 격리를 제공한다.

## 키워드

네임스페이스, cgroup, 비동기화, 컨테이너 보안, 취약점, 컨테이너 탈출, 격리
