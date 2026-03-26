# 스마트 정밀농업 IoT 플랫폼 (FarmSense)

## 2026년 예비창업패키지 사업계획서

---

## 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | FarmSense (팜센스) |
| 대표자 | 윤지호 (컴퓨터공학과 4학년) |
| 팀 구성 | 대표 1명, 개발 2명, 하드웨어/IoT 1명, 운영 1명 (총 5명) |
| 창업 아이템 | IoT 센서와 AI 분석 기반 정밀농업 통합 관리 플랫폼 |
| 아이템 분야 | 농업기술(AgriTech) / IoT / 인공지능 |
| 사업화 단계 | 프로토타입 완료 → 농가 실증 테스트 단계 |
| 목표 시장 | 시설원예 농가, 노지 재배 농가, 스마트팜 운영 법인, 지자체 농업기술센터 |
| 신청 금액 | 1억원 |

> **"데이터로 키우는 농업, 경험을 넘어서는 정밀함 - FarmSense"**

---

## 창업 아이템 개요

| 항목 | 내용 |
|------|------|
| 아이템명 | FarmSense - IoT 기반 정밀농업 통합 관리 플랫폼 |
| 핵심 기술 | IoT 환경 센서, AI 작물 성장 예측, 자동 관수/시비 제어, 위성/드론 영상 분석 |
| 목표 고객 | 시설원예 농가 (딸기, 토마토, 파프리카), 노지 농가, 영농조합, 농업기술센터 |
| 문제 정의 | 농업 종사자 고령화율 65%, 스마트팜 도입률 7.3%, 기상 이변으로 연 2.1조원 농업 피해 |
| 솔루션 | 저비용 IoT 센서 + AI 분석으로 누구나 쉽게 사용 가능한 정밀농업 플랫폼 |
| 비즈니스 모델 | 하드웨어 판매 + SaaS 구독 + 데이터 분석 컨설팅 |
| 시장 규모 | 글로벌 정밀농업 시장 2026년 127억 달러, 국내 스마트농업 시장 4,500억원 |
| 경쟁 우위 | 기존 대비 1/3 비용, 고령 농민도 사용 가능한 UX, 지역 작물 특화 AI |
| 수익 목표 | 1차년도 2.5억원, 2차년도 9억원, 3차년도 27억원 |

---

## 1. 문제 인식

### 한국 농업의 구조적 위기

| 지표 | 수치 | 출처 |
|------|------|------|
| 농업 종사자 평균 연령 | 67.2세 | 통계청 |
| 65세 이상 농업인 비율 | 65% | 농림축산식품부 |
| 스마트팜 도입률 | 7.3% (시설원예 기준) | 농촌진흥청 |
| 기상이변 농업 피해액 | 연 2.1조원 (2025년) | 농림축산식품부 |
| 기존 스마트팜 도입 비용 | 3.3ha 기준 3~5억원 | 한국농업기술진흥원 |
| 농가 소득 대비 도입비 비율 | 소득의 8~15배 | 농촌경제연구원 |
| 관수/시비 비효율 | 과잉 관수 35%, 과잉 시비 28% | 국립농업과학원 |
| 농약 과다 사용률 | OECD 평균 대비 2.3배 | OECD |
| 농업 데이터 활용률 | 12% | 과기정통부 |
| 귀농·귀촌 실패율 (3년 내) | 38% | 귀농귀촌종합센터 |

한국 농업은 고령화와 인력 부족, 기후 변화로 인한 생산성 위기에 직면해 있다. 스마트팜이 해결책으로 제시되지만, 기존 시스템은 도입 비용이 3~5억원으로 개인 농가가 감당하기 어렵고, 복잡한 인터페이스는 고령 농업인이 사용하기 어렵다.

### 사용자 페인 포인트 분석

**시설원예 농가:**
> *"스마트팜이 좋다고 들었지만, 설치 비용이 3억원이라니 엄두가 안 납니다. 저 같은 소규모 농가도 쓸 수 있는 게 있었으면 합니다."*

> *"온도, 습도를 매번 직접 확인하러 하우스에 가야 합니다. 밤에도 서리가 올까 봐 잠을 못 잡니다."*

**귀농 청년:**
> *"농사 경험이 없어서 언제 물을 주고 비료를 줘야 하는지 모르겠습니다. 데이터 기반으로 가이드해주는 시스템이 있으면 좋겠어요."*

**농업기술센터 담당자:**
> *"관내 농가에 스마트팜을 보급하고 싶지만, 예산이 한정적입니다. 저렴하면서도 효과적인 솔루션이 필요합니다."*

### 스마트농업 도입 장벽 분석

```svg
<svg viewBox="0 0 800 400" xmlns="http://www.w3.org/2000/svg">
  <rect width="800" height="400" fill="#f8f9fa" rx="10"/>
  <text x="400" y="30" text-anchor="middle" font-size="16" font-weight="bold" fill="#333">스마트팜 미도입 이유 설문 (N=1,200 농가)</text>

  <!-- Horizontal bars -->
  <text x="190" y="75" text-anchor="end" font-size="11" fill="#333">도입 비용이 너무 높음</text>
  <rect x="200" y="60" width="440" height="30" fill="#e74c3c" opacity="0.8" rx="4"/>
  <text x="650" y="80" font-size="11" fill="#333">44%</text>

  <text x="190" y="120" text-anchor="end" font-size="11" fill="#333">사용법이 어려움</text>
  <rect x="200" y="105" width="280" height="30" fill="#f39c12" opacity="0.8" rx="4"/>
  <text x="490" y="125" font-size="11" fill="#333">28%</text>

  <text x="190" y="165" text-anchor="end" font-size="11" fill="#333">효과를 모르겠음</text>
  <rect x="200" y="150" width="140" height="30" fill="#3498db" opacity="0.8" rx="4"/>
  <text x="350" y="170" font-size="11" fill="#333">14%</text>

  <text x="190" y="210" text-anchor="end" font-size="11" fill="#333">A/S가 걱정됨</text>
  <rect x="200" y="195" width="80" height="30" fill="#27ae60" opacity="0.8" rx="4"/>
  <text x="290" y="215" font-size="11" fill="#333">8%</text>

  <text x="190" y="255" text-anchor="end" font-size="11" fill="#333">기타</text>
  <rect x="200" y="240" width="60" height="30" fill="#8e44ad" opacity="0.8" rx="4"/>
  <text x="270" y="260" font-size="11" fill="#333">6%</text>

  <!-- FarmSense solution -->
  <rect x="150" y="300" width="500" height="60" fill="#27ae60" rx="8" opacity="0.9"/>
  <text x="400" y="325" text-anchor="middle" font-size="13" fill="#fff" font-weight="bold">FarmSense 해결 전략</text>
  <text x="400" y="348" text-anchor="middle" font-size="11" fill="#fff">비용 1/3 절감 | 고령자 UX | 수확량 25%+ 증가 실증 데이터 제공</text>
</svg>
```

---

## 2. 솔루션

### 핵심 기능

| 기능 | 설명 | 기술 요소 |
|------|------|-----------|
| IoT 환경 모니터링 | 온도, 습도, 토양수분, CO2, 일조량 실시간 측정 | LoRaWAN 센서 + MQTT |
| AI 작물 성장 예측 | 환경 데이터 기반 성장 단계, 수확 시기 예측 | LSTM 시계열 예측 모델 |
| 자동 관수/시비 제어 | AI 분석 기반 최적 관수량, 시비량 자동 제어 | PID 제어 + 강화학습 |
| 병해충 조기 경보 | 환경 조건 기반 병해충 발생 확률 예측 | 기상 데이터 + 감염 확산 모델 |
| 위성/드론 영상 분석 | 작물 생육 상태, 스트레스 맵 생성 | NDVI 분석, 위성영상 AI |
| 음성 인터페이스 | 고령 농민을 위한 음성 명령/알림 | STT/TTS, 음성 챗봇 |
| 기상 연동 자동 대응 | 한파/폭염/폭우 예보 시 자동 방어 조치 | 기상청 API + 자동 제어 |
| 농작업 일지 자동 생성 | GAP/친환경 인증용 농작업 기록 자동화 | 센서 데이터 기반 자동 기록 |

### 시스템 아키텍처

```svg
<svg viewBox="0 0 900 500" xmlns="http://www.w3.org/2000/svg">
  <rect width="900" height="500" fill="#f0f4f8" rx="10"/>
  <text x="450" y="30" text-anchor="middle" font-size="16" font-weight="bold" fill="#2c3e50">FarmSense 시스템 아키텍처</text>

  <!-- Sensors -->
  <rect x="30" y="60" width="160" height="180" fill="#27ae60" rx="8"/>
  <text x="110" y="85" text-anchor="middle" font-size="12" fill="#fff" font-weight="bold">IoT 센서 레이어</text>
  <text x="110" y="110" text-anchor="middle" font-size="10" fill="#fff">온습도 센서</text>
  <text x="110" y="130" text-anchor="middle" font-size="10" fill="#fff">토양수분/EC 센서</text>
  <text x="110" y="150" text-anchor="middle" font-size="10" fill="#fff">CO2 센서</text>
  <text x="110" y="170" text-anchor="middle" font-size="10" fill="#fff">일사량 센서</text>
  <text x="110" y="190" text-anchor="middle" font-size="10" fill="#fff">카메라 (작물 촬영)</text>
  <text x="110" y="210" text-anchor="middle" font-size="10" fill="#fff">기상 관측 장비</text>
  <text x="110" y="228" text-anchor="middle" font-size="9" fill="#fff">LoRaWAN 게이트웨이</text>

  <!-- Edge -->
  <rect x="240" y="80" width="150" height="70" fill="#f39c12" rx="8"/>
  <text x="315" y="105" text-anchor="middle" font-size="11" fill="#fff">엣지 게이트웨이</text>
  <text x="315" y="125" text-anchor="middle" font-size="10" fill="#fff">로컬 처리 / 버퍼링</text>
  <text x="315" y="140" text-anchor="middle" font-size="9" fill="#fff">Raspberry Pi 4</text>

  <rect x="240" y="170" width="150" height="70" fill="#f39c12" rx="8"/>
  <text x="315" y="195" text-anchor="middle" font-size="11" fill="#fff">제어 모듈</text>
  <text x="315" y="215" text-anchor="middle" font-size="10" fill="#fff">관수밸브/환기장치</text>
  <text x="315" y="230" text-anchor="middle" font-size="9" fill="#fff">릴레이 제어</text>

  <!-- Cloud -->
  <rect x="440" y="55" width="230" height="200" fill="#3498db" rx="10"/>
  <text x="555" y="80" text-anchor="middle" font-size="12" fill="#fff" font-weight="bold">클라우드 플랫폼</text>
  <rect x="455" y="95" width="200" height="28" fill="#2980b9" rx="5"/>
  <text x="555" y="114" text-anchor="middle" font-size="10" fill="#fff">데이터 수집 (Kafka + InfluxDB)</text>
  <rect x="455" y="130" width="200" height="28" fill="#2980b9" rx="5"/>
  <text x="555" y="149" text-anchor="middle" font-size="10" fill="#fff">AI 작물 성장 예측 모델</text>
  <rect x="455" y="165" width="200" height="28" fill="#2980b9" rx="5"/>
  <text x="555" y="184" text-anchor="middle" font-size="10" fill="#fff">자동 제어 최적화 엔진</text>
  <rect x="455" y="200" width="200" height="28" fill="#2980b9" rx="5"/>
  <text x="555" y="219" text-anchor="middle" font-size="10" fill="#fff">병해충 예경보 시스템</text>
  <rect x="455" y="235" width="200" height="12" fill="#2980b9" rx="3"/>
  <text x="555" y="245" text-anchor="middle" font-size="8" fill="#fff">위성영상 NDVI 분석</text>

  <!-- User Interface -->
  <rect x="720" y="70" width="150" height="60" fill="#e74c3c" rx="8"/>
  <text x="795" y="95" text-anchor="middle" font-size="11" fill="#fff">모바일 앱</text>
  <text x="795" y="115" text-anchor="middle" font-size="10" fill="#fff">음성 인터페이스</text>

  <rect x="720" y="150" width="150" height="60" fill="#e74c3c" rx="8"/>
  <text x="795" y="175" text-anchor="middle" font-size="11" fill="#fff">웹 대시보드</text>
  <text x="795" y="195" text-anchor="middle" font-size="10" fill="#fff">통계/리포트</text>

  <!-- External -->
  <rect x="200" y="340" width="160" height="50" fill="#8e44ad" rx="8"/>
  <text x="280" y="362" text-anchor="middle" font-size="10" fill="#fff">기상청 API</text>
  <text x="280" y="380" text-anchor="middle" font-size="10" fill="#fff">초단기/중기 예보</text>

  <rect x="400" y="340" width="160" height="50" fill="#8e44ad" rx="8"/>
  <text x="480" y="362" text-anchor="middle" font-size="10" fill="#fff">농촌진흥청 DB</text>
  <text x="480" y="380" text-anchor="middle" font-size="10" fill="#fff">작물 생육 정보</text>

  <rect x="600" y="340" width="160" height="50" fill="#8e44ad" rx="8"/>
  <text x="680" y="362" text-anchor="middle" font-size="10" fill="#fff">위성 영상</text>
  <text x="680" y="380" text-anchor="middle" font-size="10" fill="#fff">Sentinel-2 / 아리랑</text>

  <!-- Arrows -->
  <line x1="190" y1="150" x2="238" y2="115" stroke="#333" stroke-width="2" marker-end="url(#ar81)"/>
  <line x1="390" y1="115" x2="438" y2="130" stroke="#333" stroke-width="2" marker-end="url(#ar81)"/>
  <line x1="670" y1="140" x2="718" y2="110" stroke="#333" stroke-width="2" marker-end="url(#ar81)"/>
  <line x1="670" y1="180" x2="718" y2="180" stroke="#333" stroke-width="2" marker-end="url(#ar81)"/>

  <defs>
    <marker id="ar81" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#333"/>
    </marker>
  </defs>
</svg>
```

### 기술 스택

| 구분 | 기술 | 선정 이유 |
|------|------|-----------|
| IoT 통신 | LoRaWAN + MQTT | 장거리 저전력 농업 환경 최적 통신 |
| 엣지 컴퓨팅 | Raspberry Pi 4 + TFLite | 현장 로컬 처리, 네트워크 끊김 대응 |
| 시계열 DB | InfluxDB + TimescaleDB | 센서 데이터 고속 저장/조회 |
| AI/ML | PyTorch (LSTM, XGBoost) | 작물 성장 예측, 관수 최적화 |
| 위성영상 | Rasterio + GDAL | NDVI 등 식생지수 분석 |
| 백엔드 | FastAPI + Celery | 비동기 데이터 처리, 제어 명령 |
| 프론트엔드 | React Native | 모바일 우선 크로스플랫폼 |
| 음성 인터페이스 | Whisper + gTTS | 고령 농민 음성 명령/알림 |
| 인프라 | AWS IoT Core + Greengrass | IoT 디바이스 관리, 엣지 배포 |

---

## 3. 시장 분석

### TAM-SAM-SOM

| 구분 | 규모 | 산출 근거 |
|------|------|-----------|
| TAM | 127억 달러 (약 16.5조원) | 글로벌 정밀농업 시장 (2026년, MarketsandMarkets) |
| SAM | 4,500억원 | 국내 스마트팜 및 농업 IoT 시장 |
| SOM | 45억원 | 1차년도 시설원예 500농가 + 지자체 5곳 (점유율 1%) |

### 경쟁사 분석

| 구분 | FarmSense (자사) | 그린랩스 | 팜에어 | 엔씽 | John Deere |
|------|------------------|---------|--------|------|-----------|
| 도입 비용 (1,000평) | 300만원 | 1,000만원 | 500만원 | 2,000만원+ | 5,000만원+ |
| 고령자 UX | 음성 인터페이스 | 보통 | 보통 | 어려움 | 어려움 |
| 자동 제어 | O | O | 부분 | O | O |
| AI 예측 | O | O | 부분 | O | O |
| 위성영상 분석 | O | X | O | X | O |
| 노지 농업 지원 | O | 시설만 | O | 시설만 | O |
| 로컬 서비스 | O | O | O | O | X |
| 작물 특화 | 한국 작물 | 한국 작물 | 일반 | 엽채류 | 곡물 |

---

## 4. 비즈니스 모델

### 수익 구조

| 수익원 | 단가 | 목표 규모 | 연매출 |
|--------|------|-----------|--------|
| IoT 센서 패키지 판매 | 1세트 300만원 | 200세트 | 6억원 |
| SaaS 월 구독 | 월 5만원/농가 | 500농가 | 3억원 |
| 지자체 통합 관제 | 건당 5,000만원 | 5곳 | 2.5억원 |
| 데이터 컨설팅 | 건당 500만원 | 20건 | 1억원 |
| 센서 유지보수 | 연 50만원/농가 | 300농가 | 1.5억원 |

### 3개년 재무 계획

| 항목 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 매출액 | 2.5억원 | 9억원 | 27억원 |
| 영업비용 | 4.5억원 | 7.5억원 | 16억원 |
| 인건비 | 2.5억원 | 4.5억원 | 8억원 |
| 하드웨어 원가 | 1억원 | 2억원 | 4억원 |
| 서버/인프라 | 0.5억원 | 1억원 | 2.5억원 |
| 영업이익 | -2억원 | 1.5억원 | 11억원 |
| 누적 손익 | -2억원 | -0.5억원 | 10.5억원 |

---

## 5. 실행 계획

### 개발 로드맵

| 단계 | 기간 | 주요 과업 | 산출물 |
|------|------|-----------|--------|
| 1단계: 하드웨어 양산 | 2026.04 - 2026.07 | IoT 센서 모듈 양산 설계, AI 모델 개발 | 센서 키트 v1.0, AI 모델 |
| 2단계: 플랫폼 개발 | 2026.07 - 2026.10 | 클라우드 플랫폼, 앱, 자동 제어 시스템 | 앱 베타, 대시보드 |
| 3단계: 현장 실증 | 2026.10 - 2027.01 | 5개 지역 50농가 실증 (딸기, 토마토 등) | 실증 결과, 수확량 데이터 |
| 4단계: 상용화 | 2027.01 - 2027.04 | 정식 판매, 지자체 사업 수주, 마케팅 | 500농가 보급, 지자체 5곳 |

### 팀 구성

| 역할 | 이름 | 전공/경력 | 담당 업무 |
|------|------|-----------|-----------|
| 대표/PM | 윤지호 | 컴퓨터공학 4학년, IoT 연구실 | 프로젝트 총괄, 시스템 설계 |
| AI 개발 | 강서윤 | AI학과 석사, 시계열 예측 연구 | 작물 성장 예측, 병해충 모델 |
| 백엔드/IoT | 임태호 | 전자공학과 4학년, 임베디드 개발 | IoT 펌웨어, 엣지 컴퓨팅, API |
| 프론트/앱 | 서지민 | 소프트웨어학과 3학년 | 모바일 앱, 대시보드, UX |
| 농업 도메인/운영 | 배수현 | 원예학과 4학년, 농업기술센터 인턴 | 농업 도메인, 현장 지원, 영업 |

---

## 6. 리스크 관리

| 리스크 | 영향도 | 발생확률 | 대응 전략 |
|--------|--------|----------|-----------|
| IoT 센서 고장/열화 | 중 | 상 | IP67 방수 설계, 자가진단 기능, 교체 보증 |
| 농업 현장 통신 환경 불안정 | 중 | 중 | LoRaWAN 장거리 통신, 엣지 로컬 버퍼링 |
| 고령 농민 기술 수용 저항 | 상 | 상 | 음성 인터페이스, 현장 교육, 24시간 전화 지원 |
| 작물별 AI 모델 정확도 편차 | 중 | 중 | 작물별 데이터 확충, 농진청 협력 검증 |
| 대기업/정부 주도 스마트팜 확대 | 상 | 중 | 소규모 농가 특화 포지셔닝, 보조금 사업 참여 |
| 기후 변화로 인한 예측 모델 편향 | 중 | 중 | 실시간 데이터 반영 적응형 모델, 모델 월간 재학습 |

---

## 7. 사회적 가치

### 농업의 지속가능성과 식량 안보

FarmSense는 데이터 기반 정밀농업으로 농업 생산성을 높이고, 물과 비료 사용량을 절감하여 환경을 보호한다. 고령 농업인의 노동 부담을 줄이고, 귀농 청년의 성공적 정착을 지원한다.

| 사회적 가치 지표 | 목표 |
|-----------------|------|
| 작물 수확량 증가 | 평균 25% 향상 |
| 물 사용량 절감 | 평균 30% 절감 |
| 비료/농약 사용 감소 | 평균 20% 절감 |
| 농업인 노동시간 감소 | 주당 15시간 절감 |
| 귀농 청년 정착 지원 | 100가구 |
| 농가 소득 증가 | 평균 월 50만원 증가 |

---

## 8. 자금 운용 계획

| 항목 | 금액(만원) | 비율 | 용도 |
|------|-----------|------|------|
| 인건비 | 3,000 | 30% | 개발팀 5명 인건비 (10개월) |
| IoT 하드웨어 | 2,500 | 25% | 센서 모듈 금형, 부품, 조립, PCB 제작 |
| AI/서버 | 1,500 | 15% | 클라우드 서버, AI 모델 학습 GPU |
| 현장 실증 | 1,200 | 12% | 5개 지역 실증 운영비, 설치비, 출장비 |
| 연구개발 | 800 | 8% | 센서 캘리브레이션, 농진청 공동 연구 |
| 마케팅/영업 | 500 | 5% | 농업 박람회, 지자체 사업 제안, 홍보 |
| 운영비 | 500 | 5% | 사무실, 장비, 기타 |
| **합계** | **10,000** | **100%** | |

---

## 9. 기대 효과

### 정량적 기대 효과

| 지표 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 보급 농가 수 | 200농가 | 800농가 | 2,500농가 |
| 지자체 도입 수 | 5곳 | 15곳 | 40곳 |
| 평균 수확량 증가 | 15% | 20% | 25% |
| 물 절감량 | 총 1,000톤 | 5,000톤 | 15,000톤 |
| 매출액 | 2.5억원 | 9억원 | 27억원 |
| 고용 인원 | 5명 | 15명 | 35명 |

### 정성적 기대 효과

- **농업 디지털 전환**: 데이터 기반 의사결정으로 경험 의존적 농업에서 과학적 농업으로 전환
- **농업인 삶의 질 향상**: 자동화로 야간 순찰, 반복 노동 부담 해소
- **식량 안보 기여**: 기후변화 적응형 농업으로 안정적 식량 생산 기반 마련
- **환경 보호**: 물, 비료, 농약 사용 최적화로 토양/수질 오염 감소
- **귀농 촉진**: 초보 농업인도 AI 가이드로 성공적 영농 가능
- **농업 데이터 생태계 구축**: 농업 빅데이터 축적으로 국가 농업 정책 수립 지원
