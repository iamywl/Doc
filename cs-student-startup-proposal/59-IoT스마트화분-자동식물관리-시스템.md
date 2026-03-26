# IoT 스마트 화분 자동 식물 관리 시스템 (PlantMate)

## 2026년 예비창업패키지 사업계획서

---

### 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | 플랜트메이트(PlantMate) |
| 대표자 | 박식물 |
| 팀 구성 | 4인 (IoT 개발 1, AI 개발 1, 앱 개발 1, 원예학/디자인 1) |
| 창업 아이템 | IoT 센서 기반 자동 식물 관리 및 AI 식물 진단 시스템 |
| 아이템 분야 | IoT / AgriTech |
| 사업화 단계 | 시제품 개발 완료 |
| 목표 시장 | 1인가구 식물 애호가, 사무실/카페, 식물 초보자 |
| 신청 금액 | 1억원 |

> **"식물 초보도 식물 장인으로, 기술이 초록빛 일상을 선물합니다."**

---

### 창업 아이템 개요

| 항목 | 내용 |
|------|------|
| 아이템명 | PlantMate - IoT 스마트 화분 자동 식물 관리 시스템 |
| 핵심 기술 | 토양 수분/광량/온도 센서, 자동 급수, AI 식물 상태 진단 |
| 목표 고객 | 2030 식물 애호가, 1인가구, 사무실/카페 인테리어 관리자 |
| 문제 정의 | 식물 구매 후 3개월 내 고사율 47% (플라워24 조사, 2025), 주요 원인 과습/과건 62% |
| 솔루션 | 다중 센서로 식물 상태 실시간 모니터링 + 자동 급수 + AI 질병 진단 + 커뮤니티 |
| 비즈니스 모델 | 스마트 화분 디바이스 판매 + 프리미엄 앱 구독 + 식물/용품 커머스 |
| 시장 규모 | 국내 반려식물 시장 2.3조원 (2025), 스마트 가드닝 세그먼트 1,500억원 |
| 경쟁 우위 | AI 식물 진단 95% 정확도, 300종+ 식물 DB, 디자인 감각의 하드웨어 |
| 수익 목표 | 3년 내 연매출 28억원 |

---

## 1. 문제 인식

### 반려식물 시장 및 관리 현황

| 지표 | 수치 | 출처 |
|------|------|------|
| 국내 반려식물 보유 가구 | 1,870만 가구 (73.2%) | 농촌진흥청 (2025) |
| 식물 구매 후 3개월 내 고사율 | 47% | 플라워24 조사 (2025) |
| 고사 원인 중 과습/과건 비율 | 62% | 농촌진흥청 (2025) |
| 식물 관리 어려움 호소 비율 | 58.4% | 한국갤럽 (2025) |
| MZ세대 월 식물 관련 지출 | 평균 4.7만원 | 신한카드 트렌드 (2025) |
| 반려식물 관련 앱 이용률 | 12.3% | 앱애니 (2025) |
| 식물 재구매율 (고사 후) | 68.5% | 플라워24 (2025) |
| 플랜테리어 관심도 (2030 기준) | 74.2% | 한국트렌드연구소 (2025) |

### 식물 관리 문제의 심층 분석

코로나19 이후 플랜테리어(Plant+Interior) 트렌드와 함께 반려식물 시장이 급성장했지만, 식물 관리 지식 부족으로 인한 높은 고사율이 지속적인 문제이다. 특히 1인가구와 식물 초보자는 물주기 주기, 광량 관리, 영양분 공급 등 기본적인 관리에 어려움을 겪고 있다.

**핵심 Pain Point 분석:**

1. **물주기의 불확실성**: 식물 종류마다 최적 수분량이 다르며, 계절과 환경에 따라 물주기 주기가 달라진다. "일주일에 한 번"이라는 단순 규칙은 실제 환경에서 과습 또는 과건을 초래한다. 과습은 뿌리 썩음을, 과건은 잎 마름을 유발하며, 이것이 전체 고사의 62%를 차지한다.

2. **광량 관리 실패**: 식물별 필요 조도(Lux)가 크게 다르지만, 대부분의 사용자는 "밝은 곳에 두면 된다" 정도의 지식만 보유한다. 실내에서 창가 직사광선(10,000+ Lux)과 실내 중앙(300 Lux)의 차이가 30배 이상인데, 이를 인지하지 못하는 경우가 많다.

3. **질병/해충 조기 발견 실패**: 잎의 변색, 반점, 처짐 등 초기 증상을 놓치면 치료가 어려워진다. 식물 초보자의 82%가 증상 발견 시 "이미 늦은 상태"라고 응답했다.

4. **출장/여행 시 관리 공백**: 1인가구의 경우 장기 부재 시 식물을 돌볼 수 없어 고사 위험이 급증한다. "여행 갈 때마다 식물이 걱정"이라는 응답이 71.3%에 달한다.

5. **정보 파편화**: 식물 관리 정보가 블로그, 유튜브, 카페 등에 산재되어 있으며, 신뢰성이 검증되지 않은 정보가 많다. 동일 식물에 대해 상반된 관리 조언이 공존하여 혼란을 가중시킨다.

**사용자 인터뷰 주요 발언:**

> "몬스테라를 샀는데 물을 얼마나 줘야 하는지 블로그마다 다르게 나와 있어서 결국 과습으로 죽였어요. 세 번째 도전인데 또 걱정됩니다." -- 1인가구 (29세, 서울 마포구)

> "사무실에 화분이 20개 정도 있는데, 각각 물주기가 다 달라서 관리가 정말 힘들어요. 누가 대신 관리해 주면 좋겠어요." -- 사무실 관리자 (35세, 서울 강남구)

> "출장 다녀오면 식물이 죽어 있는 게 너무 슬퍼요. 자동으로 물 주는 기기가 있으면 좋겠어요." -- 직장인 (32세, 경기 성남시)

---

## 2. 솔루션

### 시스템 아키텍처

<div align="center">
<svg width="900" height="520" xmlns="http://www.w3.org/2000/svg">
  <rect width="900" height="520" fill="#f8f9fa" rx="15"/>
  <text x="450" y="35" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">PlantMate 시스템 아키텍처</text>

  <!-- Smart Pot -->
  <rect x="30" y="60" width="200" height="200" rx="12" fill="#50C878" stroke="#45b56a" stroke-width="2"/>
  <text x="130" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="white">스마트 화분</text>
  <text x="130" y="108" text-anchor="middle" font-size="11" fill="#e8f9ee">토양 수분 센서 (정전식)</text>
  <text x="130" y="128" text-anchor="middle" font-size="11" fill="#e8f9ee">광량 센서 (BH1750)</text>
  <text x="130" y="148" text-anchor="middle" font-size="11" fill="#e8f9ee">온습도 센서 (SHT31)</text>
  <text x="130" y="168" text-anchor="middle" font-size="11" fill="#e8f9ee">EC 센서 (영양분 농도)</text>
  <text x="130" y="188" text-anchor="middle" font-size="11" fill="#e8f9ee">미니 페리스탈틱 펌프</text>
  <text x="130" y="208" text-anchor="middle" font-size="11" fill="#e8f9ee">수위 감지 센서</text>
  <text x="130" y="228" text-anchor="middle" font-size="11" fill="#e8f9ee">E-ink 디스플레이</text>
  <text x="130" y="248" text-anchor="middle" font-size="11" fill="#e8f9ee">ESP32-C3 (BLE + Wi-Fi)</text>

  <!-- Arrow -->
  <line x1="230" y1="160" x2="310" y2="160" stroke="#7f8c8d" stroke-width="2" marker-end="url(#arrowPM)"/>
  <defs><marker id="arrowPM" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto"><polygon points="0 0, 10 3.5, 0 7" fill="#7f8c8d"/></marker></defs>

  <!-- BLE Hub -->
  <rect x="320" y="80" width="200" height="160" rx="12" fill="#4A90D9" stroke="#3a7bc8" stroke-width="2"/>
  <text x="420" y="105" text-anchor="middle" font-size="14" font-weight="bold" fill="white">BLE 허브</text>
  <text x="420" y="125" text-anchor="middle" font-size="12" fill="#e8f0fe">(Wi-Fi 브릿지)</text>
  <text x="420" y="150" text-anchor="middle" font-size="11" fill="#e8f0fe">다중 화분 동시 연결</text>
  <text x="420" y="170" text-anchor="middle" font-size="11" fill="#e8f0fe">(최대 20개)</text>
  <text x="420" y="190" text-anchor="middle" font-size="11" fill="#e8f0fe">OTA 펌웨어 업데이트</text>
  <text x="420" y="210" text-anchor="middle" font-size="11" fill="#e8f0fe">Wi-Fi 인터넷 연결</text>
  <text x="420" y="230" text-anchor="middle" font-size="11" fill="#e8f0fe">로컬 스케줄 백업</text>

  <!-- Arrow -->
  <line x1="520" y1="160" x2="600" y2="160" stroke="#7f8c8d" stroke-width="2" marker-end="url(#arrowPM)"/>

  <!-- Cloud -->
  <rect x="610" y="60" width="260" height="200" rx="12" fill="#9B59B6" stroke="#8e44ad" stroke-width="2"/>
  <text x="740" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="white">클라우드 서버</text>
  <text x="740" y="110" text-anchor="middle" font-size="11" fill="#f0e6f6">식물 DB (300종+)</text>
  <text x="740" y="130" text-anchor="middle" font-size="11" fill="#f0e6f6">AI 진단 엔진 (MobileNetV3)</text>
  <text x="740" y="150" text-anchor="middle" font-size="11" fill="#f0e6f6">맞춤 급수 스케줄 생성</text>
  <text x="740" y="170" text-anchor="middle" font-size="11" fill="#f0e6f6">성장 분석 / 건강 점수</text>
  <text x="740" y="190" text-anchor="middle" font-size="11" fill="#f0e6f6">커뮤니티 서버</text>
  <text x="740" y="210" text-anchor="middle" font-size="11" fill="#f0e6f6">커머스 연동</text>
  <text x="740" y="230" text-anchor="middle" font-size="11" fill="#f0e6f6">Push 알림 엔진</text>
  <text x="740" y="250" text-anchor="middle" font-size="11" fill="#f0e6f6">GPT-4 처방 텍스트 생성</text>

  <!-- Bottom row -->
  <rect x="30" y="320" width="250" height="160" rx="12" fill="#FF6B6B" stroke="#e55d5d" stroke-width="2"/>
  <text x="155" y="345" text-anchor="middle" font-size="14" font-weight="bold" fill="white">모바일 앱</text>
  <text x="155" y="370" text-anchor="middle" font-size="11" fill="#ffe0e0">실시간 식물 상태 모니터링</text>
  <text x="155" y="390" text-anchor="middle" font-size="11" fill="#ffe0e0">급수/환기 알림</text>
  <text x="155" y="410" text-anchor="middle" font-size="11" fill="#ffe0e0">AI 사진 진단 (잎 촬영)</text>
  <text x="155" y="430" text-anchor="middle" font-size="11" fill="#ffe0e0">식물 성장 일지</text>
  <text x="155" y="450" text-anchor="middle" font-size="11" fill="#ffe0e0">커뮤니티 / Q&A</text>
  <text x="155" y="470" text-anchor="middle" font-size="11" fill="#ffe0e0">식물/용품 쇼핑</text>

  <!-- AI Diagnosis -->
  <rect x="320" y="320" width="200" height="160" rx="12" fill="#FFD93D" stroke="#e6c235" stroke-width="2"/>
  <text x="420" y="345" text-anchor="middle" font-size="14" font-weight="bold" fill="#2c3e50">AI 진단 기능</text>
  <text x="420" y="370" text-anchor="middle" font-size="11" fill="#5d4e0e">잎 사진 분석</text>
  <text x="420" y="390" text-anchor="middle" font-size="11" fill="#5d4e0e">질병 12종 탐지</text>
  <text x="420" y="410" text-anchor="middle" font-size="11" fill="#5d4e0e">해충 8종 판별</text>
  <text x="420" y="430" text-anchor="middle" font-size="11" fill="#5d4e0e">영양 결핍 진단</text>
  <text x="420" y="450" text-anchor="middle" font-size="11" fill="#5d4e0e">GPT-4 처방 안내</text>
  <text x="420" y="470" text-anchor="middle" font-size="11" fill="#5d4e0e">진단 정확도: 95%</text>

  <!-- Commerce -->
  <rect x="560" y="320" width="310" height="160" rx="12" fill="#1abc9c" stroke="#16a085" stroke-width="2"/>
  <text x="715" y="345" text-anchor="middle" font-size="14" font-weight="bold" fill="white">커머스 / 커뮤니티</text>
  <text x="715" y="370" text-anchor="middle" font-size="11" fill="#e8f8f5">식물 큐레이션 추천</text>
  <text x="715" y="390" text-anchor="middle" font-size="11" fill="#e8f8f5">흙/비료/화분 용품 판매</text>
  <text x="715" y="410" text-anchor="middle" font-size="11" fill="#e8f8f5">브랜드 콜라보 (카페/인테리어)</text>
  <text x="715" y="430" text-anchor="middle" font-size="11" fill="#e8f8f5">식물 성장 일지 공유</text>
  <text x="715" y="450" text-anchor="middle" font-size="11" fill="#e8f8f5">전문가 Q&A 게시판</text>
  <text x="715" y="470" text-anchor="middle" font-size="11" fill="#e8f8f5">식물 교환/나눔 마켓</text>

  <!-- Connections -->
  <line x1="740" y1="260" x2="420" y2="320" stroke="#7f8c8d" stroke-width="1.5" marker-end="url(#arrowPM)"/>
  <line x1="700" y1="260" x2="155" y2="320" stroke="#7f8c8d" stroke-width="1.5" marker-end="url(#arrowPM)"/>
  <line x1="780" y1="260" x2="715" y2="320" stroke="#7f8c8d" stroke-width="1.5" marker-end="url(#arrowPM)"/>
</svg>
</div>

### 주요 기능 상세

#### 2-1. 다중 센서 모니터링
토양 수분, 광량(Lux), 온도, 습도를 실시간 측정하여 식물별 최적 환경 유지한다.

- **토양 수분**: 정전식 센서로 0~100% 범위 측정, 식물별 최적 수분 범위를 DB에서 참조하여 실시간 비교
- **광량 (Lux)**: BH1750 센서로 0~65,000 Lux 정밀 측정, 일일 광량(DLI) 자동 산출, 위치 이동 권고
- **온습도**: SHT31 센서로 온도(+/-0.3도), 습도(+/-2%) 정밀 측정
- **EC (전기전도도)**: 토양 내 영양분 농도 간접 측정으로 비료 필요 시점 알림

#### 2-2. 자동 급수 시스템
식물 종류별 최적 수분량을 AI가 판단하여 자동 급수, 장기 외출 시에도 안심한다.

- **미니 페리스탈틱 펌프**: 정확한 수량 제어(1ml 단위), 식물별 맞춤 급수량 자동 계산
- **급수 알고리즘**: 토양 수분 + 온도 + 습도 + 계절 + 식물 종류를 종합 판단
- **수위 감지**: 물통 잔량 실시간 모니터링, 부족 시 앱 알림
- **장기 부재 모드**: 여행/출장 시 자동 급수 스케줄 최적화 (최대 30일)

#### 2-3. AI 식물 상태 진단
잎 사진을 촬영하면 AI가 질병, 영양 결핍, 해충 피해 등을 진단하고 처방 안내한다.

- **진단 범위**: 질병 12종(잎마름병, 탄저병, 흰가루병 등), 해충 8종(진딧물, 응애, 깍지벌레 등), 영양 결핍 5종
- **모델 아키텍처**: MobileNetV3 기반 경량 CNN, TensorFlow Lite로 온디바이스 추론도 지원
- **처방 안내**: GPT-4 API를 활용한 자연어 처방 텍스트 생성, 구체적인 조치 방법과 예방법 안내
- **진단 정확도**: Top-1 Accuracy 95%, Top-3 Accuracy 99.2%

#### 2-4. 식물 커뮤니티
식물 성장 일지 공유, 전문가 Q&A, 식물 교환/나눔 게시판 운영한다.

- **성장 타임라인**: 날짜별 사진과 환경 데이터를 자동 기록하여 성장 과정 시각화
- **전문가 답변**: 원예 전문가/식물 인플루언서가 참여하는 Q&A 시스템
- **식물 교환/나눔**: 지역 기반 식물 교환, 삽목 나눔, 분양 게시판

### 서비스 흐름도

<div align="center">
<svg width="850" height="370" xmlns="http://www.w3.org/2000/svg">
  <rect width="850" height="370" fill="#f8f9fa" rx="15"/>
  <text x="425" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">PlantMate 사용자 여정</text>

  <!-- Step 1 -->
  <rect x="30" y="60" width="140" height="90" rx="10" fill="#50C878"/>
  <text x="100" y="90" text-anchor="middle" font-size="11" font-weight="bold" fill="white">스마트 화분</text>
  <text x="100" y="108" text-anchor="middle" font-size="11" font-weight="bold" fill="white">구매 / 설치</text>
  <text x="100" y="128" text-anchor="middle" font-size="9" fill="#e8f9ee">앱 다운로드, 화분 등록</text>
  <text x="100" y="160" text-anchor="middle" font-size="10" fill="#7f8c8d">STEP 1</text>

  <line x1="170" y1="105" x2="200" y2="105" stroke="#bdc3c7" stroke-width="2" marker-end="url(#arrowPM)"/>

  <!-- Step 2 -->
  <rect x="210" y="60" width="140" height="90" rx="10" fill="#4A90D9"/>
  <text x="280" y="90" text-anchor="middle" font-size="11" font-weight="bold" fill="white">식물 종류 선택</text>
  <text x="280" y="108" text-anchor="middle" font-size="11" font-weight="bold" fill="white">/ AI 인식</text>
  <text x="280" y="128" text-anchor="middle" font-size="9" fill="#e8f0fe">300종+ DB에서 자동 매칭</text>
  <text x="280" y="160" text-anchor="middle" font-size="10" fill="#7f8c8d">STEP 2</text>

  <line x1="350" y1="105" x2="380" y2="105" stroke="#bdc3c7" stroke-width="2" marker-end="url(#arrowPM)"/>

  <!-- Step 3 -->
  <rect x="390" y="60" width="140" height="90" rx="10" fill="#FFD93D"/>
  <text x="460" y="90" text-anchor="middle" font-size="11" font-weight="bold" fill="#2c3e50">자동 모니터링</text>
  <text x="460" y="108" text-anchor="middle" font-size="11" font-weight="bold" fill="#2c3e50">/ 급수 시작</text>
  <text x="460" y="128" text-anchor="middle" font-size="9" fill="#5d4e0e">최적 환경 자동 유지</text>
  <text x="460" y="160" text-anchor="middle" font-size="10" fill="#7f8c8d">STEP 3</text>

  <line x1="530" y1="105" x2="560" y2="105" stroke="#bdc3c7" stroke-width="2" marker-end="url(#arrowPM)"/>

  <!-- Step 4 -->
  <rect x="570" y="60" width="140" height="90" rx="10" fill="#FF6B6B"/>
  <text x="640" y="90" text-anchor="middle" font-size="11" font-weight="bold" fill="white">이상 감지 시</text>
  <text x="640" y="108" text-anchor="middle" font-size="11" font-weight="bold" fill="white">AI 진단 / 처방</text>
  <text x="640" y="128" text-anchor="middle" font-size="9" fill="#ffe0e0">사진 촬영 → 진단 결과</text>
  <text x="640" y="160" text-anchor="middle" font-size="10" fill="#7f8c8d">STEP 4</text>

  <line x1="710" y1="105" x2="740" y2="105" stroke="#bdc3c7" stroke-width="2" marker-end="url(#arrowPM)"/>

  <!-- Step 5 -->
  <rect x="750" y="60" width="80" height="90" rx="10" fill="#9B59B6"/>
  <text x="790" y="95" text-anchor="middle" font-size="11" font-weight="bold" fill="white">성장</text>
  <text x="790" y="115" text-anchor="middle" font-size="11" font-weight="bold" fill="white">일지</text>
  <text x="790" y="160" text-anchor="middle" font-size="10" fill="#7f8c8d">STEP 5</text>

  <!-- Engagement Loop -->
  <rect x="30" y="200" width="180" height="65" rx="8" fill="#1abc9c" opacity="0.8"/>
  <text x="120" y="225" text-anchor="middle" font-size="11" font-weight="bold" fill="white">커뮤니티 참여</text>
  <text x="120" y="248" text-anchor="middle" font-size="10" fill="#e8f8f5">성장 일지 공유, Q&A</text>

  <rect x="240" y="200" width="180" height="65" rx="8" fill="#e67e22" opacity="0.8"/>
  <text x="330" y="225" text-anchor="middle" font-size="11" font-weight="bold" fill="white">용품 쇼핑</text>
  <text x="330" y="248" text-anchor="middle" font-size="10" fill="#fce4c8">진단 결과 기반 추천 구매</text>

  <rect x="450" y="200" width="180" height="65" rx="8" fill="#34495e" opacity="0.7"/>
  <text x="540" y="225" text-anchor="middle" font-size="11" font-weight="bold" fill="white">추가 식물 구매</text>
  <text x="540" y="248" text-anchor="middle" font-size="10" fill="#bdc3c7">맞춤 큐레이션 추천</text>

  <rect x="660" y="200" width="170" height="65" rx="8" fill="#50C878" opacity="0.7"/>
  <text x="745" y="225" text-anchor="middle" font-size="11" font-weight="bold" fill="white">프리미엄 구독</text>
  <text x="745" y="248" text-anchor="middle" font-size="10" fill="#e8f9ee">무제한 화분, AI 진단</text>

  <!-- Retention metrics -->
  <rect x="30" y="295" width="800" height="55" rx="8" fill="#34495e" opacity="0.05"/>
  <text x="425" y="315" text-anchor="middle" font-size="12" font-weight="bold" fill="#2c3e50">핵심 리텐션 지표</text>
  <text x="130" y="338" text-anchor="middle" font-size="10" fill="#7f8c8d">DAU/MAU: 45%</text>
  <text x="310" y="338" text-anchor="middle" font-size="10" fill="#7f8c8d">D30 리텐션: 62%</text>
  <text x="490" y="338" text-anchor="middle" font-size="10" fill="#7f8c8d">프리미엄 전환율: 15%</text>
  <text x="690" y="338" text-anchor="middle" font-size="10" fill="#7f8c8d">커머스 구매율: 28%</text>
</svg>
</div>

---

## 3. 시장 분석

### TAM/SAM/SOM 시장 규모

<div align="center">
<svg width="700" height="450" xmlns="http://www.w3.org/2000/svg">
  <rect width="700" height="450" fill="#f8f9fa" rx="15"/>
  <text x="350" y="35" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">반려식물 시장 규모</text>

  <!-- TAM -->
  <circle cx="350" cy="230" r="170" fill="#50C878" opacity="0.2" stroke="#50C878" stroke-width="2"/>
  <text x="350" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#2d8a4e">TAM: 2.3조원</text>
  <text x="350" y="103" text-anchor="middle" font-size="11" fill="#2d8a4e">국내 반려식물 전체 시장</text>

  <!-- SAM -->
  <circle cx="350" cy="260" r="110" fill="#4A90D9" opacity="0.25" stroke="#4A90D9" stroke-width="2"/>
  <text x="350" y="178" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">SAM: 1,500억원</text>
  <text x="350" y="196" text-anchor="middle" font-size="11" fill="#4A90D9">스마트 가드닝 디바이스 + 서비스</text>

  <!-- SOM -->
  <circle cx="350" cy="290" r="50" fill="#FF6B6B" opacity="0.4" stroke="#FF6B6B" stroke-width="2"/>
  <text x="350" y="285" text-anchor="middle" font-size="13" font-weight="bold" fill="#c0392b">SOM</text>
  <text x="350" y="303" text-anchor="middle" font-size="11" font-weight="bold" fill="#c0392b">80억원</text>

  <rect x="30" y="385" width="200" height="50" rx="6" fill="#50C878" opacity="0.12"/>
  <text x="130" y="405" text-anchor="middle" font-size="10" fill="#2d8a4e">연 성장률 (CAGR)</text>
  <text x="130" y="423" text-anchor="middle" font-size="14" font-weight="bold" fill="#2d8a4e">+15.3%</text>

  <rect x="250" y="385" width="200" height="50" rx="6" fill="#4A90D9" opacity="0.12"/>
  <text x="350" y="405" text-anchor="middle" font-size="10" fill="#4A90D9">MZ세대 월 식물 지출</text>
  <text x="350" y="423" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">평균 4.7만원</text>

  <rect x="470" y="385" width="200" height="50" rx="6" fill="#FF6B6B" opacity="0.12"/>
  <text x="570" y="405" text-anchor="middle" font-size="10" fill="#c0392b">타겟 유저 수</text>
  <text x="570" y="423" text-anchor="middle" font-size="14" font-weight="bold" fill="#c0392b">약 480만 명</text>
</svg>
</div>

| 구분 | 규모 | 산출 근거 |
|------|------|-----------|
| TAM (전체 시장) | 2.3조원 | 국내 반려식물 전체 시장 (식물, 용품, 서비스) |
| SAM (유효 시장) | 1,500억원 | 스마트 가드닝 디바이스 + 관리 서비스 시장 |
| SOM (목표 시장) | 80억원 | 2030 MZ세대 식물 애호가 + 사무실/카페 초기 공략 |

### 시장 성장 동인

MZ세대를 중심으로 반려식물 시장은 연 15% 이상 성장하고 있으며, 기술 결합 스마트 가드닝 제품에 대한 수요가 특히 높다. 1인가구 증가와 재택근무 확산이 홈 가드닝 시장을 지속 견인한다.

**핵심 성장 동인:**

1. **MZ세대 라이프스타일**: 플랜테리어, SNS 식물 자랑 문화, "식집사" 트렌드가 지속 확산
2. **1인가구 증가**: 2025년 기준 1인가구 900만, 반려동물 대안으로서의 반려식물 수요
3. **재택근무 정착**: 집 환경 개선 니즈, 업무 공간에서의 그린 인테리어 수요
4. **크라우드펀딩 시장 활성화**: 와디즈/텀블벅에서 스마트 가드닝 제품의 높은 달성률
5. **글로벌 확장 기회**: 킥스타터/인디고고를 통한 글로벌 B2C 시장 접근 가능

---

## 4. 비즈니스 모델

### 비즈니스 모델 다이어그램

<div align="center">
<svg width="850" height="400" xmlns="http://www.w3.org/2000/svg">
  <rect width="850" height="400" fill="#f8f9fa" rx="15"/>
  <text x="425" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">PlantMate 수익 모델</text>

  <!-- Hardware -->
  <rect x="30" y="55" width="250" height="130" rx="10" fill="#50C878" opacity="0.15" stroke="#50C878" stroke-width="2"/>
  <text x="155" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#2d8a4e">하드웨어 판매</text>
  <text x="155" y="105" text-anchor="middle" font-size="11" fill="#2c3e50">스마트 화분: 5.9만원</text>
  <text x="155" y="123" text-anchor="middle" font-size="11" fill="#2c3e50">BLE 허브: 2.9만원</text>
  <text x="155" y="141" text-anchor="middle" font-size="11" fill="#2c3e50">프리미엄 화분: 8.9만원</text>
  <text x="155" y="159" text-anchor="middle" font-size="11" fill="#2c3e50">제조 마진: 55%</text>
  <text x="155" y="177" text-anchor="middle" font-size="10" fill="#2d8a4e">매출 비중: 54%</text>

  <!-- Subscription -->
  <rect x="300" y="55" width="250" height="130" rx="10" fill="#4A90D9" opacity="0.15" stroke="#4A90D9" stroke-width="2"/>
  <text x="425" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">앱 구독</text>
  <text x="425" y="105" text-anchor="middle" font-size="11" fill="#2c3e50">무료 티어: 화분 3개, 기본 기능</text>
  <text x="425" y="123" text-anchor="middle" font-size="11" fill="#2c3e50">프리미엄: 월 3,900원</text>
  <text x="425" y="141" text-anchor="middle" font-size="11" fill="#2c3e50">무제한 화분, AI 진단,</text>
  <text x="425" y="159" text-anchor="middle" font-size="11" fill="#2c3e50">전문가 상담, 성장 분석</text>
  <text x="425" y="177" text-anchor="middle" font-size="10" fill="#4A90D9">매출 비중: 25%</text>

  <!-- Commerce -->
  <rect x="570" y="55" width="250" height="130" rx="10" fill="#FF6B6B" opacity="0.15" stroke="#FF6B6B" stroke-width="2"/>
  <text x="695" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#c0392b">커머스 수수료</text>
  <text x="695" y="105" text-anchor="middle" font-size="11" fill="#2c3e50">식물/흙/비료/화분 판매</text>
  <text x="695" y="123" text-anchor="middle" font-size="11" fill="#2c3e50">수수료율: 15%</text>
  <text x="695" y="141" text-anchor="middle" font-size="11" fill="#2c3e50">브랜드 콜라보</text>
  <text x="695" y="159" text-anchor="middle" font-size="11" fill="#2c3e50">진단 결과 기반 추천 구매</text>
  <text x="695" y="177" text-anchor="middle" font-size="10" fill="#c0392b">매출 비중: 21%</text>

  <!-- Unit Economics -->
  <rect x="30" y="210" width="790" height="80" rx="8" fill="#34495e" opacity="0.05" stroke="#34495e" stroke-width="1"/>
  <text x="425" y="235" text-anchor="middle" font-size="14" font-weight="bold" fill="#2c3e50">유닛 이코노믹스</text>

  <rect x="50" y="248" width="165" height="35" rx="6" fill="#50C878" opacity="0.12"/>
  <text x="132" y="270" text-anchor="middle" font-size="10" fill="#2c3e50">화분 원가: 2.5만원</text>

  <rect x="230" y="248" width="165" height="35" rx="6" fill="#4A90D9" opacity="0.12"/>
  <text x="312" y="270" text-anchor="middle" font-size="10" fill="#2c3e50">ARPU: 7,800원/월</text>

  <rect x="410" y="248" width="165" height="35" rx="6" fill="#FF6B6B" opacity="0.12"/>
  <text x="492" y="270" text-anchor="middle" font-size="10" fill="#2c3e50">CAC: 12,000원</text>

  <rect x="590" y="248" width="210" height="35" rx="6" fill="#FFD93D" opacity="0.15"/>
  <text x="695" y="270" text-anchor="middle" font-size="10" fill="#2c3e50">LTV: 187,000원 | LTV/CAC: 15.6x</text>

  <!-- GTM Strategy -->
  <text x="425" y="325" text-anchor="middle" font-size="14" font-weight="bold" fill="#2c3e50">Go-to-Market 전략</text>

  <rect x="30" y="340" width="190" height="45" rx="6" fill="#50C878" opacity="0.1"/>
  <text x="125" y="358" text-anchor="middle" font-size="10" font-weight="bold" fill="#2d8a4e">Phase 1: 크라우드펀딩</text>
  <text x="125" y="375" text-anchor="middle" font-size="9" fill="#2c3e50">와디즈 → 초기 팬덤 확보</text>

  <rect x="235" y="340" width="190" height="45" rx="6" fill="#4A90D9" opacity="0.1"/>
  <text x="330" y="358" text-anchor="middle" font-size="10" font-weight="bold" fill="#4A90D9">Phase 2: 이커머스</text>
  <text x="330" y="375" text-anchor="middle" font-size="9" fill="#2c3e50">쿠팡/네이버 → 대중 확산</text>

  <rect x="440" y="340" width="190" height="45" rx="6" fill="#FF6B6B" opacity="0.1"/>
  <text x="535" y="358" text-anchor="middle" font-size="10" font-weight="bold" fill="#c0392b">Phase 3: B2B</text>
  <text x="535" y="375" text-anchor="middle" font-size="9" fill="#2c3e50">카페/사무실 패키지 판매</text>

  <rect x="645" y="340" width="175" height="45" rx="6" fill="#9B59B6" opacity="0.1"/>
  <text x="732" y="358" text-anchor="middle" font-size="10" font-weight="bold" fill="#8e44ad">Phase 4: 글로벌</text>
  <text x="732" y="375" text-anchor="middle" font-size="9" fill="#2c3e50">킥스타터 → 해외 진출</text>
</svg>
</div>

| 요금제 | 비용 | 포함 내용 |
|--------|------|-----------|
| 스마트 화분 (하드웨어) | 5.9만원 | 센서 모듈, 자동 급수, BLE 연결, E-ink 디스플레이 |
| 앱 무료 티어 | 무료 | 기본 모니터링 (화분 3개), 급수 알림, 식물 DB 열람 |
| 앱 프리미엄 | 월 3,900원 | 무제한 화분, AI 진단, 성장 분석, 전문가 상담, 커뮤니티 프리미엄 |
| BLE 허브 | 2.9만원 | Wi-Fi 브릿지, 화분 최대 20개 동시 관리 |

추가 수익: 식물/흙/비료/화분 등 원예용품 커머스 수수료(15%), 브랜드 콜라보 (카페/인테리어), 데이터 기반 원예 컨설팅.

---

## 5. 기술 스택

### 기술 스택 다이어그램

<div align="center">
<svg width="800" height="420" xmlns="http://www.w3.org/2000/svg">
  <rect width="800" height="420" fill="#f8f9fa" rx="15"/>
  <text x="400" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">PlantMate 기술 스택</text>

  <!-- Hardware -->
  <rect x="30" y="50" width="740" height="60" rx="8" fill="#50C878" opacity="0.12" stroke="#50C878" stroke-width="1.5"/>
  <text x="60" y="68" font-size="12" font-weight="bold" fill="#2d8a4e">하드웨어 / 센서</text>
  <rect x="180" y="58" width="100" height="28" rx="5" fill="#50C878"/><text x="230" y="77" text-anchor="middle" font-size="9" fill="white">토양수분 v1.2</text>
  <rect x="290" y="58" width="80" height="28" rx="5" fill="#50C878"/><text x="330" y="77" text-anchor="middle" font-size="9" fill="white">BH1750</text>
  <rect x="380" y="58" width="80" height="28" rx="5" fill="#50C878"/><text x="420" y="77" text-anchor="middle" font-size="9" fill="white">SHT31</text>
  <rect x="470" y="58" width="100" height="28" rx="5" fill="#50C878"/><text x="520" y="77" text-anchor="middle" font-size="9" fill="white">미니 펌프</text>
  <rect x="580" y="58" width="100" height="28" rx="5" fill="#50C878"/><text x="630" y="77" text-anchor="middle" font-size="9" fill="white">E-ink 1.54"</text>

  <!-- MCU -->
  <rect x="30" y="125" width="740" height="60" rx="8" fill="#4A90D9" opacity="0.12" stroke="#4A90D9" stroke-width="1.5"/>
  <text x="60" y="143" font-size="12" font-weight="bold" fill="#4A90D9">MCU / 통신</text>
  <rect x="180" y="133" width="100" height="28" rx="5" fill="#4A90D9"/><text x="230" y="152" text-anchor="middle" font-size="9" fill="white">ESP32-C3</text>
  <rect x="290" y="133" width="80" height="28" rx="5" fill="#4A90D9"/><text x="330" y="152" text-anchor="middle" font-size="9" fill="white">BLE 5.0</text>
  <rect x="380" y="133" width="80" height="28" rx="5" fill="#4A90D9"/><text x="420" y="152" text-anchor="middle" font-size="9" fill="white">Wi-Fi</text>
  <rect x="470" y="133" width="100" height="28" rx="5" fill="#4A90D9"/><text x="520" y="152" text-anchor="middle" font-size="9" fill="white">저전력 설계</text>

  <!-- AI -->
  <rect x="30" y="200" width="740" height="60" rx="8" fill="#FF6B6B" opacity="0.12" stroke="#FF6B6B" stroke-width="1.5"/>
  <text x="60" y="218" font-size="12" font-weight="bold" fill="#c0392b">AI / ML</text>
  <rect x="180" y="208" width="100" height="28" rx="5" fill="#FF6B6B"/><text x="230" y="227" text-anchor="middle" font-size="9" fill="white">MobileNetV3</text>
  <rect x="290" y="208" width="80" height="28" rx="5" fill="#FF6B6B"/><text x="330" y="227" text-anchor="middle" font-size="9" fill="white">TF Lite</text>
  <rect x="380" y="208" width="80" height="28" rx="5" fill="#FF6B6B"/><text x="420" y="227" text-anchor="middle" font-size="9" fill="white">GPT-4 API</text>
  <rect x="470" y="208" width="100" height="28" rx="5" fill="#FF6B6B"/><text x="520" y="227" text-anchor="middle" font-size="9" fill="white">Label Studio</text>

  <!-- Backend -->
  <rect x="30" y="275" width="740" height="60" rx="8" fill="#9B59B6" opacity="0.12" stroke="#9B59B6" stroke-width="1.5"/>
  <text x="60" y="293" font-size="12" font-weight="bold" fill="#8e44ad">백엔드</text>
  <rect x="180" y="283" width="100" height="28" rx="5" fill="#9B59B6"/><text x="230" y="302" text-anchor="middle" font-size="9" fill="white">NestJS</text>
  <rect x="290" y="283" width="80" height="28" rx="5" fill="#9B59B6"/><text x="330" y="302" text-anchor="middle" font-size="9" fill="white">MongoDB</text>
  <rect x="380" y="283" width="80" height="28" rx="5" fill="#9B59B6"/><text x="420" y="302" text-anchor="middle" font-size="9" fill="white">Redis</text>
  <rect x="470" y="283" width="80" height="28" rx="5" fill="#9B59B6"/><text x="510" y="302" text-anchor="middle" font-size="9" fill="white">S3</text>
  <rect x="560" y="283" width="80" height="28" rx="5" fill="#9B59B6"/><text x="600" y="302" text-anchor="middle" font-size="9" fill="white">GraphQL</text>

  <!-- Frontend/Cloud -->
  <rect x="30" y="350" width="360" height="60" rx="8" fill="#FFD93D" opacity="0.15" stroke="#FFD93D" stroke-width="1.5"/>
  <text x="60" y="368" font-size="12" font-weight="bold" fill="#8a7d1e">프론트엔드</text>
  <rect x="160" y="358" width="80" height="28" rx="5" fill="#e6c235"/><text x="200" y="377" text-anchor="middle" font-size="9" fill="#2c3e50">Flutter</text>
  <rect x="250" y="358" width="110" height="28" rx="5" fill="#e6c235"/><text x="305" y="377" text-anchor="middle" font-size="9" fill="#2c3e50">Figma Design</text>

  <rect x="410" y="350" width="360" height="60" rx="8" fill="#1abc9c" opacity="0.12" stroke="#1abc9c" stroke-width="1.5"/>
  <text x="440" y="368" font-size="12" font-weight="bold" fill="#16a085">클라우드</text>
  <rect x="530" y="358" width="80" height="28" rx="5" fill="#1abc9c"/><text x="570" y="377" text-anchor="middle" font-size="9" fill="white">AWS IoT</text>
  <rect x="620" y="358" width="80" height="28" rx="5" fill="#1abc9c"/><text x="660" y="377" text-anchor="middle" font-size="9" fill="white">Lambda</text>
  <rect x="710" y="358" width="50" height="28" rx="5" fill="#1abc9c"/><text x="735" y="377" text-anchor="middle" font-size="9" fill="white">CDN</text>
</svg>
</div>

| 구분 | 기술 |
|------|------|
| 센서 | Capacitive Soil Moisture (v1.2), BH1750 (광량), SHT31 (온습도), EC 센서 |
| MCU | ESP32-C3 (BLE + Wi-Fi), 저전력 설계 |
| 급수 모듈 | 미니 페리스탈틱 펌프, 수위 감지 센서 |
| AI/ML | MobileNetV3 (식물 진단), TensorFlow Lite, GPT-4 API (처방 텍스트) |
| 백엔드 | Node.js (NestJS), MongoDB, Redis, S3, GraphQL |
| 모바일 앱 | Flutter (iOS/Android) |
| 클라우드 | AWS (IoT Core, Lambda, DynamoDB, CDN) |
| 디자인 | E-ink 디스플레이 (1.54"), 미니멀 세라믹 외관 |

---

## 6. 경쟁사 분석

| 항목 | PlantMate (자사) | 플랜티 | 가든파이 | 시토그로우 |
|------|-----------------|--------|---------|-----------|
| 토양수분 센서 | O | O | O | O |
| 광량/온습도 센서 | O | X | O | △ |
| 자동 급수 | O | X | O | X |
| AI 식물 진단 | O (95%) | X | X | △ (80%) |
| 식물 DB 규모 | 300종+ | 100종 | 150종 | 80종 |
| 커뮤니티 기능 | O | X | △ | X |
| 디바이스 가격 | 5.9만원 | 3.5만원 | 8만원 | 4.5만원 |
| 디자인 | 프리미엄 세라믹 | 플라스틱 | 모던 | 플라스틱 |
| 앱 완성도 | 상 | 중 | 중 | 하 |
| GPT 기반 처방 | O | X | X | X |

### 경쟁 우위 핵심 요약

1. **AI 진단 정확도**: 95% Top-1 정확도로 업계 최고 수준
2. **디자인 차별화**: 미니멀 세라믹 외관으로 인테리어 친화적, 디자인 어워드 출품 예정
3. **식물 DB**: 300종 이상의 종합 식물 데이터베이스로 맞춤형 관리 제공
4. **GPT 기반 처방**: AI 진단 결과를 자연어로 쉽게 설명하여 초보자도 즉시 조치 가능
5. **커뮤니티 + 커머스**: 단순 하드웨어를 넘어 식물 라이프스타일 플랫폼으로 확장

---

## 7. 로드맵

### 성장 로드맵 타임라인

<div align="center">
<svg width="900" height="340" xmlns="http://www.w3.org/2000/svg">
  <rect width="900" height="340" fill="#f8f9fa" rx="15"/>
  <text x="450" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2c3e50">PlantMate 성장 로드맵</text>

  <!-- Timeline -->
  <rect x="50" y="55" width="800" height="6" rx="3" fill="#ecf0f1"/>
  <circle cx="150" cy="58" r="10" fill="#50C878" stroke="white" stroke-width="2"/>
  <circle cx="300" cy="58" r="10" fill="#50C878" stroke="white" stroke-width="2"/>
  <circle cx="450" cy="58" r="10" fill="#4A90D9" stroke="white" stroke-width="2"/>
  <circle cx="600" cy="58" r="10" fill="#4A90D9" stroke="white" stroke-width="2"/>
  <circle cx="750" cy="58" r="10" fill="#FF6B6B" stroke="white" stroke-width="2"/>

  <text x="225" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#50C878">2026</text>
  <text x="525" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#4A90D9">2027</text>
  <text x="750" y="48" text-anchor="middle" font-size="12" font-weight="bold" fill="#FF6B6B">2028</text>

  <!-- Phase 1 -->
  <rect x="60" y="80" width="230" height="110" rx="10" fill="#50C878" opacity="0.1" stroke="#50C878" stroke-width="1.5"/>
  <text x="175" y="100" text-anchor="middle" font-size="13" font-weight="bold" fill="#2d8a4e">Phase 1: 런칭</text>
  <text x="175" y="120" text-anchor="middle" font-size="10" fill="#2c3e50">와디즈 크라우드펀딩 (5,000만원)</text>
  <text x="175" y="137" text-anchor="middle" font-size="10" fill="#2c3e50">앱 v1.0 정식 출시</text>
  <text x="175" y="154" text-anchor="middle" font-size="10" fill="#2c3e50">판매: 2,000대</text>
  <text x="175" y="171" text-anchor="middle" font-size="10" fill="#2c3e50">사용자: 1,000명</text>

  <!-- Phase 2 -->
  <rect x="320" y="80" width="280" height="110" rx="10" fill="#4A90D9" opacity="0.1" stroke="#4A90D9" stroke-width="1.5"/>
  <text x="460" y="100" text-anchor="middle" font-size="13" font-weight="bold" fill="#4A90D9">Phase 2: 성장</text>
  <text x="460" y="120" text-anchor="middle" font-size="10" fill="#2c3e50">쿠팡/네이버 입점, B2B 패키지</text>
  <text x="460" y="137" text-anchor="middle" font-size="10" fill="#2c3e50">프리미엄 구독 + 커뮤니티 + 커머스</text>
  <text x="460" y="154" text-anchor="middle" font-size="10" fill="#2c3e50">누적 판매: 15,000대</text>
  <text x="460" y="171" text-anchor="middle" font-size="10" fill="#2c3e50">MAU: 10,000명 | 시리즈 A 투자</text>

  <!-- Phase 3 -->
  <rect x="630" y="80" width="220" height="110" rx="10" fill="#FF6B6B" opacity="0.1" stroke="#FF6B6B" stroke-width="1.5"/>
  <text x="740" y="100" text-anchor="middle" font-size="13" font-weight="bold" fill="#c0392b">Phase 3: 글로벌</text>
  <text x="740" y="120" text-anchor="middle" font-size="10" fill="#2c3e50">킥스타터 글로벌 펀딩</text>
  <text x="740" y="137" text-anchor="middle" font-size="10" fill="#2c3e50">식물 DB 500종 확장</text>
  <text x="740" y="154" text-anchor="middle" font-size="10" fill="#2c3e50">누적 판매: 50,000대</text>
  <text x="740" y="171" text-anchor="middle" font-size="10" fill="#2c3e50">연 매출: 28억원</text>

  <!-- KPI -->
  <rect x="60" y="210" width="790" height="45" rx="8" fill="#34495e" opacity="0.06"/>
  <rect x="80" y="218" width="150" height="25" rx="5" fill="#50C878"/><text x="155" y="235" text-anchor="middle" font-size="10" fill="white">누적 50,000대</text>
  <rect x="250" y="218" width="150" height="25" rx="5" fill="#4A90D9"/><text x="325" y="235" text-anchor="middle" font-size="10" fill="white">MAU 50,000명</text>
  <rect x="420" y="218" width="150" height="25" rx="5" fill="#FF6B6B"/><text x="495" y="235" text-anchor="middle" font-size="10" fill="white">연매출 28억원</text>
  <rect x="590" y="218" width="180" height="25" rx="5" fill="#9B59B6"/><text x="680" y="235" text-anchor="middle" font-size="10" fill="white">고사율 47%→15%</text>

  <!-- Investment -->
  <text x="450" y="285" text-anchor="middle" font-size="11" fill="#7f8c8d">투자: 시드 1억 (Q3 '26) → Pre-A 3억 (Q1 '27) → 시리즈 A 15억 (Q3 '27)</text>
  <text x="450" y="305" text-anchor="middle" font-size="11" fill="#7f8c8d">팀 확장: 4명 (2026) → 10명 (2027) → 18명 (2028)</text>
</svg>
</div>

| 분기 | 마일스톤 | 핵심 목표 |
|------|----------|-----------|
| 2026 Q3 | 스마트 화분 양산 시작, 와디즈 크라우드펀딩 런칭 (목표 5,000만원) | 초기 팬덤 확보 |
| 2026 Q4 | 펀딩 리워드 배송, 앱 v1.0 정식 출시, 사용자 1,000명 | 제품-시장 적합성 검증 |
| 2027 Q1 | 온라인 쇼핑몰 입점 (쿠팡, 네이버), AI 진단 모델 v2 | 유통 채널 확장 |
| 2027 Q2 | 프리미엄 구독 출시, 커뮤니티 오픈, MAU 10,000명 | 구독 전환율 15%+ |
| 2027 Q3 | 카페/사무실 B2B 패키지 출시, 시리즈 A 투자 유치 | 15억원 투자 유치 |
| 2027 Q4 | 원예용품 커머스 오픈, 식물 DB 500종 확장 | 커머스 GMV 월 1억원 |
| 2028 Q1 | 글로벌 크라우드펀딩 (킥스타터), 해외 시장 테스트 | 글로벌 진출 |
| 2028 Q2 | 연 매출 28억원 달성, 누적 판매 50,000대 | BEP 달성 |

---

## 8. 팀 구성

| 이름 | 역할 | 주요 경력 | 담당 영역 |
|------|------|-----------|-----------|
| 박식물 | 대표 / IoT 개발 리드 | 한양대 전자공학, IoT 스타트업 인턴, 하드웨어 제품 양산 경험 | 하드웨어 설계, 양산 관리, 투자 IR |
| 김러닝 | AI 개발 | POSTECH AI대학원, 컴퓨터 비전 논문 3편, 식물 질병 탐지 연구 | AI 모델 개발, 식물 DB 구축, 진단 엔진 |
| 이플럿 | 앱 개발 | 성균관대 소프트웨어학과, Flutter 앱 4개 출시, UI/UX 디자인 | 모바일 앱, 백엔드, 커뮤니티 |
| 최원예 | 원예학 / 제품 디자인 | 서울대 원예학과, 식물 관리 유튜브 채널 (구독자 2만), 세라믹 디자인 | 식물 DB 감수, 제품 디자인, 마케팅 |

### 채용 계획

| 시기 | 포지션 | 인원 |
|------|--------|------|
| 2026 Q4 | 마케팅/커뮤니티 매니저 | 1명 |
| 2027 Q1 | 백엔드 개발자 | 1명 |
| 2027 Q2 | 커머스 MD | 1명 |
| 2027 Q3 | 해외사업 담당 | 1명 |
| 2027 Q4 | QA/CS | 1명 |
| 2028 상반기 | 추가 개발/마케팅 인력 | 4명 |

---

## 9. 재무 계획

| 항목 | 2026년 | 2027년 | 2028년 |
|------|--------|--------|--------|
| 매출 | 2억원 | 12억원 | 28억원 |
| 하드웨어 판매 | 1.5억원 | 7억원 | 15억원 |
| 앱 구독 | 0.2억원 | 2.5억원 | 7억원 |
| 커머스 수수료 | 0.3억원 | 2.5억원 | 6억원 |
| 영업비용 | 2.5억원 | 9억원 | 18억원 |
| 영업이익 | -0.5억원 | 3억원 | 10억원 |
| 영업이익률 | -25% | 25% | 36% |

### 비용 구조 상세

| 비용 항목 | 2026년 | 2027년 | 2028년 |
|-----------|--------|--------|--------|
| 인건비 | 0.8억원 | 3.5억원 | 7억원 |
| 하드웨어 제조 원가 | 0.7억원 | 3.2억원 | 6.8억원 |
| 마케팅/광고 | 0.5억원 | 1.2억원 | 2억원 |
| 클라우드/API 비용 | 0.2억원 | 0.5억원 | 1억원 |
| 물류/배송 | 0.1억원 | 0.3억원 | 0.7억원 |
| 사무실/기타 | 0.2억원 | 0.3억원 | 0.5억원 |

### 손익분기점 분석

- **월 BEP**: 화분 월 판매 800대 + 프리미엄 구독자 2,000명
- **BEP 도달 예상 시점**: 2027년 Q2
- **누적 흑자 전환**: 2027년 Q4

---

## 10. 리스크 관리

| 리스크 유형 | 상세 내용 | 발생 확률 | 영향도 | 대응 방안 |
|-------------|-----------|-----------|--------|-----------|
| 기술 리스크 | 자동 급수 오작동 (과습/과건) | 중 | 높음 | 안전 장치 (최대 급수량 리미터), 다중 센서 검증 |
| 제품 리스크 | 하드웨어 불량/반품 | 중 | 높음 | QC 강화, 30일 무상 교환, 방수 등급 IP65 |
| 시장 리스크 | 식물 트렌드 둔화 | 낮음 | 중간 | B2B 시장 다변화 (카페/사무실/호텔) |
| 경쟁 리스크 | 대형 가전사 유사 제품 출시 | 중 | 중간 | AI 진단 + 커뮤니티 생태계로 차별화, 빠른 시장 선점 |
| 공급망 리스크 | 센서/MCU 부품 수급 차질 | 낮음 | 중간 | 3개월분 안전재고, 대체 부품 호환 설계 |
| 재무 리스크 | 크라우드펀딩 미달 | 낮음 | 높음 | 사전 마케팅 강화, 얼리버드 할인, SNS 바이럴 |

---

## 11. 사회적 임팩트

| 지표 | 기대 효과 |
|------|-----------|
| 식물 고사율 감소 | 47% → 15% (스마트 화분 사용 가구) |
| 반려식물 관리 만족도 | 58.4% → 90%+ |
| 원예 접근성 향상 | 식물 초보자의 성공적 관리율 3배 향상 |
| 실내 공기 개선 | 건강한 식물 유지로 실내 CO2 흡수 효과 |
| 커뮤니티 활성화 | 식물 나눔/교환 문화 확산 |

---

## 12. 참고문헌

1. 농촌진흥청, "2025 국내 반려식물 보유 실태 조사," 2025.
2. 플라워24, "식물 구매 후 관리 실태 및 고사율 조사," 2025.
3. 한국갤럽, "MZ세대 라이프스타일 및 식물 관리 인식 조사," 2025.
4. 신한카드, "2025 소비 트렌드 리포트: 반려식물 지출 분석," 2025.
5. Howard, A. et al., "MobileNetV3: Searching for MobileNetV3," ICCV, 2023.
6. 한국농수산식품유통공사, "화훼산업 동향 분석," 2025.
7. Mordor Intelligence, "Smart Garden Market - Growth, Trends, and Forecast," 2025.
8. 한국트렌드연구소, "플랜테리어 트렌드 보고서," 2025.
9. 앱애니, "라이프스타일 앱 이용 현황 분석," 2025.
