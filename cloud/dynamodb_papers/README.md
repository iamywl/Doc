# DynamoDB 논문 기반 조사

Amazon DynamoDB의 학술적 배경을 논문 중심으로 조사한 문서 모음.

## 문서 구조

| 파일 | 내용 |
|------|------|
| [00_BK21_tier_system.md](00_BK21_tier_system.md) | BK21 CS 분야 우수학술대회 등급 체계 (인정 IF 4/3/2/1) |
| [01_dynamo_origin.md](01_dynamo_origin.md) | Dynamo 원본 논문(SOSP 2007) + DynamoDB 진화(ATC 2022, 2023) |
| [02_foundational_papers.md](02_foundational_papers.md) | Dynamo가 직접 참조한 기반 논문 (Consistent Hashing, Vector Clocks, Gossip, CAP, Quorum 등) |
| [03_distributed_systems_foundations.md](03_distributed_systems_foundations.md) | 분산 시스템 이론적 기반 (Lamport Clocks, Paxos, FLP, Raft, GFS, MapReduce 등) |
| [04_competing_systems.md](04_competing_systems.md) | 동시대/경쟁 분산 DB 비교 (Bigtable, Spanner, Cassandra, CockroachDB, TiDB 등) |
| [05_paper_influence_map.md](05_paper_influence_map.md) | 논문 영향 관계도 + BK21 점수별 전체 인덱스 |

## 참고문헌 원칙

- **모든 참고문헌은 학술 논문(peer-reviewed paper)만 포함**
- BK21 인정 IF 기준으로 등급 표기
- 저널 논문은 JCR Impact Factor 표기

## 수록 논문 통계

| BK21 등급 | 논문 수 | 대표 학회 |
|----------|---------|----------|
| IF 4 (최우수) | 15편 | SOSP, OSDI, SIGMOD, VLDB, SIGCOMM, STOC |
| IF 3 (우수) | 7편 | ATC, PODC, ICDE |
| SCIE 저널 | 5편 | CACM, TOCS, JACM |
| 기타/미등재 | 8편 | CIDR, 워크숍, 뉴스레터 |
| **합계** | **~35편** | |
