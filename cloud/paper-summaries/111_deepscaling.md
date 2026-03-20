# DeepScaling: Microservices AutoScaling for Stable CPU Utilization

- **참고문헌 번호:** 111
- **저자:** (Not specified)
- **학회/출처:** SoCC, 2022
- **논문 링크:** https://scholar.google.com/scholar?q=DeepScaling+Microservices+AutoScaling+Stable+CPU+Utilization
- **분류:** 확장

## 요약

DeepScaling은 딥러닝을 활용하여 마이크로서비스의 CPU 사용률을 안정적으로 유지하는 오토스케일링 시스템이다. 기존 임계값 기반 스케일링의 진동(oscillation) 문제를 해결하고, 안정적인 CPU 활용을 달성한다. 딥러닝 모델이 복잡한 워크로드 패턴을 학습하여 최적의 인스턴스 수를 결정한다.

## 핵심 기여

- 딥러닝 기반 오토스케일링 시스템 설계
- CPU 사용률 안정화를 통한 진동 문제 해결
- 복잡한 워크로드 패턴 학습 및 예측
- 기존 임계값 기반 대비 안정성 개선

## Firecracker와의 관계

DeepScaling의 안정적 오토스케일링 기법은 Firecracker microVM 풀의 크기를 동적으로 관리하는 데 적용 가능하다. microVM의 CPU 할당을 안정적으로 유지하면서 워크로드 변동에 대응하는 전략을 제공한다.

## 키워드

DeepScaling, 딥러닝, 오토스케일링, CPU 안정화, 마이크로서비스, 진동 제거
