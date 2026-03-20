# Autopilot: Workload Autoscaling at Google Scale

- **참고문헌 번호:** 109
- **저자:** Rzadca et al.
- **학회/출처:** EuroSys, 2020
- **논문 링크:** https://scholar.google.com/scholar?q=Autopilot+Workload+Autoscaling+Google+Rzadca
- **분류:** 확장

## 요약

Autopilot는 Google 생산 환경에서 사용되는 워크로드 오토스케일러로, 수평(horizontal) 및 수직(vertical) 오토스케일링을 통합적으로 수행한다. Google 전체 플릿의 48% 이상에 적용되어 있으며, 워크로드의 실제 자원 사용량을 기반으로 자동으로 자원 할당을 조정한다.

## 핵심 기여

- 수평/수직 오토스케일링 통합 시스템
- Google 플릿 48% 이상에 적용된 대규모 생산 시스템
- 실제 자원 사용량 기반 자동 조정
- 오버프로비저닝 감소와 SLO 준수 동시 달성

## Firecracker와의 관계

Autopilot의 오토스케일링 기법은 Firecracker 기반 서버리스 플랫폼에서 microVM 풀의 크기를 동적으로 조절하는 데 참고가 된다. 워크로드 패턴에 따른 microVM 사전 워밍(pre-warming) 전략에도 활용 가능하다.

## 키워드

Autopilot, 오토스케일링, Google, 수평/수직 스케일링, 자원 조정, SLO, 생산 시스템
