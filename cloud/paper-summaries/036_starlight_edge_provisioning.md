# Starlight: Fast Container Provisioning on the Edge

- **참고문헌 번호:** 36
- **저자:** Chen et al.
- **학회/출처:** NSDI, 2022
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Starlight+Fast+Container+Provisioning+on+the+Edge)
- **분류:** 보완, 엣지 프로비저닝

## 요약

엣지/WAN 환경에서 컨테이너를 빠르게 프로비저닝하기 위해 델타 전송(delta transfer)과 지연 로딩(lazy loading)을 결합한 시스템이다. 이전에 배포된 이미지와의 차이만 전송하고, 실행에 필요한 데이터만 우선 로드하여 대역폭이 제한된 엣지 환경에서도 빠른 컨테이너 시작을 가능하게 한다.

## 핵심 기여

- 델타 전송과 지연 로딩의 결합을 통한 엣지 프로비저닝 최적화
- WAN 대역폭 제약 하에서의 컨테이너 시작 시간 단축
- 기존 이미지 레지스트리와 호환되는 설계
- 엣지 컴퓨팅 환경에서의 실용적 컨테이너 배포 솔루션

## Firecracker와의 관계

Firecracker가 엣지 컴퓨팅에 배포될 때 게스트 이미지의 효율적 프로비저닝을 지원하는 보완 기술이다. Firecracker의 빠른 부팅과 Starlight의 빠른 이미지 프로비저닝을 결합하면 엣지 서버리스 시나리오에서 종단 간 지연을 크게 줄일 수 있다.

## 키워드

엣지 컴퓨팅, 컨테이너 프로비저닝, 델타 전송, 지연 로딩, WAN
