# MigrOS: Transparent Live Migration Support for Containerised RDMA Applications

- **참고문헌 번호:** 125
- **저자:** (Not specified)
- **학회/출처:** ATC, 2021
- **논문 링크:** https://scholar.google.com/scholar?q=MigrOS+Transparent+Live+Migration+Containerised+RDMA+Applications
- **분류:** 보완

## 요약

MigrOS는 RDMA 애플리케이션을 사용하는 컨테이너의 투명한 라이브 마이그레이션을 지원하는 시스템이다. CRIU(Checkpoint/Restore In Userspace)를 기반으로 RDMA 연결 상태를 보존하면서 컨테이너를 다른 호스트로 마이그레이션한다. RDMA의 커널 바이패스 특성으로 인한 마이그레이션 어려움을 해결한다.

## 핵심 기여

- RDMA 컨테이너의 투명한 라이브 마이그레이션
- CRIU 기반 체크포인트/복원 메커니즘 확장
- RDMA 연결 상태 보존 기술
- 커널 바이패스 환경에서의 마이그레이션 과제 해결

## Firecracker와의 관계

MigrOS의 컨테이너 마이그레이션 기법은 Firecracker microVM의 라이브 마이그레이션에 참고가 될 수 있다. 특히 Firecracker의 스냅샷/복원 기능과 결합하여 RDMA 워크로드를 포함한 microVM 마이그레이션을 지원하는 데 활용 가능하다.

## 키워드

MigrOS, 라이브 마이그레이션, CRIU, RDMA, 컨테이너, 체크포인트, 복원
