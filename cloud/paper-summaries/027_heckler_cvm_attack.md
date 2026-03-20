# HECKLER: Breaking Confidential VMs with Malicious Interrupt Injection

- **참고문헌 번호:** 27
- **저자:** Schluter et al.
- **학회/출처:** USENIX Security, 2024
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=HECKLER+Breaking+Confidential+VMs+with+Malicious+Interrupt+Injection)
- **분류:** 보완, CVM 공격

## 요약

악의적인 하이퍼바이저가 AMD SEV-SNP 기밀 VM에 인터럽트를 주입하여 기밀성을 침해할 수 있는 공격(HECKLER)을 제시한 논문이다. 기밀 VM이 하드웨어 메모리 암호화로 데이터를 보호하더라도, 인터럽트 주입을 통해 제어 흐름을 조작하고 민감한 정보를 유출할 수 있음을 입증한다.

## 핵심 기여

- AMD SEV-SNP 기밀 VM에 대한 인터럽트 주입 공격 벡터 발견
- 악의적 하이퍼바이저의 인터럽트 조작을 통한 기밀 데이터 유출 입증
- 기존 기밀 VM 보안 모델의 인터럽트 처리 취약점 분석
- 인터럽트 기반 공격에 대한 방어 기법 제안

## Firecracker와의 관계

Firecracker가 기밀 VM 환경에서 사용될 경우의 보안 위협을 분석하는 데 중요한 연구이다. Firecracker VMM이 하이퍼바이저 역할을 수행하므로, VMM의 무결성이 기밀 VM 보안에 직접적으로 영향을 미치며, 이에 대한 방어가 필요함을 보여준다.

## 키워드

Confidential VM, AMD SEV-SNP, 인터럽트 주입, 하이퍼바이저 공격, 보안 취약점
