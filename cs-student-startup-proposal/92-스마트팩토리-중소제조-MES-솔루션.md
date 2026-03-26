# 스마트팩토리 중소제조 MES 솔루션 (팩토리원)
## 2026년 예비창업패키지 사업계획서

---

## 1. 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | 팩토리원 (FactoryOne) |
| 대표자 | 강민재 (컴퓨터공학과 석사과정) |
| 팀구성 | 대표 1명, 백엔드 1명, IoT개발 1명, 제조공정 전문가 1명, 프론트엔드 1명 |
| 창업아이템 | 중소 제조업 맞춤 클라우드 MES(제조실행시스템) 솔루션 |
| 분야 | 스마트팩토리 / 제조 DX / IoT |
| 사업화단계 | MVP 개발 완료 |
| 목표시장 | 중소 제조업체 (종업원 10~100인) |
| 신청금액 | 1억원 |

---

## 2. 창업 아이템 개요

| 항목 | 내용 |
|------|------|
| 아이템명 | 팩토리원 - 중소 제조업 맞춤 클라우드 MES |
| 핵심기술 | 클라우드 MES, IoT 센서 연동, AI 품질 예측, 실시간 모니터링 |
| 목표고객 | 종업원 10~100인 중소 제조업체 (금속, 플라스틱, 전자부품 등) |
| 문제정의 | 중소 제조업 스마트팩토리 도입률 24.7%, 기존 MES 도입 비용 3억원 이상 |
| 솔루션 | 월 50만원부터 시작하는 클라우드 MES, 무선 IoT 센서, 태블릿 기반 현장 UI |
| 비즈니스모델 | SaaS 구독 + IoT 하드웨어 + 구축 서비스 |
| 시장규모 | 국내 스마트팩토리 시장 18.5조원(2025), MES 시장 1.2조원 |
| 경쟁우위 | 중소 제조업 특화 UX, 90% 낮은 도입 비용, 3일 내 구축 |
| 수익목표 | 1차년도 3억원, 2차년도 12억원, 3차년도 38억원 |

---

## 3. 문제 인식

### 3.1 핵심 문제 통계

| 지표 | 수치 | 출처 |
|------|------|------|
| 중소 제조업 스마트팩토리 도입률 | 24.7% | 중소벤처기업부 (2025) |
| MES 미도입 중소 제조업체 비율 | 82.3% | 스마트제조혁신추진단 (2025) |
| 기존 MES 평균 도입 비용 | 3~5억원 | 한국스마트팩토리데이터센터 (2024) |
| MES 구축 평균 소요 기간 | 6~12개월 | 한국정보통신산업협회 (2024) |
| 중소 제조업 불량률 | 평균 3.8% | 한국표준협회 (2025) |
| 수작업 생산 관리 비율 | 67.4% (엑셀/수기) | 중소기업중앙회 (2025) |
| 설비 가동률 | 평균 62.3% | 통계청 제조업동향 (2025) |
| 생산 데이터 활용률 | 18.7% | 중소벤처기업부 (2024) |
| 납기 지연율 (중소제조) | 15.2% | 대한상공회의소 (2025) |

### 3.2 사용자 페인포인트

> "MES가 필요한 건 알지만, 도입 비용이 3억 원이라니 우리 같은 20인 규모 공장에서는 꿈도 못 꿉니다. 그래서 아직도 엑셀로 생산 관리를 합니다."
> — 금속 가공 중소업체 대표

> "스마트팩토리 정부 지원금을 받아서 도입했는데, 우리 공장에 안 맞는 기능이 대부분이라 현장 직원들이 안 써요. 결국 다시 수기로 돌아갔습니다."
> — 플라스틱 사출 업체 공장장

> "주문이 들어오면 생산 계획을 머릿속으로 짜요. 재고가 얼마나 있는지, 어떤 설비가 비어 있는지 실시간으로 파악이 안 됩니다."
> — 전자부품 제조 팀장

> "불량이 나와도 원인을 찾는 데 며칠씩 걸려요. 어떤 설비에서, 어떤 조건에서 불량이 나왔는지 기록이 없으니까요."
> — 품질 관리 담당자

### 3.3 문제 구조

```svg
<svg viewBox="0 0 900 470" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="sf_g1" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#e67e22;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#f39c12;stop-opacity:1" />
    </linearGradient>
  </defs>

  <text x="450" y="30" text-anchor="middle" font-size="20" font-weight="bold" fill="#2c3e50">중소 제조업 디지털 전환 장벽</text>

  <!-- Barriers -->
  <rect x="50" y="60" width="250" height="100" rx="12" fill="#e74c3c" opacity="0.9"/>
  <text x="175" y="95" text-anchor="middle" font-size="14" fill="white" font-weight="bold">비용 장벽</text>
  <text x="175" y="120" text-anchor="middle" font-size="11" fill="white">MES 도입 3~5억원</text>
  <text x="175" y="140" text-anchor="middle" font-size="11" fill="white">중소기업 IT 예산 부족</text>

  <rect x="330" y="60" width="250" height="100" rx="12" fill="#e74c3c" opacity="0.9"/>
  <text x="455" y="95" text-anchor="middle" font-size="14" fill="white" font-weight="bold">복잡성 장벽</text>
  <text x="455" y="120" text-anchor="middle" font-size="11" fill="white">구축 6~12개월</text>
  <text x="455" y="140" text-anchor="middle" font-size="11" fill="white">전문 인력 필요</text>

  <rect x="610" y="60" width="250" height="100" rx="12" fill="#e74c3c" opacity="0.9"/>
  <text x="735" y="95" text-anchor="middle" font-size="14" fill="white" font-weight="bold">활용 장벽</text>
  <text x="735" y="120" text-anchor="middle" font-size="11" fill="white">현장 직원 IT 역량 부족</text>
  <text x="735" y="140" text-anchor="middle" font-size="11" fill="white">커스터마이징 한계</text>

  <!-- Result -->
  <rect x="250" y="195" width="400" height="50" rx="8" fill="#e67e22"/>
  <text x="450" y="225" text-anchor="middle" font-size="14" fill="white">결과: 82.3%가 MES 미도입, 엑셀/수기 관리</text>

  <!-- Solution -->
  <rect x="200" y="290" width="500" height="80" rx="15" fill="url(#sf_g1)"/>
  <text x="450" y="320" text-anchor="middle" font-size="16" fill="white" font-weight="bold">팩토리원: 중소 제조업 맞춤 클라우드 MES</text>
  <text x="450" y="345" text-anchor="middle" font-size="12" fill="white">월 50만원 | 3일 구축 | 태블릿 UX | IoT 센서</text>

  <!-- Benefits -->
  <rect x="50" y="400" width="230" height="50" rx="8" fill="#27ae60"/>
  <text x="165" y="430" text-anchor="middle" font-size="12" fill="white">도입비 90% 절감</text>

  <rect x="310" y="400" width="260" height="50" rx="8" fill="#27ae60"/>
  <text x="440" y="430" text-anchor="middle" font-size="12" fill="white">구축 기간 3일 (vs 6개월)</text>

  <rect x="600" y="400" width="260" height="50" rx="8" fill="#27ae60"/>
  <text x="730" y="430" text-anchor="middle" font-size="12" fill="white">현장 직원도 쉬운 태블릿 UX</text>

  <line x1="450" y1="370" x2="165" y2="400" stroke="#27ae60" stroke-width="1.5"/>
  <line x1="450" y1="370" x2="440" y2="400" stroke="#27ae60" stroke-width="1.5"/>
  <line x1="450" y1="370" x2="730" y2="400" stroke="#27ae60" stroke-width="1.5"/>
</svg>
```

---

## 4. 솔루션

### 4.1 핵심 기능

#### 기능 1: 클라우드 MES (생산 관리)
- 작업지시서 생성/배포 (태블릿으로 현장 실시간 전달)
- 생산 실적 자동 집계 (센서 또는 태블릿 터치 입력)
- 재공/재고 실시간 현황
- 생산 계획 대비 실적 대시보드

#### 기능 2: IoT 설비 모니터링
- 무선 IoT 센서 (진동, 온도, 전류) 자석 부착형
- 설비 가동/비가동 자동 감지
- 이상 징후 감지 및 예지보전 알림
- OEE(설비종합효율) 자동 산출

#### 기능 3: AI 품질 관리
- 공정 조건(온도, 압력, 속도)과 불량 상관 분석
- 불량 발생 예측 및 사전 알림
- 불량 이력 추적 (어떤 설비, 어떤 작업자, 어떤 자재)
- 통계적 공정 관리(SPC) 자동 차트

#### 기능 4: 실시간 현장 대시보드
- 공장 TV/모니터용 안돈(Andon) 보드
- 생산 현황, 불량률, 가동률 실시간 표시
- 경보/알림 (목표 미달, 이상 발생)
- 모바일 앱으로 공장장 원격 모니터링

### 4.2 시스템 아키텍처

```svg
<svg viewBox="0 0 900 520" xmlns="http://www.w3.org/2000/svg">
  <text x="450" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">팩토리원 시스템 아키텍처</text>

  <!-- Shop Floor -->
  <rect x="20" y="55" width="860" height="100" rx="10" fill="#ecf0f1" stroke="#bdc3c7"/>
  <text x="50" y="80" font-size="13" font-weight="bold" fill="#2c3e50">현장 (Shop Floor)</text>
  <rect x="40" y="90" width="150" height="50" rx="6" fill="#e67e22"/>
  <text x="115" y="112" text-anchor="middle" font-size="10" fill="white">IoT 센서</text>
  <text x="115" y="128" text-anchor="middle" font-size="9" fill="white">(진동/온도/전류)</text>
  <rect x="210" y="90" width="150" height="50" rx="6" fill="#e67e22"/>
  <text x="285" y="112" text-anchor="middle" font-size="10" fill="white">작업자 태블릿</text>
  <text x="285" y="128" text-anchor="middle" font-size="9" fill="white">(실적 입력/작업지시)</text>
  <rect x="380" y="90" width="150" height="50" rx="6" fill="#e67e22"/>
  <text x="455" y="112" text-anchor="middle" font-size="10" fill="white">바코드/QR</text>
  <text x="455" y="128" text-anchor="middle" font-size="9" fill="white">(자재/WIP 추적)</text>
  <rect x="550" y="90" width="150" height="50" rx="6" fill="#e67e22"/>
  <text x="625" y="112" text-anchor="middle" font-size="10" fill="white">PLC/설비 연동</text>
  <text x="625" y="128" text-anchor="middle" font-size="9" fill="white">(OPC-UA)</text>
  <rect x="720" y="90" width="150" height="50" rx="6" fill="#e67e22"/>
  <text x="795" y="112" text-anchor="middle" font-size="10" fill="white">안돈 디스플레이</text>
  <text x="795" y="128" text-anchor="middle" font-size="9" fill="white">(현장 대시보드)</text>

  <!-- Gateway -->
  <rect x="250" y="180" width="400" height="40" rx="8" fill="#f39c12"/>
  <text x="450" y="205" text-anchor="middle" font-size="13" fill="white" font-weight="bold">IoT Gateway (MQTT + Edge Processing)</text>

  <!-- Cloud -->
  <rect x="50" y="245" width="800" height="120" rx="10" fill="#ecf0f1" stroke="#bdc3c7"/>
  <text x="80" y="270" font-size="13" font-weight="bold" fill="#2c3e50">클라우드 MES 플랫폼</text>
  <rect x="70" y="285" width="145" height="65" rx="8" fill="#2980b9"/>
  <text x="142" y="310" text-anchor="middle" font-size="10" fill="white">생산 관리</text>
  <text x="142" y="330" text-anchor="middle" font-size="9" fill="white">계획/실적/지시</text>
  <rect x="230" y="285" width="145" height="65" rx="8" fill="#2980b9"/>
  <text x="302" y="310" text-anchor="middle" font-size="10" fill="white">품질 관리</text>
  <text x="302" y="330" text-anchor="middle" font-size="9" fill="white">SPC/불량추적</text>
  <rect x="390" y="285" width="145" height="65" rx="8" fill="#2980b9"/>
  <text x="462" y="310" text-anchor="middle" font-size="10" fill="white">설비 관리</text>
  <text x="462" y="330" text-anchor="middle" font-size="9" fill="white">OEE/예지보전</text>
  <rect x="550" y="285" width="145" height="65" rx="8" fill="#2980b9"/>
  <text x="622" y="310" text-anchor="middle" font-size="10" fill="white">재고/물류</text>
  <text x="622" y="330" text-anchor="middle" font-size="9" fill="white">입출고/WIP</text>
  <rect x="710" y="285" width="125" height="65" rx="8" fill="#2980b9"/>
  <text x="772" y="310" text-anchor="middle" font-size="10" fill="white">AI 분석</text>
  <text x="772" y="330" text-anchor="middle" font-size="9" fill="white">품질예측/최적화</text>

  <!-- Output -->
  <rect x="50" y="395" width="800" height="70" rx="10" fill="#ecf0f1" stroke="#bdc3c7"/>
  <text x="80" y="420" font-size="13" font-weight="bold" fill="#2c3e50">사용자 인터페이스</text>
  <rect x="70" y="430" width="170" height="28" rx="5" fill="#8e44ad"/>
  <text x="155" y="449" text-anchor="middle" font-size="10" fill="white">경영자 대시보드 (웹)</text>
  <rect x="260" y="430" width="170" height="28" rx="5" fill="#8e44ad"/>
  <text x="345" y="449" text-anchor="middle" font-size="10" fill="white">공장장 모바일 앱</text>
  <rect x="450" y="430" width="170" height="28" rx="5" fill="#8e44ad"/>
  <text x="535" y="449" text-anchor="middle" font-size="10" fill="white">작업자 태블릿</text>
  <rect x="640" y="430" width="190" height="28" rx="5" fill="#8e44ad"/>
  <text x="735" y="449" text-anchor="middle" font-size="10" fill="white">ERP 연동 API</text>

  <!-- Infra -->
  <rect x="250" y="485" width="400" height="28" rx="6" fill="#34495e"/>
  <text x="450" y="504" text-anchor="middle" font-size="11" fill="white">AWS (ECS + RDS + IoT Core + SageMaker)</text>
</svg>
```

### 4.3 기술 스택

| 구분 | 기술 | 용도 |
|------|------|------|
| IoT | MQTT + AWS IoT Core | 센서 데이터 수집/전송 |
| IoT | OPC-UA | 기존 PLC/설비 연동 |
| Edge | Raspberry Pi 4 + LoRa | 현장 게이트웨이, 무선 센서 통신 |
| AI/ML | XGBoost + LSTM | 불량 예측, 설비 이상 감지 |
| Backend | Node.js + NestJS | 클라우드 MES API |
| Frontend | React + Ant Design Pro | 웹 대시보드, 관리 화면 |
| Mobile | React Native | 태블릿/모바일 현장 앱 |
| Database | PostgreSQL + TimescaleDB | 생산/품질 데이터 + 시계열 센서 데이터 |
| Infra | AWS ECS + RDS | 컨테이너 서비스, 관리형 DB |
| BI | Apache Superset | 리포트, 데이터 시각화 |

---

## 5. 시장 분석

### 5.1 시장 규모 (TAM/SAM/SOM)

| 구분 | 시장 | 규모 | 산출 근거 |
|------|------|------|-----------|
| TAM | 국내 스마트팩토리 시장 | 18.5조원 | 과기정통부 (2025) |
| SAM | 중소 제조업 MES 시장 | 1.2조원 | 스마트제조혁신추진단 (2025) |
| SOM | 클라우드 MES 초기 시장 | 600억원 | SAM x 클라우드 비중 5% |

### 5.2 경쟁사 비교

| 경쟁사 | 유형 | 가격 | 구축 기간 | 한계 |
|--------|------|------|-----------|------|
| 지멘스 MES | 온프레미스 | 5억원+ | 12개월+ | 중소기업 비용 부담 |
| 미라콤 | 온프레미스 | 3억원+ | 6~9개월 | 고가, 커스터마이징 필수 |
| 포스코ICT | SaaS | 월 200만원+ | 2~3개월 | 대기업 지향 |
| 팩토리올 (한국) | SaaS | 월 100만원 | 1개월 | 기능 제한적 |
| 팩토리원 (자사) | 클라우드 SaaS | 월 50만원~ | 3일 | 중소 제조업 특화 |

---

## 6. 비즈니스 모델

### 6.1 수익 구조

| 수익원 | 대상 | 가격 | 비율(3차년도) |
|--------|------|------|---------------|
| SaaS 구독 (라이트) | 10인 미만 | 월 50만원 | 20% |
| SaaS 구독 (스탠다드) | 10~50인 | 월 120만원 | 35% |
| SaaS 구독 (프로) | 50~100인 | 월 250만원 | 20% |
| IoT 센서 패키지 | 전 고객 | 세트당 200~500만원 | 15% |
| 초기 구축/교육 | 전 고객 | 200~500만원 | 5% |
| 정부 지원 사업 | 중기부 연계 | 프로젝트 기반 | 5% |

### 6.2 3개년 재무 계획

| 구분 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 매출액 | 3억원 | 12억원 | 38억원 |
| SaaS 구독 | 1.5억원 | 7.5억원 | 28.5억원 |
| 하드웨어/구축 | 1.2억원 | 3.5억원 | 7.6억원 |
| 기타 | 0.3억원 | 1억원 | 1.9억원 |
| 영업비용 | 4.5억원 | 9억원 | 22억원 |
| 영업이익 | -1.5억원 | 3억원 | 16억원 |
| 도입 공장 수 | 20개 | 80개 | 300개 |
| MRR | 2,500만원 | 1억원 | 3.2억원 |

---

## 7. 실행 계획

### 7.1 개발 로드맵

| 단계 | 기간 | 주요 과업 | 성과 지표 |
|------|------|-----------|-----------|
| Phase 1: MVP 검증 | 2026 Q1~Q2 | 생산관리/품질 모듈, IoT 센서 v1, 파일럿 | 파일럿 5곳, NPS 50+ |
| Phase 2: 상용화 | 2026 Q3~Q4 | SaaS 정식 출시, 설비관리 모듈, 센서 양산 | 20개 공장, MRR 2,500만원 |
| Phase 3: 고도화 | 2027 Q1~Q2 | AI 품질 예측, ERP 연동, 정부사업 파트너 | 80개 공장, AI 불량예측 도입 |
| Phase 4: 확장 | 2027 Q3~2028 | 업종별 템플릿, 동남아 진출 | 300개 공장, 해외 진출 |

### 7.2 팀 구성

| 직무 | 인원 | 역할 | 현재 상태 |
|------|------|------|-----------|
| CEO / PM | 1명 | 전략, 제조업체 영업, 파트너십 | 확보 |
| 백엔드 개발자 | 1명 | MES 코어 시스템, API | 확보 |
| IoT 개발자 | 1명 | 센서 펌웨어, 게이트웨이 | 확보 |
| 제조 공정 전문가 | 1명 | 현장 프로세스 설계, 구축 지원 | 확보 |
| 프론트엔드 개발자 | 1명 | 대시보드, 태블릿 앱 | 확보 |
| 영업/CS | 1명 | 고객 영업, 온보딩, 지원 | 채용 예정 (Q3) |

---

## 8. 리스크 관리

| 리스크 | 영향도 | 발생확률 | 대응전략 |
|--------|--------|----------|----------|
| 현장 직원 디지털 역량 부족 | 중 | 상 | 극도로 쉬운 태블릿 UX, 현장 교육, 영상 매뉴얼 |
| 다양한 제조 공정 대응 | 중 | 상 | 업종별 템플릿, 유연한 설정 체계, 커스텀 필드 |
| IoT 센서 내구성 (공장 환경) | 중 | 중 | IP67 방진방수, 내열 설계, 1년 무상 A/S |
| 기존 ERP와의 연동 | 중 | 중 | 주요 ERP(더존, 영림원) 커넥터 사전 개발 |
| 정부 지원 정책 변경 | 중 | 중 | 자체 고객 확보 병행, 정책 의존도 낮추기 |
| 데이터 보안 (제조 데이터) | 상 | 하 | 데이터 암호화, 접근 제어, ISMS 인증 |

---

## 9. 사회적 가치

### 9.1 중소 제조업 경쟁력 강화
- 대기업 수준의 생산 관리 시스템을 중소기업도 사용 가능
- 데이터 기반 의사결정으로 생산성/품질 향상
- 글로벌 공급망 참여를 위한 디지털 역량 확보

### 9.2 제조 일자리 질 개선
- 반복적 수기 기록 업무 자동화로 부가가치 업무 집중
- 안전 사고 예방 (설비 이상 사전 감지)
- 데이터 활용 역량 교육으로 숙련 인력 양성

### 9.3 국가 제조업 DX 촉진
- 정부 스마트팩토리 보급 확산 정책 이행 지원
- 중소기업 제조 데이터 축적으로 산업 분석 기반 구축
- 탄소 배출 모니터링으로 환경 규제 대응 지원

---

## 10. 자금 운용 계획

| 항목 | 금액(만원) | 비율 | 세부 내역 |
|------|-----------|------|-----------|
| MES 플랫폼 개발 | 2,500 | 25% | 생산/품질/설비 관리 모듈, 대시보드 |
| IoT 하드웨어 개발 | 2,000 | 20% | 센서, 게이트웨이, 펌웨어, 금형 |
| 인건비 | 2,500 | 25% | 개발팀 + 제조 전문가 인건비 |
| 인프라/서버 | 800 | 8% | AWS 클라우드, IoT Core, DB |
| 파일럿/구축 | 700 | 7% | 파일럿 공장 구축, 현장 테스트 |
| 마케팅/영업 | 800 | 8% | 제조업체 영업, 전시회, 콘텐츠 |
| 법률/인증 | 400 | 4% | 특허, ISMS, 정부사업 신청 |
| 예비비 | 300 | 3% | 예비 운영비 |
| **합계** | **10,000** | **100%** | |

---

## 11. 기대 효과

### 11.1 정량적 기대 효과

| 지표 | 현재 | 도입 후 목표 | 개선율 |
|------|------|-------------|--------|
| MES 도입 비용 | 3~5억원 | 1,500만원(연간) | -95% |
| 구축 기간 | 6~12개월 | 3일 | -99% |
| 불량률 | 3.8% | 1.5% | -61% |
| 설비 가동률 (OEE) | 62.3% | 78% | +25% |
| 납기 준수율 | 84.8% | 96% | +13% |
| 재고 정확도 | 72% | 98% | +36% |
| 생산 관리 인력 공수 | 월 80시간 | 20시간 | -75% |
| 수기 기록 비율 | 67.4% | 5% | -93% |

### 11.2 정성적 기대 효과

- **중소 제조업 디지털 혁신**: 비용/기술 장벽 없이 스마트팩토리 실현
- **생산 가시성 확보**: 실시간 데이터로 현장 상황 정확 파악
- **품질 경쟁력 강화**: AI 기반 불량 예측으로 불량률 획기적 감소
- **경영 의사결정 고도화**: 데이터 기반 생산 계획, 원가 분석
- **공급망 신뢰성 향상**: 정확한 납기 관리로 대기업 공급망 참여 확대
- **제조 인력 역량 향상**: 디지털 도구 활용 능력 배양

---

> **팩토리원**은 중소 제조업의 디지털 전환을 가장 쉽고 빠르게 실현하여, 대한민국 제조업의 저력을 혁신합니다.
