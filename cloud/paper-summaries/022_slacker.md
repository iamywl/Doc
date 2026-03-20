# Slacker: Fast Distribution with Lazy Docker Containers

- **참고문헌 번호:** 22
- **저자:** Harter et al.
- **학회/출처:** FAST, 2016
- **논문 링크:** [Google Scholar](https://scholar.google.com/scholar?q=Slacker+Fast+Distribution+with+Lazy+Docker+Containers)
- **분류:** 보완

## 요약

Slacker는 Docker 컨테이너 이미지의 지연 로딩(lazy loading)을 통해 배포 속도를 5배 향상시킨 연구이다. 분석 결과 컨테이너 pull 시간의 76%를 이미지 다운로드가 차지하지만 실제로 사용되는 데이터는 6.4%에 불과함을 발견하고, 필요한 데이터만 온디맨드로 로드하는 방식을 제안한다.

## 핵심 기여

- 컨테이너 이미지의 76% pull 시간 중 6.4%만 실제 사용된다는 발견
- 지연 로딩(lazy loading) 기반 이미지 배포
- 5배의 배포 속도 향상
- NFS 기반의 온디맨드 데이터 로딩 메커니즘

## Firecracker와의 관계

Firecracker MicroVM의 게스트 이미지 배포를 보완하는 연구이다. Slacker의 지연 로딩 개념은 Firecracker의 MicroVM 이미지 또는 스냅샷 배포에 적용되어 콜드 스타트 시 이미지 로딩 시간을 줄이는 데 활용될 수 있다.

## 키워드

Slacker, Lazy Loading, Docker, Container Image, Fast Distribution, On-demand Loading
