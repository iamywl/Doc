# AI 식당위생 HACCP 자동관리 솔루션 - 클린키친(CleanKitchen)

## 1. 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | 클린키친(CleanKitchen) |
| 대표자 | 김하은 (컴퓨터공학과 4학년) |
| 팀구성 | 대표 1명, AI개발 2명, 백엔드 1명, 프론트엔드 1명, IoT 1명, 식품안전전문가 1명 (총 7명) |
| 창업아이템 | AI 기반 식당/급식소 위생관리 자동화 및 HACCP 준수 솔루션 |
| 분야 | AI / 식품안전 / SaaS |
| 사업화단계 | MVP 개발 완료 → 실증 테스트 진행 중 |
| 목표시장 | 국내 외식업 위생관리 및 HACCP 인증 관리 솔루션 시장 |
| 신청금액 | 10,000만원 (1억원) |

---

## 2. 창업 아이템 개요

| 구분 | 내용 |
|------|------|
| 아이템명 | 클린키친(CleanKitchen) - AI HACCP 자동관리 플랫폼 |
| 핵심기술 | 컴퓨터비전 위생 감시, IoT 온도 자동 모니터링, HACCP 서류 자동화 |
| 서비스형태 | B2B SaaS + IoT 센서 키트 + AI 영상분석 |
| 목표고객 | 프랜차이즈 외식업체, 단체급식소, HACCP 인증 대상 업소 |
| 차별성 | HACCP 서류작업 90% 자동화, 영상AI 위생위반 실시간 감지, 식중독 예방 |
| 특허현황 | 출원 2건 (AI 주방위생 감시 시스템, HACCP 자동 문서화 방법) |

---

## 3. 문제 인식

### 3.1 식품위생 현황 통계

| 지표 | 수치 | 출처 |
|------|------|------|
| 국내 외식업체 수 (2025) | 약 74만 개소 | 식품의약품안전처 |
| 연간 식중독 발생 건수 | 약 350건 (환자 6,000명+) | 식약처 식중독 통계 |
| 식중독 1건당 평균 피해액 | 약 4,800만원 | 한국식품안전관리인증원 |
| HACCP 의무 적용 업소 | 약 12만 개소 | 식약처 |
| HACCP 서류작업 소요 시간 | 일 평균 2~3시간/업소 | 업계 설문 |
| 위생 점검 행정처분 업소 (연간) | 약 28,000건 | 식약처 |
| 급식소 위생관리 부적합률 | 18.3% | 교육부 급식 실태조사 |
| 외식업 폐업률 (연간) | 약 25% | 소상공인시장진흥공단 |
| 식품안전 관련 소비자 불만 | 연간 45,000건+ | 소비자원 |
| 조리원 위생교육 이수율 | 72.4% | 한국식품산업협회 |

### 3.2 이해관계자 인터뷰

> "HACCP 서류를 매일 수기로 작성하는 데 2시간 이상 걸립니다. 조리에 집중할 시간이 부족합니다."
> — 프랜차이즈 급식업체 조리장 (경력 12년)

> "50개 지점의 위생 상태를 관리하려면 슈퍼바이저를 여러 명 둬야 하는데 인건비가 큰 부담입니다."
> — 프랜차이즈 외식업체 품질관리 본부장

> "냉장고 온도가 밤사이 올라가서 식재료가 상한 적이 있습니다. 24시간 모니터링이 안 되니 발견이 늦습니다."
> — 학교 급식실 영양사

> "위생 점검 때만 잠깐 정리하고, 평소에는 관리가 안 되는 업소가 많습니다. 상시 관리 체계가 필요합니다."
> — 지자체 식품위생 점검관

### 3.3 식당 위생관리 문제 흐름도

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 480">
  <rect width="800" height="480" fill="#f5f5f5" rx="10"/>
  <text x="400" y="35" text-anchor="middle" font-size="18" font-weight="bold" fill="#333">외식업 위생관리 Pain Point 분석</text>

  <!-- 문제 카테고리 -->
  <rect x="30" y="60" width="230" height="180" fill="#ffebee" rx="8" stroke="#ef9a9a"/>
  <text x="145" y="85" text-anchor="middle" font-size="13" font-weight="bold" fill="#c62828">수기 관리 문제</text>
  <text x="50" y="110" font-size="11" fill="#555">- HACCP 일지 수기 작성 (2h/일)</text>
  <text x="50" y="130" font-size="11" fill="#555">- 온도 기록 누락/위조</text>
  <text x="50" y="150" font-size="11" fill="#555">- 서류 보관 관리 부담</text>
  <text x="50" y="170" font-size="11" fill="#555">- 실시간 데이터 부재</text>
  <text x="50" y="190" font-size="11" fill="#555">- 인증 심사 대비 부담</text>
  <text x="50" y="215" font-size="12" font-weight="bold" fill="#c62828">영향: 인력 낭비, 데이터 신뢰도↓</text>

  <rect x="285" y="60" width="230" height="180" fill="#fff3e0" rx="8" stroke="#ffcc80"/>
  <text x="400" y="85" text-anchor="middle" font-size="13" font-weight="bold" fill="#e65100">감시 사각지대</text>
  <text x="305" y="110" font-size="11" fill="#555">- 야간 냉장고 온도 무관리</text>
  <text x="305" y="130" font-size="11" fill="#555">- 조리원 위생수칙 미준수</text>
  <text x="305" y="150" font-size="11" fill="#555">- 교차오염 미감지</text>
  <text x="305" y="170" font-size="11" fill="#555">- 해충 침입 사후 발견</text>
  <text x="305" y="190" font-size="11" fill="#555">- 식재료 유통기한 놓침</text>
  <text x="305" y="215" font-size="12" font-weight="bold" fill="#e65100">영향: 식중독 사고 위험↑</text>

  <rect x="540" y="60" width="230" height="180" fill="#e8eaf6" rx="8" stroke="#9fa8da"/>
  <text x="655" y="85" text-anchor="middle" font-size="13" font-weight="bold" fill="#283593">다점포 관리 한계</text>
  <text x="560" y="110" font-size="11" fill="#555">- 지점별 위생 편차 심함</text>
  <text x="560" y="130" font-size="11" fill="#555">- 현장 방문 점검 비용</text>
  <text x="560" y="150" font-size="11" fill="#555">- 표준화된 관리 어려움</text>
  <text x="560" y="170" font-size="11" fill="#555">- 본사 실시간 파악 불가</text>
  <text x="560" y="190" font-size="11" fill="#555">- 위반 발생 시 늦은 대응</text>
  <text x="560" y="215" font-size="12" font-weight="bold" fill="#283593">영향: 브랜드 리스크↑</text>

  <!-- 결과 -->
  <line x1="145" y1="240" x2="400" y2="290" stroke="#999" stroke-width="2"/>
  <line x1="400" y1="240" x2="400" y2="290" stroke="#999" stroke-width="2"/>
  <line x1="655" y1="240" x2="400" y2="290" stroke="#999" stroke-width="2"/>

  <rect x="200" y="290" width="400" height="60" fill="#d32f2f" rx="10"/>
  <text x="400" y="317" text-anchor="middle" font-size="14" font-weight="bold" fill="white">결과: 식중독 사고, 행정처분, 폐업</text>
  <text x="400" y="337" text-anchor="middle" font-size="11" fill="#ffcdd2">연간 식중독 350건, 행정처분 28,000건, 폐업률 25%</text>

  <!-- 솔루션 -->
  <line x1="400" y1="350" x2="400" y2="380" stroke="#4caf50" stroke-width="3"/>
  <polygon points="390,380 410,380 400,395" fill="#4caf50"/>
  <rect x="200" y="400" width="400" height="60" fill="#2e7d32" rx="10"/>
  <text x="400" y="425" text-anchor="middle" font-size="14" font-weight="bold" fill="white">클린키친 솔루션</text>
  <text x="400" y="445" text-anchor="middle" font-size="11" fill="#c8e6c9">AI 영상감시 + IoT 자동기록 + HACCP 자동화</text>
</svg>
```

---

## 4. 솔루션

### 4.1 핵심 기능

| 기능 | 설명 |
|------|------|
| AI 위생 영상 감시 | 조리복/위생모/장갑 착용, 손씻기, 교차오염 등 컴퓨터비전 실시간 감지 |
| IoT 온도 자동 모니터링 | 냉장/냉동고, 조리 온도, 배식 온도 24시간 자동 기록 및 이상 알림 |
| HACCP 서류 자동화 | 센서 데이터 기반 CCP 기록지, 위생 점검표 자동 생성 및 보관 |
| 식재료 유통기한 관리 | 바코드/OCR 기반 식재료 입고-사용-폐기 자동 추적 |
| 실시간 대시보드 | 다점포 위생 현황 실시간 모니터링, 위반 즉시 알림 |
| 위생 등급 스코어링 | AI 기반 업소별 위생 점수 산출, 개선 사항 자동 제안 |
| 교육 관리 | 조리원별 위생교육 이수 현황 관리, 맞춤형 교육 콘텐츠 |
| 행정 대응 지원 | 위생 점검 대비 서류 자동 준비, 인증 심사 지원 |

### 4.2 시스템 아키텍처

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 850 520">
  <rect width="850" height="520" fill="#fafafa" rx="10"/>
  <text x="425" y="30" text-anchor="middle" font-size="16" font-weight="bold" fill="#333">클린키친 시스템 아키텍처</text>

  <!-- 현장 데이터 수집 -->
  <rect x="30" y="50" width="790" height="100" fill="#e8f5e9" rx="8" stroke="#66bb6a"/>
  <text x="425" y="70" text-anchor="middle" font-size="13" font-weight="bold" fill="#2e7d32">현장 데이터 수집 레이어</text>
  <rect x="50" y="85" width="130" height="50" fill="#43a047" rx="6"/>
  <text x="115" y="106" text-anchor="middle" font-size="10" fill="white">AI 카메라</text>
  <text x="115" y="122" text-anchor="middle" font-size="9" fill="#c8e6c9">위생행위 감시</text>
  <rect x="200" y="85" width="130" height="50" fill="#43a047" rx="6"/>
  <text x="265" y="106" text-anchor="middle" font-size="10" fill="white">온도 센서</text>
  <text x="265" y="122" text-anchor="middle" font-size="9" fill="#c8e6c9">냉장/조리/배식</text>
  <rect x="350" y="85" width="130" height="50" fill="#43a047" rx="6"/>
  <text x="415" y="106" text-anchor="middle" font-size="10" fill="white">습도 센서</text>
  <text x="415" y="122" text-anchor="middle" font-size="9" fill="#c8e6c9">환경 모니터링</text>
  <rect x="500" y="85" width="130" height="50" fill="#43a047" rx="6"/>
  <text x="565" y="106" text-anchor="middle" font-size="10" fill="white">바코드 스캐너</text>
  <text x="565" y="122" text-anchor="middle" font-size="9" fill="#c8e6c9">식재료 입출고</text>
  <rect x="650" y="85" width="140" height="50" fill="#43a047" rx="6"/>
  <text x="720" y="106" text-anchor="middle" font-size="10" fill="white">해충 트랩 센서</text>
  <text x="720" y="122" text-anchor="middle" font-size="9" fill="#c8e6c9">해충 감지</text>

  <!-- 엣지 처리 -->
  <rect x="200" y="170" width="450" height="50" fill="#fff3e0" rx="8" stroke="#ffb74d"/>
  <text x="425" y="200" text-anchor="middle" font-size="12" font-weight="bold" fill="#e65100">엣지 컴퓨팅 게이트웨이 (온프레미스 AI 추론)</text>

  <!-- 클라우드 AI -->
  <rect x="30" y="240" width="790" height="110" fill="#e3f2fd" rx="8" stroke="#42a5f5"/>
  <text x="425" y="260" text-anchor="middle" font-size="13" font-weight="bold" fill="#1565c0">클라우드 AI 분석 플랫폼</text>
  <rect x="50" y="275" width="170" height="60" fill="#1976d2" rx="6"/>
  <text x="135" y="300" text-anchor="middle" font-size="10" fill="white">위생 위반 AI 감지</text>
  <text x="135" y="316" text-anchor="middle" font-size="9" fill="#bbdefb">YOLOv8 + Pose Est.</text>
  <rect x="240" y="275" width="170" height="60" fill="#1976d2" rx="6"/>
  <text x="325" y="300" text-anchor="middle" font-size="10" fill="white">온도 이상 분석</text>
  <text x="325" y="316" text-anchor="middle" font-size="9" fill="#bbdefb">시계열 이상탐지</text>
  <rect x="430" y="275" width="170" height="60" fill="#1976d2" rx="6"/>
  <text x="515" y="300" text-anchor="middle" font-size="10" fill="white">HACCP 문서 생성</text>
  <text x="515" y="316" text-anchor="middle" font-size="9" fill="#bbdefb">LLM 기반 자동화</text>
  <rect x="620" y="275" width="170" height="60" fill="#1976d2" rx="6"/>
  <text x="705" y="300" text-anchor="middle" font-size="10" fill="white">위생 스코어링</text>
  <text x="705" y="316" text-anchor="middle" font-size="9" fill="#bbdefb">멀티팩터 평가</text>

  <!-- 서비스 -->
  <rect x="30" y="370" width="790" height="60" fill="#fce4ec" rx="8" stroke="#f48fb1"/>
  <text x="425" y="395" text-anchor="middle" font-size="12" font-weight="bold" fill="#c2185b">API / 연동 레이어</text>
  <text x="425" y="415" text-anchor="middle" font-size="10" fill="#880e4f">식약처 연동 | 식재료 공급업체 | POS 시스템 | 위생교육 플랫폼</text>

  <!-- 사용자 -->
  <rect x="30" y="450" width="790" height="55" fill="#f3e5f5" rx="8" stroke="#ab47bc"/>
  <text x="160" y="482" text-anchor="middle" font-size="11" fill="#6a1b9a">매장 관리자 앱</text>
  <text x="350" y="482" text-anchor="middle" font-size="11" fill="#6a1b9a">본사 대시보드</text>
  <text x="530" y="482" text-anchor="middle" font-size="11" fill="#6a1b9a">영양사 포털</text>
  <text x="710" y="482" text-anchor="middle" font-size="11" fill="#6a1b9a">점검관 모바일</text>

  <!-- 연결선 -->
  <line x1="425" y1="150" x2="425" y2="170" stroke="#999" stroke-width="2" stroke-dasharray="5,3"/>
  <line x1="425" y1="220" x2="425" y2="240" stroke="#999" stroke-width="2" stroke-dasharray="5,3"/>
  <line x1="425" y1="350" x2="425" y2="370" stroke="#999" stroke-width="2" stroke-dasharray="5,3"/>
  <line x1="425" y1="430" x2="425" y2="450" stroke="#999" stroke-width="2" stroke-dasharray="5,3"/>
</svg>
```

### 4.3 기술 스택

| 분류 | 기술 | 용도 |
|------|------|------|
| 컴퓨터비전 | YOLOv8, MediaPipe Pose | 위생복 착용 감지, 손씻기 동작 인식 |
| AI/ML | PyTorch, TensorFlow | 위생위반 분류 모델, 이상탐지 |
| LLM | GPT-4 API, 파인튜닝 모델 | HACCP 문서 자동 생성, 개선사항 작성 |
| IoT | ESP32, DS18B20(온도), BME280(습도) | 환경 센서 데이터 수집 |
| 엣지 | NVIDIA Jetson Nano, TensorRT | 현장 실시간 AI 추론 |
| 백엔드 | FastAPI (Python), PostgreSQL | API 서버, 데이터 관리 |
| 프론트엔드 | Next.js, Flutter | 웹 대시보드, 모바일 앱 |
| 클라우드 | AWS (ECS, RDS, S3, IoT Core) | 인프라 운영 |
| 메시지 | RabbitMQ, WebSocket | 실시간 알림, 이벤트 처리 |

---

## 5. 시장 분석

### 5.1 시장 규모 (TAM/SAM/SOM)

| 시장 구분 | 규모 | 산출 근거 |
|-----------|------|-----------|
| TAM (전체 시장) | 1조 2,000억원 | 글로벌 식품안전 관리 소프트웨어 시장 중 아시아 |
| SAM (유효 시장) | 2,800억원 | 국내 외식업 위생관리 솔루션 시장 |
| SOM (목표 시장) | 95억원 | 초기 3년 내 AI HACCP 관리 솔루션 점유 목표 |

### 5.2 경쟁사 분석

| 경쟁사 | 유형 | 강점 | 약점 | 차별점 |
|--------|------|------|------|--------|
| 해썹코리아 | HACCP 컨설팅 | 인증 노하우, 네트워크 | SW 미약, 수기 중심 | 수동 컨설팅 vs AI 자동화 |
| 식신 | 외식 데이터 | 매출 데이터, 시장 점유 | 위생관리 기능 없음 | 매출 분석 vs 위생 특화 |
| Squadle (미국) | 식품안전 SaaS | 글로벌 사례, 기술력 | 국내 미진출, 현지화 없음 | 글로벌 vs 한국 법규 최적화 |
| 식품안전나라 | 정부 포털 | 무료, 공공 데이터 | UX 열악, 자동화 없음 | 정보 제공 vs 자동 관리 |
| 자체 위생관리 | 사내 시스템 | 커스터마이징 | 개발/유지비 고가 | 고비용 vs SaaS 합리적 가격 |

---

## 6. 비즈니스 모델

### 6.1 수익 구조

| 수익원 | 내용 | 예상 비중 |
|--------|------|-----------|
| SaaS 구독료 | 매장당 월 10~40만원 (규모/기능별) | 50% |
| IoT 하드웨어 판매 | 센서 키트 + AI 카메라 (매장당 150~400만원) | 25% |
| 인증 컨설팅 | HACCP 신규 인증 및 갱신 심사 지원 | 15% |
| 데이터 분석 | 업종별 위생 벤치마킹 리포트, 프리미엄 분석 | 10% |

### 6.2 3개년 재무 계획

| 구분 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 매출액 | 4억원 | 18억원 | 50억원 |
| 영업비용 | 9억원 | 16억원 | 30억원 |
| 영업이익 | -5억원 | 2억원 | 20억원 |
| 고객 매장 수 | 80개 | 400개 | 1,200개 |
| MRR (월간반복매출) | 2,000만원 | 1억원 | 3억원 |
| 구독 유지율 | 82% | 88% | 93% |

---

## 7. 실행 계획

### 7.1 4단계 로드맵

| 단계 | 기간 | 핵심 목표 | 주요 활동 |
|------|------|-----------|-----------|
| Phase 1 | 1~3개월 | 제품 완성 | AI 위생감시 모델 정확도 95% 달성, IoT 키트 양산, HACCP 문서 템플릿 구축 |
| Phase 2 | 4~6개월 | 실증 테스트 | 프랜차이즈 본사 2곳 파일럿 (20개 매장), 급식소 5곳 시범 운영 |
| Phase 3 | 7~9개월 | 상용화 | 정식 서비스 출시, 프랜차이즈 B2B 영업, 식약처 연계 추진 |
| Phase 4 | 10~12개월 | 시장 확대 | 전국 확장, 학교급식 진출, HACCP 인증기관 공식 연동 |

### 7.2 팀 구성

| 역할 | 인원 | 주요 역량 | 담당 업무 |
|------|------|-----------|-----------|
| CEO/PM | 1명 | 컴퓨터비전, 사업 기획 | 전략 수립, 영업 총괄 |
| AI 엔지니어 | 2명 | 컴퓨터비전, 자연어처리 | 위생 감지 AI, HACCP 문서 자동화 |
| 백엔드 개발자 | 1명 | 클라우드, API 개발 | 서버/플랫폼 개발 |
| 프론트엔드 개발자 | 1명 | React, Flutter | 대시보드/앱 개발 |
| IoT 개발자 | 1명 | 임베디드, 센서 | 센서 키트 개발, 엣지 AI |
| 식품안전 전문가 | 1명 | HACCP 심사원 자격, 식품공학 | HACCP 체계 설계, 컨설팅 |

---

## 8. 리스크 관리

| 리스크 | 발생확률 | 영향도 | 대응 방안 |
|--------|----------|--------|-----------|
| 주방 카메라 프라이버시 이슈 | 높음 | 높음 | 얼굴 비식별 처리, 위생 행위만 감지, 영상 미저장(메타데이터만) |
| AI 오탐지 (장갑 미착용 등) | 중 | 높음 | 지속 학습, 다양한 주방 환경 데이터 확보, 오탐 피드백 루프 |
| 고온/습도 환경 센서 내구성 | 중 | 중 | IP67 방수, 내열 설계, 주기적 교체 프로그램 |
| HACCP 법규 변경 | 낮음 | 높음 | 식약처 법규 모니터링 전담, 즉시 시스템 업데이트 |
| 기존 관행 저항 (수기 선호) | 높음 | 중 | 단계적 도입, 직관적 UX, 현장 교육 프로그램 |
| 프랜차이즈 본사 의사결정 지연 | 중 | 중 | 파일럿 ROI 입증, 경영진 대상 데모, 경쟁사 도입 사례 활용 |

---

## 9. 사회적 가치

### 9.1 기대 사회적 효과

| 영역 | 효과 |
|------|------|
| 식품 안전 | 식중독 발생률 감소, 국민 건강 보호 |
| 외식업 경영 | HACCP 관리 비용 절감, 소상공인 행정 부담 해소 |
| 급식 안전 | 학교/병원/군 급식 위생수준 표준화 |
| 일자리 | AI 식품안전 전문가 양성, 위생관리 고도화 일자리 창출 |
| 투명성 | 식당 위생 데이터 공개로 소비자 알권리 보장 |

### 9.2 ESG 기여

- **Environmental**: 식재료 낭비 감소 (유통기한 자동관리), 적정 냉장 온도로 에너지 절약
- **Social**: 식중독 예방으로 국민건강 보호, 취약계층 급식 안전 강화
- **Governance**: 식품 안전 데이터 투명성, HACCP 준수율 향상

---

## 10. 자금 운용 계획

| 항목 | 금액(만원) | 비율 | 세부 내용 |
|------|-----------|------|-----------|
| AI 모델 개발 | 2,500 | 25% | 위생감시 AI, HACCP 문서 자동화 LLM, 학습 데이터 구축 |
| IoT 하드웨어 | 1,800 | 18% | 센서 키트 설계/양산, AI 카메라 모듈, 엣지 디바이스 |
| 소프트웨어 개발 | 2,000 | 20% | 클라우드 플랫폼, 대시보드, 모바일 앱 |
| 실증 테스트 | 1,000 | 10% | 파일럿 매장 설치, 시범 운영, 데이터 수집 |
| 인건비 | 1,500 | 15% | 핵심 인력 인건비 보조 (6개월) |
| 인증/법률 | 500 | 5% | 특허 출원, 법률 자문, 식품안전 인증 |
| 마케팅/영업 | 400 | 4% | B2B 영업, 전시회, 프랜차이즈 본사 대상 세미나 |
| 운영비 | 300 | 3% | 사무실, 클라우드 비용, 기타 |
| **합계** | **10,000** | **100%** | |

---

## 11. 기대 효과

### 11.1 정량적 기대 효과

| 지표 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 관리 매장 수 | 80개 | 400개 | 1,200개 |
| HACCP 서류작업 시간 절감 | 80% | 88% | 92% |
| 위생위반 감지 정확도 | 90% | 94% | 97% |
| 도입 매장 식중독 발생률 | 기존 대비 -60% | -75% | -85% |
| 행정처분 감소율 | 50% | 70% | 85% |
| 고용 창출 | 10명 | 22명 | 40명 |

### 11.2 정성적 기대 효과

- 국내 외식업 위생관리 패러다임을 '사후 점검'에서 '실시간 예방'으로 전환
- HACCP 인증의 실질적 이행 수준 향상
- 식품안전에 대한 소비자 신뢰도 제고
- K-푸드 해외 진출 시 위생관리 체계 표준화 기여
- 프랜차이즈 업계 위생관리 디지털 전환 선도

---

> **"클린키친은 주방의 보이지 않는 곳까지 AI의 눈으로 지켜봅니다. 깨끗한 주방이 곧 건강한 식탁입니다."**
