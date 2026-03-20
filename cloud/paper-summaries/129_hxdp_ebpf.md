# hXDP: Efficient Software Packet Processing on FPGA NICs

- **참고문헌 번호:** 129
- **저자:** (Not specified)
- **학회/출처:** SIGCOMM, 2021
- **논문 링크:** https://scholar.google.com/scholar?q=hXDP+eBPF+Kernel+Extensions+Packet+Processing+SIGCOMM
- **분류:** 보완

## 요약

hXDP는 eBPF를 활용한 고성능 패킷 처리 시스템이다. eBPF 프로그램을 네트워크 경로에 삽입하여 커널 수준에서 효율적인 패킷 필터링, 로드밸런싱, 라우팅을 수행한다. 기존 소프트웨어 기반 패킷 처리 대비 높은 처리량과 낮은 지연 시간을 달성한다.

## 핵심 기여

- eBPF 기반 고성능 패킷 처리 아키텍처
- 커널 수준 패킷 필터링 및 라우팅
- 높은 처리량과 낮은 지연 시간 달성
- 프로그래밍 가능한 네트워크 데이터 플레인

## Firecracker와의 관계

eBPF 기반 패킷 처리는 Firecracker microVM의 네트워크 성능을 향상시키는 데 활용될 수 있다. virtio-net을 통한 패킷 전달 경로에 eBPF를 적용하여 네트워크 오버헤드를 줄이고, microVM 간 고성능 통신을 지원할 수 있다.

## 키워드

hXDP, eBPF, 패킷 처리, XDP, 고성능 네트워킹, 커널 바이패스, 데이터 플레인
