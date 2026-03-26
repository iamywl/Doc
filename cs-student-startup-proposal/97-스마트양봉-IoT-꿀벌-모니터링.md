# 스마트양봉 IoT 꿀벌 모니터링 플랫폼 (BeeKeep AI)
## 2026년 예비창업패키지 사업계획서

---

## 1. 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | 비킵AI (BeeKeep AI) |
| 대표자 | 윤서현 (컴퓨터공학과 4학년) |
| 팀구성 | 대표 1명, 개발 2명(IoT/백엔드), AI 1명, 하드웨어 1명, 양봉전문가 1명 |
| 창업아이템 | IoT 센서 기반 스마트 양봉 모니터링 및 AI 분석 플랫폼 |
| 분야 | AgriTech / IoT / AI |
| 사업화단계 | 프로토타입 단계 |
| 목표시장 | 양봉 농가, 양봉 조합, 농업기술센터, 꿀 생산 기업 |
| 신청금액 | 1억원 (100,000천원) |

---

## 2. 창업 아이템 개요

| 구분 | 내용 |
|------|------|
| 아이템명 | BeeKeep AI - IoT 스마트 양봉 모니터링 플랫폼 |
| 핵심기술 | IoT 센서 네트워크, AI 벌집 상태 분석, 실시간 모니터링 대시보드 |
| 주요기능 | 벌통 온습도/무게/소리 실시간 모니터링, AI 질병 예측, 분봉 감지, 꿀 수확 적기 알림 |
| 목표고객 | 전국 양봉 농가 (약 25,000호), 양봉 협회, 농업기술센터 |
| 차별점 | 음향 AI 기반 꿀벌 건강 상태 분석, 저전력 LoRa 기반 산간지역 통신 |
| 서비스형태 | IoT 디바이스 + SaaS 플랫폼 (모바일 앱) |
| 수익모델 | 디바이스 판매, 월 구독료, 데이터 분석 리포트 |

---

## 3. 문제 인식

### 3.1 양봉 산업 현황 통계

| 지표 | 수치 | 출처 |
|------|------|------|
| 국내 양봉 농가 수 | 약 25,400호 | 농림축산식품부 (2025) |
| 연간 꿀 생산량 | 약 22,000톤 | 한국양봉협회 (2025) |
| 양봉 산업 시장 규모 | 약 5,800억원 | 농촌진흥청 (2025) |
| 꿀벌 폐사율 (겨울철) | 약 28~35% | 농촌진흥청 꿀벌질병관리센터 (2025) |
| 말벌 피해 벌통 수 | 연간 약 43만 통 | 농림축산식품부 (2025) |
| 양봉 농가 평균 연령 | 64.2세 | 통계청 농업총조사 (2025) |
| 스마트 양봉 기술 도입률 | 3.8% 미만 | 농촌진흥청 (2025) |
| 기후변화 영향 수확량 감소 | 최근 5년간 연평균 12% 감소 | 한국양봉학회 (2025) |
| 낭충봉아부패병 발생 건수 | 연간 약 1,200건 | 농림축산검역본부 (2025) |
| 양봉 인력 부족률 | 약 32% | 한국양봉협회 (2025) |

### 3.2 이해관계자 페인포인트

> "벌통이 산꼭대기에 있어서 매일 확인하러 가기 힘듭니다. 특히 겨울철에 폐사를 늦게 발견하면 벌통 전체를 잃게 됩니다."
> — 강OO, 충북 충주 양봉 농가 (20년 경력)

> "분봉 시기를 놓치면 절반의 꿀벌을 잃습니다. 경험에만 의존하기에는 날씨 변화가 너무 심합니다."
> — 정OO, 전남 구례 양봉 농가 (15년 경력)

> "말벌 침입을 바로 감지하면 피해를 최소화할 수 있는데, 대부분 이미 늦은 뒤에 발견합니다."
> — 이OO, 경남 합천 양봉 농가 (12년 경력)

> "아버지 양봉을 이어받았는데, 체계적인 데이터 관리가 안 돼서 매년 시행착오를 반복합니다."
> — 김OO, 청년 양봉인 (3년차)

### 3.3 문제 구조 분석

```svg
<svg viewBox="0 0 800 480" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="grad97a" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#D97706;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#F59E0B;stop-opacity:1"/>
    </linearGradient>
  </defs>

  <rect x="250" y="15" width="300" height="55" rx="12" fill="url(#grad97a)"/>
  <text x="400" y="48" text-anchor="middle" fill="white" font-size="15" font-weight="bold">양봉 산업의 구조적 문제</text>

  <rect x="30" y="110" width="170" height="50" rx="8" fill="#92400E"/>
  <text x="115" y="140" text-anchor="middle" fill="white" font-size="12" font-weight="bold">고령화·인력 부족</text>

  <rect x="230" y="110" width="170" height="50" rx="8" fill="#92400E"/>
  <text x="315" y="140" text-anchor="middle" fill="white" font-size="12" font-weight="bold">질병·폐사 증가</text>

  <rect x="430" y="110" width="170" height="50" rx="8" fill="#92400E"/>
  <text x="515" y="140" text-anchor="middle" fill="white" font-size="12" font-weight="bold">기후변화 영향</text>

  <rect x="600" y="110" width="170" height="50" rx="8" fill="#92400E"/>
  <text x="685" y="140" text-anchor="middle" fill="white" font-size="12" font-weight="bold">데이터 부재</text>

  <line x1="400" y1="70" x2="115" y2="110" stroke="#D97706" stroke-width="2"/>
  <line x1="400" y1="70" x2="315" y2="110" stroke="#D97706" stroke-width="2"/>
  <line x1="400" y1="70" x2="515" y2="110" stroke="#D97706" stroke-width="2"/>
  <line x1="400" y1="70" x2="685" y2="110" stroke="#D97706" stroke-width="2"/>

  <rect x="30" y="190" width="170" height="75" rx="6" fill="#FEF3C7" stroke="#D97706"/>
  <text x="115" y="210" text-anchor="middle" font-size="10">• 평균 연령 64.2세</text>
  <text x="115" y="227" text-anchor="middle" font-size="10">• 노동집약적 관리</text>
  <text x="115" y="244" text-anchor="middle" font-size="10">• 청년 인력 유입 저조</text>

  <rect x="230" y="190" width="170" height="75" rx="6" fill="#FEF3C7" stroke="#D97706"/>
  <text x="315" y="210" text-anchor="middle" font-size="10">• 겨울 폐사율 35%</text>
  <text x="315" y="227" text-anchor="middle" font-size="10">• 조기 발견 어려움</text>
  <text x="315" y="244" text-anchor="middle" font-size="10">• 말벌 피해 심각</text>

  <rect x="430" y="190" width="170" height="75" rx="6" fill="#FEF3C7" stroke="#D97706"/>
  <text x="515" y="210" text-anchor="middle" font-size="10">• 이상기온 빈번</text>
  <text x="515" y="227" text-anchor="middle" font-size="10">• 밀원식물 변화</text>
  <text x="515" y="244" text-anchor="middle" font-size="10">• 수확량 변동성 증가</text>

  <rect x="600" y="190" width="170" height="75" rx="6" fill="#FEF3C7" stroke="#D97706"/>
  <text x="685" y="210" text-anchor="middle" font-size="10">• 경험 의존 관리</text>
  <text x="685" y="227" text-anchor="middle" font-size="10">• 기록 체계 미비</text>
  <text x="685" y="244" text-anchor="middle" font-size="10">• 과학적 분석 부재</text>

  <rect x="200" y="330" width="400" height="55" rx="12" fill="#059669"/>
  <text x="400" y="355" text-anchor="middle" fill="white" font-size="13" font-weight="bold">BeeKeep AI: IoT + AI 기반</text>
  <text x="400" y="375" text-anchor="middle" fill="white" font-size="13" font-weight="bold">데이터 주도 스마트 양봉 혁신</text>

  <line x1="115" y1="265" x2="350" y2="330" stroke="#059669" stroke-width="2" stroke-dasharray="5,5"/>
  <line x1="315" y1="265" x2="380" y2="330" stroke="#059669" stroke-width="2" stroke-dasharray="5,5"/>
  <line x1="515" y1="265" x2="420" y2="330" stroke="#059669" stroke-width="2" stroke-dasharray="5,5"/>
  <line x1="685" y1="265" x2="450" y2="330" stroke="#059669" stroke-width="2" stroke-dasharray="5,5"/>
</svg>
```

---

## 4. 솔루션

### 4.1 핵심 기능

**① IoT 벌통 모니터링 디바이스**
- 온도, 습도, 무게, 소리, 진동 5종 센서 통합 모듈
- LoRa 기반 저전력 장거리 통신 (산간지역 5km 범위)
- 태양광 패널 + 배터리로 6개월 이상 무충전 운용
- IP67 방수·방진 설계

**② AI 꿀벌 건강 분석**
- 음향 분석 AI로 꿀벌 건강 상태 5단계 진단
- 온습도 패턴 분석으로 질병(낭충봉아부패병 등) 사전 경고
- 무게 변화 패턴으로 꿀 수확 적기 알림 및 분봉 감지

**③ 스마트 양봉 대시보드**
- 모든 벌통 상태를 모바일 앱에서 실시간 확인
- 알림(질병 경고, 분봉 감지, 말벌 침입, 이상 온도 등)
- 양봉 일지 자동 기록 및 관리

**④ 데이터 기반 양봉 컨설팅**
- 지역별 밀원 현황 및 최적 이동시기 분석
- 벌통별 생산성 분석 및 개선 리포트
- 양봉 커뮤니티 및 정보 공유 기능

### 4.2 시스템 아키텍처

```svg
<svg viewBox="0 0 800 520" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="arch97" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#D97706;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#92400E;stop-opacity:1"/>
    </linearGradient>
  </defs>

  <!-- IoT Layer -->
  <rect x="20" y="20" width="760" height="100" rx="10" fill="#FFFBEB" stroke="#D97706"/>
  <text x="400" y="42" text-anchor="middle" font-size="14" font-weight="bold" fill="#92400E">IoT 센서 레이어</text>
  <rect x="40" y="55" width="120" height="50" rx="6" fill="url(#arch97)"/>
  <text x="100" y="77" text-anchor="middle" fill="white" font-size="10">온습도 센서</text>
  <text x="100" y="93" text-anchor="middle" fill="white" font-size="9">(DHT22)</text>
  <rect x="175" y="55" width="120" height="50" rx="6" fill="url(#arch97)"/>
  <text x="235" y="77" text-anchor="middle" fill="white" font-size="10">무게 센서</text>
  <text x="235" y="93" text-anchor="middle" fill="white" font-size="9">(로드셀 HX711)</text>
  <rect x="310" y="55" width="120" height="50" rx="6" fill="url(#arch97)"/>
  <text x="370" y="77" text-anchor="middle" fill="white" font-size="10">음향 센서</text>
  <text x="370" y="93" text-anchor="middle" fill="white" font-size="9">(MEMS 마이크)</text>
  <rect x="445" y="55" width="120" height="50" rx="6" fill="url(#arch97)"/>
  <text x="505" y="77" text-anchor="middle" fill="white" font-size="10">진동 센서</text>
  <text x="505" y="93" text-anchor="middle" fill="white" font-size="9">(가속도계)</text>
  <rect x="580" y="55" width="180" height="50" rx="6" fill="url(#arch97)"/>
  <text x="670" y="77" text-anchor="middle" fill="white" font-size="10">통신 모듈</text>
  <text x="670" y="93" text-anchor="middle" fill="white" font-size="9">(LoRa + ESP32)</text>

  <!-- Gateway -->
  <rect x="250" y="150" width="300" height="50" rx="8" fill="#F59E0B"/>
  <text x="400" y="180" text-anchor="middle" fill="white" font-size="13" font-weight="bold">LoRa 게이트웨이 (셀룰러 LTE 업링크)</text>

  <!-- Cloud -->
  <rect x="20" y="230" width="760" height="100" rx="10" fill="#ECFDF5" stroke="#059669"/>
  <text x="400" y="252" text-anchor="middle" font-size="14" font-weight="bold" fill="#059669">클라우드 백엔드</text>
  <rect x="40" y="265" width="170" height="45" rx="6" fill="#059669"/>
  <text x="125" y="285" text-anchor="middle" fill="white" font-size="11">MQTT Broker</text>
  <text x="125" y="300" text-anchor="middle" fill="white" font-size="9">(AWS IoT Core)</text>
  <rect x="230" y="265" width="170" height="45" rx="6" fill="#059669"/>
  <text x="315" y="285" text-anchor="middle" fill="white" font-size="11">시계열 DB</text>
  <text x="315" y="300" text-anchor="middle" fill="white" font-size="9">(InfluxDB)</text>
  <rect x="420" y="265" width="170" height="45" rx="6" fill="#059669"/>
  <text x="505" y="285" text-anchor="middle" fill="white" font-size="11">AI 분석 서버</text>
  <text x="505" y="300" text-anchor="middle" fill="white" font-size="9">(TensorFlow Lite)</text>
  <rect x="610" y="265" width="150" height="45" rx="6" fill="#059669"/>
  <text x="685" y="285" text-anchor="middle" fill="white" font-size="11">알림 서비스</text>
  <text x="685" y="300" text-anchor="middle" fill="white" font-size="9">(FCM / SMS)</text>

  <!-- App -->
  <rect x="20" y="360" width="760" height="80" rx="10" fill="#EFF6FF" stroke="#3B82F6"/>
  <text x="400" y="382" text-anchor="middle" font-size="14" font-weight="bold" fill="#1D4ED8">사용자 서비스</text>
  <rect x="100" y="395" width="200" height="35" rx="6" fill="#3B82F6"/>
  <text x="200" y="417" text-anchor="middle" fill="white" font-size="12">모바일 앱 (Flutter)</text>
  <rect x="340" y="395" width="200" height="35" rx="6" fill="#3B82F6"/>
  <text x="440" y="417" text-anchor="middle" fill="white" font-size="12">웹 대시보드 (React)</text>
  <rect x="580" y="395" width="150" height="35" rx="6" fill="#3B82F6"/>
  <text x="655" y="417" text-anchor="middle" fill="white" font-size="12">API 서비스</text>

  <!-- Arrows -->
  <line x1="400" y1="120" x2="400" y2="150" stroke="#64748B" stroke-width="2"/>
  <line x1="400" y1="200" x2="400" y2="230" stroke="#64748B" stroke-width="2"/>
  <line x1="400" y1="330" x2="400" y2="360" stroke="#64748B" stroke-width="2"/>
</svg>
```

### 4.3 기술 스택

| 구분 | 기술 | 선정 사유 |
|------|------|-----------|
| MCU | ESP32-S3 | 저전력, Wi-Fi/BLE 지원, 음향 처리 가능 |
| 통신 | LoRa (SX1276) | 산간지역 장거리 통신 (5km+) |
| 음향 AI | TensorFlow Lite for MCU | 엣지 디바이스 음향 분류 |
| 클라우드 | AWS IoT Core + Lambda | 서버리스 IoT 데이터 처리 |
| 시계열 DB | InfluxDB | 센서 데이터 특화 저장/쿼리 |
| 모바일 앱 | Flutter | iOS/Android 크로스 플랫폼 |
| 백엔드 | FastAPI (Python) | 빠른 API 개발 및 AI 모델 연동 |
| 데이터 분석 | Pandas, Scikit-learn | 패턴 분석 및 예측 모델 |
| 하드웨어 설계 | KiCad, FreeCAD | 커스텀 PCB 및 외장 케이스 설계 |
| DevOps | Docker, AWS ECS | 컨테이너 기반 배포 |

---

## 5. 시장 분석

### 5.1 시장 규모 (TAM/SAM/SOM)

| 구분 | 시장 규모 | 산출 근거 |
|------|-----------|-----------|
| TAM (전체 시장) | 1.2조원 | 글로벌 정밀 양봉(Precision Beekeeping) 시장 (Grand View Research, 2025) |
| SAM (유효 시장) | 1,800억원 | 국내 양봉 산업 중 기술 솔루션 시장 (스마트팜 포함) |
| SOM (목표 시장) | 85억원 | 스마트 양봉 IoT 솔루션 도입 잠재 시장 (양봉 농가 15% × 연 56만원) |

### 5.2 경쟁사 비교

| 항목 | BeeKeep AI | Arnia (영국) | BroodMinder (미국) | 국내 A사 |
|------|-----------|-------------|-------------------|----------|
| 센서 종류 | 5종 통합 | 3종 | 2종 | 2종 |
| 음향 AI 분석 | O | O | X | X |
| LoRa 통신 | O | X | X | X |
| 한국어 앱 | O | X | X | O |
| 한국 양봉 최적화 | O | X | X | △ |
| 디바이스 가격 | 15만원 | 40만원+ | 25만원 | 20만원 |
| AI 질병 예측 | O | △ | X | X |

---

## 6. 비즈니스 모델

### 6.1 수익 구조

| 수익원 | 내용 | 과금 방식 |
|--------|------|-----------|
| IoT 디바이스 판매 | 벌통 모니터링 센서 모듈 | 대당 15만원 (원가 7만원) |
| SaaS 구독 | 모바일 앱 + 대시보드 이용료 | 월 9,900원 (벌통 50통 기준) |
| 프리미엄 분석 | AI 심층 분석 리포트 | 월 29,900원 |
| 데이터 서비스 | 양봉 빅데이터 분석 리포트 | 기관용 연 500만원~ |
| LoRa 게이트웨이 | 공용 게이트웨이 설치 | 대당 50만원 |

### 6.2 3개년 재무 계획

| 항목 | 1차년도 (2026) | 2차년도 (2027) | 3차년도 (2028) |
|------|---------------|---------------|---------------|
| 매출액 | 1.2억원 | 5.5억원 | 14억원 |
| 디바이스 매출 | 0.8억원 | 3.0억원 | 7억원 |
| SaaS 구독 매출 | 0.2억원 | 1.5억원 | 4.5억원 |
| 기타 매출 | 0.2억원 | 1.0억원 | 2.5억원 |
| 영업이익률 | -55% | 3% | 22% |

---

## 7. 실행 계획

### 7.1 4단계 로드맵

| 단계 | 기간 | 주요 목표 | 핵심 성과 |
|------|------|-----------|-----------|
| 1단계: 프로토타입 | 2026.04~07 | 센서 모듈 시작품, 음향 AI 모델 학습 | 디바이스 시작품 완성, AI 정확도 80%+ |
| 2단계: 필드 테스트 | 2026.08~11 | 양봉 농가 현장 시험 | 시범 농가 10곳, 데이터 수집 |
| 3단계: 양산/출시 | 2026.12~2027.03 | 디바이스 양산, 앱 정식 출시 | 판매 300세트, 월 구독 200건 |
| 4단계: 확장 | 2027.04~12 | 전국 확대, 기관 판매 | 판매 2,000세트, MOU 10건 |

### 7.2 팀 구성

| 역할 | 이름 | 전문 분야 | 주요 업무 |
|------|------|-----------|-----------|
| CEO/PM | 윤서현 | 컴퓨터공학, 사업 기획 | 전략 수립, 사업 개발 |
| IoT 개발 | 박준혁 | 임베디드 시스템, LoRa | 센서 모듈 및 통신 개발 |
| 백엔드 | 김예린 | 클라우드, 데이터 엔지니어링 | 서버 및 데이터 파이프라인 |
| AI 엔지니어 | 이승호 | 음향 분석, 딥러닝 | 음향 AI 모델 개발 |
| 하드웨어 | 최민기 | 전자공학, PCB 설계 | 디바이스 하드웨어 설계 |
| 양봉 전문가 | 장형수 | 양봉학, 양봉 20년 경력 | 도메인 자문, 현장 시험 |

---

## 8. 리스크 관리

| 리스크 | 발생 확률 | 영향도 | 대응 전략 |
|--------|-----------|--------|-----------|
| 고령 농가의 디지털 기기 사용 어려움 | 상 | 상 | 직관적 UI 설계, 현장 교육 지원, 자녀 연동 기능 |
| IoT 디바이스 현장 내구성 문제 | 중 | 상 | IP67 방수, 내후성 테스트, 1년 무상 A/S |
| 산간 지역 통신 불량 | 중 | 중 | LoRa 메시 네트워크, 로컬 데이터 저장 후 일괄 전송 |
| AI 음향 분석 정확도 부족 | 중 | 상 | 현장 데이터 반복 학습, 양봉 전문가 검증 |
| 낮은 시장 인지도 | 상 | 중 | 양봉 협회 제휴, 농업기술센터 시범사업 참여 |
| 수입 제품과의 가격 경쟁 | 하 | 중 | 한국 양봉 환경 최적화, 현지 A/S로 차별화 |

---

## 9. 사회적 가치

### 9.1 기대 사회적 효과

- **꿀벌 보호**: AI 조기 질병 감지로 꿀벌 폐사율 50% 이상 감소 목표
- **식량 안보**: 꿀벌의 수분(受粉) 역할 보호로 국내 농업 생태계 유지에 기여
- **고령 농가 지원**: 원격 모니터링으로 고령 양봉인의 노동 부담 경감
- **청년 양봉 유도**: 스마트 기술 도입으로 양봉 산업의 매력도 제고
- **데이터 기반 정책**: 양봉 빅데이터를 통한 과학적 양봉 정책 수립 지원
- **생물다양성 보전**: 꿀벌 개체수 유지가 생태계 다양성 보전에 핵심적 역할

### 9.2 UN SDGs 연계

| SDG | 내용 | 기여 방안 |
|-----|------|-----------|
| SDG 2 | 기아 종식 | 수분 매개 곤충 보호를 통한 농업 생산성 유지 |
| SDG 15 | 육상 생태계 | 꿀벌 보호를 통한 생물다양성 보전 |
| SDG 9 | 산업 혁신 | 전통 양봉 산업의 디지털 혁신 |

---

## 10. 자금 운용 계획

| 항목 | 금액 (만원) | 비율 | 상세 내역 |
|------|------------|------|-----------|
| 하드웨어 개발 | 2,500 | 25% | PCB 설계·제작, 센서 모듈, 금형, 시작품 100대 |
| 소프트웨어 개발 | 2,200 | 22% | 모바일 앱, 웹 대시보드, 백엔드 API |
| AI 모델 개발 | 1,500 | 15% | 음향 AI 학습, 질병 예측 모델, GPU 서버 |
| 클라우드 인프라 | 800 | 8% | AWS IoT Core, 서버, DB |
| 현장 시험 | 1,000 | 10% | 시범 농가 운영, 설치·모니터링, 데이터 수집 |
| 인건비 | 1,200 | 12% | 양봉 전문가 자문료, 보조 인력 |
| 마케팅/영업 | 500 | 5% | 양봉 박람회, 농가 방문 영업, 온라인 마케팅 |
| 운영비/예비비 | 300 | 3% | 사무실, 특허 출원, 예비비 |
| **합계** | **10,000** | **100%** | |

---

## 11. 기대 효과

### 11.1 정량적 기대 효과

| 지표 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 디바이스 판매량 | 500대 | 3,000대 | 10,000대 |
| 앱 구독자 수 | 200명 | 1,500명 | 5,000명 |
| 모니터링 벌통 수 | 2,500통 | 15,000통 | 50,000통 |
| 꿀벌 폐사율 감소 | 15% | 30% | 50% |
| 수확량 증가 (도입 농가) | 10% | 18% | 25% |
| 누적 매출 | 1.2억원 | 6.7억원 | 20.7억원 |
| 고용 창출 | 6명 | 12명 | 25명 |

### 11.2 정성적 기대 효과

- 국내 스마트 양봉 산업의 선도 기업으로 자리매김
- 양봉 산업의 디지털 전환 가속화로 청년 인력 유입 촉진
- 꿀벌 보호를 통한 국가 생태계 서비스 가치 보전
- 글로벌 스마트 양봉 시장 진출 기반 마련 (동남아, 아프리카)
- 농업 IoT 기술의 타 축산·농업 분야 확장 가능성

---

*본 사업계획서는 2026년 예비창업패키지 신청을 위해 작성되었습니다.*
*BeeKeep AI - 기술로 지키는 꿀벌, 데이터로 키우는 양봉*
