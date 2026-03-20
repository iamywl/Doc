# 분산시스템 & 클라우드 논문 요약 + 링크 모음

> 각 논문 제목이 **원본 논문 링크**로 연결되는 간결한 요약본입니다.
> 상세 분석은 [distributed-systems-cloud-papers/](../distributed-systems-cloud-papers/)를 참고하세요.

---

## 문서 구성

| # | 파일 | 분야 | 논문 수 |
|---|---|---|---|
| 01 | [기초이론 요약](01_분산컴퓨팅_기초이론_요약.md) | Lamport Clocks, CAP, FLP, CRDT, DHT, MapReduce, Spark | ~47편 |
| 02 | [합의알고리즘 요약](02_합의알고리즘_분산조정_요약.md) | Paxos, Raft, PBFT, HotStuff, ZooKeeper, Chubby | ~33편 |
| 03 | [파일시스템/스토리지 요약](03_분산파일시스템_객체스토리지_요약.md) | GFS, HDFS, Ceph, S3, Kafka, Delta Lake, 이레이저 코딩 | ~25편 |
| 04 | [가상화/컨테이너/서버리스 요약](04_가상화_컨테이너_서버리스_요약.md) | Xen, KVM, Firecracker, Borg, K8s, 유니커널, FaaS | ~35편 |
| 05 | [네트워킹/서비스메시/eBPF 요약](05_네트워킹_서비스메시_eBPF_요약.md) | OpenFlow, SDN, eBPF, Cilium, RDMA, SmartNIC, P4 | ~30편 |
| 06 | [보안/옵저버빌리티 요약](06_보안_옵저버빌리티_카오스엔지니어링_요약.md) | TEE, 제로트러스트, Dapper, 카오스 엔지니어링, SRE, AIOps | ~44편 |
| 07 | [분산ML/엣지/그린/양자 요약](07_분산ML_엣지_그린_양자_요약.md) | ZeRO, Megatron, vLLM, 연합학습, 엣지, 그린, 양자 | ~45편 |
| 08 | [분산스토리지/DB 요약](08_분산스토리지_분산DB_요약.md) | Dynamo, Spanner, CockroachDB, TiDB, Aurora, 벡터DB | ~36편 |

**총 약 295편** | 모든 논문 제목 클릭 시 원본 논문으로 이동

---

## 사용법

각 요약 파일에서 관심 있는 논문의 제목(링크)을 클릭하면 원본 논문(ACM DL, IEEE Xplore, USENIX, arXiv 등)으로 바로 이동합니다.

```
### [Time, Clocks, and the Ordering of Events in a Distributed System](https://lamport.azurewebsites.net/pubs/time-clocks.pdf)
- **저자**: Leslie Lamport
- **연도/학회**: 1978, Communications of the ACM
- **핵심 요약**: 분산 시스템에서 이벤트 순서를 정의하는 논리적 시계 개념을 제시한 기념비적 논문.
```

---

## 관련 디렉토리

| 디렉토리 | 내용 |
|---|---|
| [distributed-systems-papers-summary/](.) | 논문별 링크 + 간결 요약 (현재) |
| [distributed-systems-cloud-papers/](../distributed-systems-cloud-papers/) | 논문별 상세 분석 (저자, 학회, 기여, 중요도 등) |

---

> 마지막 업데이트: 2026-03-20
