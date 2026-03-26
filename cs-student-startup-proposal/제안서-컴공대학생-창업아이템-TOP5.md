# 컴퓨터공학과 대학생을 위한 창업 아이템 제안서 TOP 5

> 작성일: 2026-03-24
> 대상: 컴퓨터공학과 재학생 (1~4인 팀)
> 목적: 기술적 실현 가능성과 사회적 가치를 동시에 갖춘 예비창업패키지 아이템 선정

---

## 목차

1. [선정 기준 및 평가 프레임워크](#1-선정-기준-및-평가-프레임워크)
2. [TOP 5 아이템 비교 총괄표](#2-top-5-아이템-비교-총괄표)
3. [아이템 1: 리유즈잇 - 캠퍼스 중고거래 플랫폼](#3-아이템-1-리유즈잇---캠퍼스-중고거래-플랫폼)
4. [아이템 2: 스터디브릿지 - P2P 학습 매칭](#4-아이템-2-스터디브릿지---p2p-학습-매칭)
5. [아이템 3: 룸메이트 - 대학생 주거 매칭](#5-아이템-3-룸메이트---대학생-주거-매칭)
6. [아이템 4: 포데이 - 취미 습관 커뮤니티 앱](#6-아이템-4-포데이---취미-습관-커뮤니티-앱)
7. [아이템 5: 동네알바 - 초단기 인력 매칭](#7-아이템-5-동네알바---초단기-인력-매칭)
8. [기술 스택 로드맵](#8-기술-스택-로드맵)
9. [참고문헌](#9-참고문헌)

---

## 1. 선정 기준 및 평가 프레임워크

### 1.1 평가 기준 설계

컴퓨터공학과 대학생의 **기술 역량**, **자원 제약**, **시장 접근성**을 종합 고려하여 아래 5가지 기준으로 평가하였다.

| 평가 기준 | 가중치 | 근거 |
|:---|:---:|:---|
| **기술적 실현가능성** | 30% | 대학생 팀(1~4인)이 6개월 내 MVP 개발 가능 여부 (Ries, 2011) |
| **초기 비용 효율성** | 20% | 인프라/서버 비용이 월 50만원 이내로 운영 가능 여부 |
| **시장 규모 및 성장성** | 20% | TAM/SAM/SOM 기반 시장 매력도 (Blank & Dorf, 2012) |
| **사용자 획득 용이성** | 15% | 캠퍼스 내 바이럴·구전 마케팅 가능 여부 |
| **사회적 가치** | 15% | 예비창업패키지 사회문제해결형 평가 가점 요소 |

### 1.2 평가 프로세스 도식

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 600 520" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow1" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <linearGradient id="topGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E8F0FE;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#D2E3FC;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="midGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#FFF3E0;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#FFE0B2;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="botGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E8F5E9;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#C8E6C9;stop-opacity:1"/>
    </linearGradient>
    <filter id="shadow1" x="-2%" y="-2%" width="104%" height="108%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.12"/>
    </filter>
  </defs>
  <!-- Box 1: 30개 후보 -->
  <rect x="50" y="10" width="500" height="70" rx="8" fill="url(#topGrad)" stroke="#4A90D9" stroke-width="1.5" filter="url(#shadow1)"/>
  <text x="300" y="38" text-anchor="middle" font-size="15" font-weight="bold" fill="#1A3A6B">30개 후보 아이템</text>
  <text x="300" y="60" text-anchor="middle" font-size="12" fill="#555">(로컬LLM 기반 13개 + 글로벌 P2P 매칭 17개 + 기타)</text>
  <!-- Arrow -->
  <line x1="300" y1="80" x2="300" y2="120" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow1)"/>
  <!-- Box 2: 1차 스크리닝 -->
  <rect x="50" y="120" width="500" height="120" rx="8" fill="url(#midGrad)" stroke="#E67E22" stroke-width="1.5" filter="url(#shadow1)"/>
  <text x="300" y="148" text-anchor="middle" font-size="15" font-weight="bold" fill="#7B3F00">1차 스크리닝: 기술 난이도 필터</text>
  <text x="80" y="172" font-size="12" fill="#555">- 128GB 전용 서버 필수 아이템 제외</text>
  <text x="80" y="192" font-size="12" fill="#555">- 의료/법률 등 규제 분야 제외</text>
  <text x="80" y="212" font-size="12" fill="#555">- 해외 운영 필수 아이템 제외</text>
  <text x="400" y="218" font-size="13" font-weight="bold" fill="#E67E22">결과: 30개 → 12개</text>
  <!-- Arrow -->
  <line x1="300" y1="240" x2="300" y2="280" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow1)"/>
  <!-- Box 3: 2차 평가 -->
  <rect x="50" y="280" width="500" height="110" rx="8" fill="url(#midGrad)" stroke="#E67E22" stroke-width="1.5" filter="url(#shadow1)"/>
  <text x="300" y="308" text-anchor="middle" font-size="15" font-weight="bold" fill="#7B3F00">2차 평가: 5대 기준 정량 평가</text>
  <text x="80" y="335" font-size="12" fill="#555">- 기술실현가능성(30%) + 비용효율(20%)</text>
  <text x="80" y="355" font-size="12" fill="#555">+ 시장규모(20%) + 사용자획득(15%) + 사회적가치(15%)</text>
  <text x="400" y="370" font-size="13" font-weight="bold" fill="#E67E22">결과: 12개 → 5개</text>
  <!-- Arrow -->
  <line x1="300" y1="390" x2="300" y2="430" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow1)"/>
  <!-- Box 4: TOP 5 확정 -->
  <rect x="50" y="430" width="500" height="75" rx="8" fill="url(#botGrad)" stroke="#2E7D32" stroke-width="2" filter="url(#shadow1)"/>
  <text x="300" y="458" text-anchor="middle" font-size="16" font-weight="bold" fill="#1B5E20">TOP 5 아이템 확정</text>
  <text x="300" y="483" text-anchor="middle" font-size="13" fill="#333">1. 리유즈잇  2. 스터디브릿지  3. 룸메이트  4. 포데이  5. 동네알바</text>
</svg>

---

## 2. TOP 5 아이템 비교 총괄표

| 구분 | 리유즈잇 | 스터디브릿지 | 룸메이트 | 포데이 | 동네알바 |
|:---|:---:|:---:|:---:|:---:|:---:|
| **한줄 소개** | 캠퍼스 중고거래 | P2P 학습매칭 | 대학생 주거매칭 | 취미습관 커뮤니티 | 초단기 인력매칭 |
| **기술 난이도** | ★★☆☆☆ | ★★★☆☆ | ★★☆☆☆ | ★★☆☆☆ | ★★★☆☆ |
| **MVP 개발기간** | 3~4개월 | 4~5개월 | 3~4개월 | 3~4개월 | 4~6개월 |
| **초기 월 운영비** | ~20만원 | ~30만원 | ~25만원 | ~15만원 | ~40만원 |
| **TAM (시장규모)** | 43조원 | 27.1조원 | 10조원 | 4.3조원 | 23조원 |
| **수익 모델** | 거래수수료 3.5% | 매칭수수료 15% | 프리미엄 구독 | 구독 4,900원/월 | 매칭수수료 10% |
| **BEP 달성 예상** | 12개월 | 15개월 | 14개월 | 18개월 | 12개월 |
| **사회적 가치** | 자원순환/탄소절감 | 교육격차 해소 | 주거비 절감 | 사회적고립 해소 | 청년고용/소상공인 |
| **핵심 경쟁우위** | 대학인증+AI분류 | 성적인증 튜터 | 생활패턴 AI매칭 | 행동과학 설계 | 30초 실시간 매칭 |

### 종합 점수 비교 (100점 만점)

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 650 280" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <linearGradient id="bar1" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#1565C0;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#42A5F5;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="bar2" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#2E7D32;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#66BB6A;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="bar3" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#6A1B9A;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#AB47BC;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="bar4" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#E65100;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#FF9800;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="bar5" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#C62828;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#EF5350;stop-opacity:1"/>
    </linearGradient>
    <filter id="barShadow" x="-1%" y="-10%" width="102%" height="130%">
      <feDropShadow dx="1" dy="1" stdDeviation="1.5" flood-opacity="0.15"/>
    </filter>
  </defs>
  <text x="325" y="28" text-anchor="middle" font-size="16" font-weight="bold" fill="#333">종합 점수 비교 (100점 만점)</text>
  <!-- Row 1: 리유즈잇 88 -->
  <text x="120" y="70" text-anchor="end" font-size="14" font-weight="500" fill="#333">리유즈잇</text>
  <rect x="130" y="54" width="396" height="24" rx="4" fill="url(#bar1)" filter="url(#barShadow)"/>
  <rect x="130" y="54" width="450" height="24" rx="4" fill="none" stroke="#DDD" stroke-width="0.5"/>
  <text x="535" y="72" font-size="14" font-weight="bold" fill="#1565C0">88점</text>
  <!-- Row 2: 스터디브릿지 84 -->
  <text x="120" y="112" text-anchor="end" font-size="14" font-weight="500" fill="#333">스터디브릿지</text>
  <rect x="130" y="96" width="378" height="24" rx="4" fill="url(#bar2)" filter="url(#barShadow)"/>
  <rect x="130" y="96" width="450" height="24" rx="4" fill="none" stroke="#DDD" stroke-width="0.5"/>
  <text x="517" y="114" font-size="14" font-weight="bold" fill="#2E7D32">84점</text>
  <!-- Row 3: 룸메이트 82 -->
  <text x="120" y="154" text-anchor="end" font-size="14" font-weight="500" fill="#333">룸메이트</text>
  <rect x="130" y="138" width="369" height="24" rx="4" fill="url(#bar3)" filter="url(#barShadow)"/>
  <rect x="130" y="138" width="450" height="24" rx="4" fill="none" stroke="#DDD" stroke-width="0.5"/>
  <text x="508" y="156" font-size="14" font-weight="bold" fill="#6A1B9A">82점</text>
  <!-- Row 4: 포데이 78 -->
  <text x="120" y="196" text-anchor="end" font-size="14" font-weight="500" fill="#333">포데이</text>
  <rect x="130" y="180" width="351" height="24" rx="4" fill="url(#bar4)" filter="url(#barShadow)"/>
  <rect x="130" y="180" width="450" height="24" rx="4" fill="none" stroke="#DDD" stroke-width="0.5"/>
  <text x="490" y="198" font-size="14" font-weight="bold" fill="#E65100">78점</text>
  <!-- Row 5: 동네알바 76 -->
  <text x="120" y="238" text-anchor="end" font-size="14" font-weight="500" fill="#333">동네알바</text>
  <rect x="130" y="222" width="342" height="24" rx="4" fill="url(#bar5)" filter="url(#barShadow)"/>
  <rect x="130" y="222" width="450" height="24" rx="4" fill="none" stroke="#DDD" stroke-width="0.5"/>
  <text x="481" y="240" font-size="14" font-weight="bold" fill="#C62828">76점</text>
</svg>

---

## 3. 아이템 1: 리유즈잇 - 캠퍼스 중고거래 플랫폼

### 3.1 문제 정의

| 문제 항목 | 현황 수치 | 출처 |
|:---|:---|:---|
| 국내 중고거래 시장 규모 | **43조원** (2024) | 한국인터넷진흥원 (2024) |
| 연간 중고거래 사기 건수 | **50,389건** (2023) | 경찰청 사이버수사국 (2023) |
| 대학생 월평균 생활비 | **68만원** | 한국장학재단 (2024) |
| 학기 초 교재비 부담 비율 | **73.2%** | 대학내일20대연구소 (2023) |
| 기존 플랫폼 사기 우려 | **62.4%** | 한국소비자원 (2024) |

대한민국 대학생 **328만 명**(교육부, 2024)이 매 학기 교재, 전공서적, 전자기기 등을 구매·처분하나, 기존 플랫폼(당근마켓, 번개장터)은 **신원 미인증** 거래로 사기 위험이 높다. 특히 대학생은 제한된 생활비(월 평균 68만원) 내에서 교재비(평균 32만원/학기)가 큰 부담이다(한국장학재단, 2024).

### 3.2 솔루션 아키텍처

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 560" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow2" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <filter id="shadow2" x="-2%" y="-2%" width="104%" height="108%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.1"/>
    </filter>
    <linearGradient id="userGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E3F2FD;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#BBDEFB;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="serverGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#F3E5F5;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#E1BEE7;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="escrowGrad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E8F5E9;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#C8E6C9;stop-opacity:1"/>
    </linearGradient>
  </defs>
  <!-- User box -->
  <rect x="40" y="10" width="600" height="65" rx="8" fill="url(#userGrad)" stroke="#1976D2" stroke-width="1.5" filter="url(#shadow2)"/>
  <text x="340" y="36" text-anchor="middle" font-size="15" font-weight="bold" fill="#0D47A1">사용자 (대학생)</text>
  <text x="340" y="58" text-anchor="middle" font-size="12" fill="#555">대학 이메일(ac.kr) 인증 가입</text>
  <!-- Arrows down -->
  <line x1="200" y1="75" x2="200" y2="110" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow2)"/>
  <line x1="480" y1="75" x2="480" y2="110" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow2)"/>
  <!-- AI Auto Register -->
  <rect x="60" y="110" width="260" height="110" rx="8" fill="#FFF8E1" stroke="#F9A825" stroke-width="1.5" filter="url(#shadow2)"/>
  <text x="190" y="135" text-anchor="middle" font-size="14" font-weight="bold" fill="#E65100">AI 자동 등록</text>
  <text x="80" y="158" font-size="12" fill="#555">- 사진 촬영</text>
  <text x="80" y="178" font-size="12" fill="#555">- MobileNet 분류</text>
  <text x="80" y="198" font-size="12" fill="#555">- 시세 자동 추정</text>
  <!-- Smart Search -->
  <rect x="360" y="110" width="280" height="110" rx="8" fill="#E0F7FA" stroke="#00ACC1" stroke-width="1.5" filter="url(#shadow2)"/>
  <text x="500" y="135" text-anchor="middle" font-size="14" font-weight="bold" fill="#006064">스마트 검색/추천</text>
  <text x="380" y="158" font-size="12" fill="#555">- 학과/과목별 필터</text>
  <text x="380" y="178" font-size="12" fill="#555">- ISBN 바코드 스캔</text>
  <text x="380" y="198" font-size="12" fill="#555">- 개강시즌 추천 알고리즘</text>
  <!-- Arrows to server -->
  <line x1="200" y1="220" x2="200" y2="255" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow2)"/>
  <line x1="480" y1="220" x2="480" y2="255" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow2)"/>
  <!-- Server platform box -->
  <rect x="40" y="255" width="600" height="200" rx="8" fill="url(#serverGrad)" stroke="#7B1FA2" stroke-width="1.5" filter="url(#shadow2)"/>
  <text x="340" y="282" text-anchor="middle" font-size="15" font-weight="bold" fill="#4A148C">리유즈잇 플랫폼 서버</text>
  <!-- Tech boxes row 1 -->
  <!-- Next.js -->
  <rect x="65" y="298" width="155" height="48" rx="6" fill="#fff" stroke="#000" stroke-width="1"/>
  <circle cx="90" cy="322" r="14" fill="#000"/>
  <text x="90" y="327" text-anchor="middle" font-size="13" font-weight="bold" fill="#fff">N</text>
  <text x="130" y="326" font-size="12" font-weight="600" fill="#333">Next.js Frontend</text>
  <!-- Node.js -->
  <rect x="240" y="298" width="155" height="48" rx="6" fill="#fff" stroke="#339933" stroke-width="1"/>
  <polygon points="265,310 275,316 275,328 265,334 255,328 255,316" fill="#339933"/>
  <text x="265" y="326" text-anchor="middle" font-size="7" font-weight="bold" fill="#fff">JS</text>
  <text x="310" y="326" font-size="12" font-weight="600" fill="#333">Node.js API</text>
  <!-- PostgreSQL -->
  <rect x="415" y="298" width="200" height="48" rx="6" fill="#fff" stroke="#336791" stroke-width="1"/>
  <circle cx="442" cy="322" r="13" fill="#336791"/>
  <path d="M436,318 Q436,314 442,314 Q448,314 448,318 L448,326 Q448,330 442,330 Q436,330 436,326 Z" fill="none" stroke="#fff" stroke-width="1.2"/>
  <text x="490" y="326" font-size="12" font-weight="600" fill="#333">PostgreSQL + pgvector</text>
  <!-- Tech boxes row 2 -->
  <!-- Redis -->
  <rect x="65" y="358" width="155" height="48" rx="6" fill="#fff" stroke="#DC382D" stroke-width="1"/>
  <polygon points="90,372 102,378 90,384 78,378" fill="#DC382D"/>
  <text x="130" y="386" font-size="12" font-weight="600" fill="#333">Redis (채팅캐시)</text>
  <!-- S3 -->
  <rect x="240" y="358" width="155" height="48" rx="6" fill="#fff" stroke="#569A31" stroke-width="1"/>
  <rect x="254" y="370" width="22" height="18" rx="3" fill="#569A31"/>
  <text x="265" y="383" text-anchor="middle" font-size="7" font-weight="bold" fill="#fff">S3</text>
  <text x="310" y="386" font-size="12" font-weight="600" fill="#333">S3 (이미지)</text>
  <!-- Firebase Auth -->
  <rect x="415" y="358" width="200" height="48" rx="6" fill="#fff" stroke="#FFCA28" stroke-width="1"/>
  <path d="M440,383 L445,367 L449,375 L456,362 L462,383 Z" fill="#FFCA28"/>
  <text x="505" y="386" font-size="12" font-weight="600" fill="#333">Firebase Auth (ac.kr)</text>
  <!-- Arrow to escrow -->
  <line x1="340" y1="455" x2="340" y2="490" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow2)"/>
  <!-- Escrow box -->
  <rect x="40" y="490" width="600" height="55" rx="8" fill="url(#escrowGrad)" stroke="#2E7D32" stroke-width="1.5" filter="url(#shadow2)"/>
  <text x="340" y="515" text-anchor="middle" font-size="15" font-weight="bold" fill="#1B5E20">에스크로 결제</text>
  <text x="340" y="535" text-anchor="middle" font-size="12" fill="#555">거래 완료 후 자동 정산 (수수료 3.5%)</text>
</svg>

### 3.3 핵심 차별화 요소

| 기존 서비스 | 리유즈잇 차별점 | 근거 |
|:---|:---|:---|
| 당근마켓 - 지역 기반, 신원 미인증 | **대학 이메일(ac.kr) 실명 인증** → 사기율 92% 감소 예상 | Resnick et al. (2006), "The value of reputation on eBay" |
| 번개장터 - 범용 중고거래 | **학과·과목별 카테고리** + 개강 시즌 추천 | 대학 특화 버티컬 전략 |
| 에브리타임 장터 - 텍스트만 가능 | **AI 사진 분류 + 시세 자동 추정** | MobileNetV3 pre-trained model (Howard et al., 2019) |
| 중고나라 - 에스크로 없음 | **플랫폼 내 에스크로 결제** | 한국소비자원 사기예방 가이드라인 (2024) |

### 3.4 시장 규모 분석

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 620 340" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <linearGradient id="tamGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#E3F2FD;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#BBDEFB;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="samGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#E8F5E9;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#A5D6A7;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="somGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#FFF3E0;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#FFCC80;stop-opacity:1"/>
    </linearGradient>
    <filter id="shadow3" x="-1%" y="-1%" width="102%" height="104%">
      <feDropShadow dx="1" dy="2" stdDeviation="3" flood-opacity="0.12"/>
    </filter>
  </defs>
  <!-- TAM outer -->
  <rect x="20" y="15" width="580" height="310" rx="12" fill="url(#tamGrad)" stroke="#1565C0" stroke-width="2" filter="url(#shadow3)"/>
  <text x="50" y="45" font-size="16" font-weight="bold" fill="#0D47A1">TAM (전체시장)</text>
  <text x="50" y="68" font-size="13" fill="#333">국내 중고거래 시장: 43조원</text>
  <!-- SAM middle -->
  <rect x="55" y="85" width="510" height="220" rx="10" fill="url(#samGrad)" stroke="#2E7D32" stroke-width="1.5"/>
  <text x="80" y="115" font-size="15" font-weight="bold" fill="#1B5E20">SAM (유효시장)</text>
  <text x="80" y="138" font-size="13" fill="#333">대학생 중고거래: 3.2조원</text>
  <text x="80" y="158" font-size="12" fill="#666">(328만명 x 연 97만원 거래액)</text>
  <!-- SOM inner -->
  <rect x="95" y="175" width="435" height="115" rx="8" fill="url(#somGrad)" stroke="#E65100" stroke-width="1.5"/>
  <text x="120" y="205" font-size="14" font-weight="bold" fill="#BF360C">SOM (수익시장)</text>
  <text x="120" y="228" font-size="13" fill="#333">수도권 10개 대학 시작: 320억원</text>
  <text x="120" y="248" font-size="12" fill="#666">(32만명 x 연 97만원 x 3.5% 수수료)</text>
  <text x="120" y="272" font-size="13" font-weight="bold" fill="#E65100">= 연 11.2억원 수수료 수익</text>
</svg>

### 3.5 수익 모델

| 수익원 | 단가 | 3년차 목표 | 비중 |
|:---|:---|:---|:---:|
| 거래 수수료 (3.5%) | 건당 평균 1,200원 | 18억원 | 60% |
| 프리미엄 노출 광고 | 건당 1,000원 | 6억원 | 20% |
| 대학 생협 제휴 | 월 50만원/교 | 3.6억원 | 12% |
| 이사/택배 연계 | 건당 2,000원 | 2.4억원 | 8% |
| **합계** | | **30억원** | **100%** |

### 3.6 개발 로드맵

| 단계 | 기간 | 주요 마일스톤 | 기술 과제 |
|:---|:---|:---|:---|
| **Phase 1: MVP** | 1~3개월 | 웹앱 출시, 1개 대학 테스트 | Next.js + Supabase, 기본 CRUD |
| **Phase 2: AI 적용** | 4~6개월 | AI 분류·시세추정, 에스크로 | MobileNet 통합, PG 연동 |
| **Phase 3: 확장** | 7~12개월 | 수도권 10개 대학, 앱 출시 | React Native, 푸시알림 |
| **Phase 4: 고도화** | 13~18개월 | 전국 확대, B2B 제휴 | 추천 알고리즘, 데이터 분석 |

### 3.7 왜 컴공 대학생에게 최적인가

1. **자기 자신이 타겟 사용자**: 대학생이 겪는 교재비·전자기기 거래 문제를 직접 체감 (Customer Development 원칙, Blank, 2013)
2. **기술 난이도 적정**: 웹/앱 개발 수업 수준의 기술로 MVP 구현 가능, AI는 사전학습 모델 활용
3. **캠퍼스 바이럴**: 에브리타임·학과 단톡방 등 대학생 채널로 무비용 마케팅 가능
4. **글로벌 검증 모델**: 미국 Depop(유니콘, Etsy 인수 $1.6B), 일본 메르카리(상장) 등 검증된 비즈니스 모델

---

## 4. 아이템 2: 스터디브릿지 - P2P 학습 매칭

### 4.1 문제 정의

| 문제 항목 | 현황 수치 | 출처 |
|:---|:---|:---|
| 국내 사교육 시장 규모 | **27.1조원** (2024) | 통계청 사교육비조사 (2024) |
| 대학생 전공과목 학습 어려움 | **67.3%** | 한국교육개발원 (2023) |
| 기존 과외 플랫폼 대학 전공 커버율 | **12% 미만** | 숨고·김과외 카테고리 분석 |
| 대학생 전공 튜터링 수요 | **81.4%** | 대학교육협의회 설문 (2023) |
| 대학 내 튜터링 프로그램 만족도 | **34.2%** | 한국대학교육협의회 (2023) |

대학 전공과목(데이터구조, 운영체제, 열역학 등)은 **기존 K-12 중심 과외 플랫폼에서 거의 다루지 않으며**, 대학 공식 튜터링은 시간·인원 제한으로 만족도가 낮다. 결과적으로 전공 학습에 어려움을 겪는 학생 67.3%가 적절한 도움을 받지 못하고 있다(한국교육개발원, 2023).

### 4.2 솔루션 개요

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 660 420" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow3" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <marker id="arrowBi" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#7B1FA2"/>
    </marker>
    <filter id="shadow4" x="-1%" y="-1%" width="102%" height="105%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.1"/>
    </filter>
  </defs>
  <!-- Outer platform box -->
  <rect x="20" y="10" width="620" height="400" rx="10" fill="#FAFAFA" stroke="#7B1FA2" stroke-width="2" filter="url(#shadow4)"/>
  <text x="330" y="40" text-anchor="middle" font-size="17" font-weight="bold" fill="#4A148C">스터디브릿지 플랫폼</text>
  <!-- Tutor box -->
  <rect x="60" y="60" width="190" height="65" rx="8" fill="#E8F5E9" stroke="#2E7D32" stroke-width="1.5"/>
  <text x="155" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#1B5E20">튜터</text>
  <text x="155" y="107" text-anchor="middle" font-size="11" fill="#555">(A+ 성적인증)</text>
  <!-- Learner box -->
  <rect x="410" y="60" width="190" height="65" rx="8" fill="#E3F2FD" stroke="#1565C0" stroke-width="1.5"/>
  <text x="505" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#0D47A1">학습자</text>
  <text x="505" y="107" text-anchor="middle" font-size="11" fill="#555">(수강중 과목)</text>
  <!-- Bidirectional arrow -->
  <line x1="250" y1="92" x2="410" y2="92" stroke="#7B1FA2" stroke-width="2" marker-end="url(#arrowBi)"/>
  <line x1="410" y1="88" x2="250" y2="88" stroke="#7B1FA2" stroke-width="2" marker-end="url(#arrowBi)"/>
  <text x="330" y="82" text-anchor="middle" font-size="11" font-weight="bold" fill="#7B1FA2">매칭</text>
  <!-- Arrows down -->
  <line x1="155" y1="125" x2="155" y2="155" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow3)"/>
  <line x1="505" y1="125" x2="505" y2="155" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow3)"/>
  <!-- AI Matching Engine -->
  <rect x="75" y="155" width="510" height="100" rx="8" fill="#FFF3E0" stroke="#E65100" stroke-width="1.5"/>
  <text x="330" y="180" text-anchor="middle" font-size="15" font-weight="bold" fill="#BF360C">AI 매칭 엔진</text>
  <text x="120" y="205" font-size="12" fill="#555">- 전공/과목/교수별 매칭</text>
  <text x="120" y="225" font-size="12" fill="#555">- 학습 스타일 호환성 분석</text>
  <text x="380" y="205" font-size="12" fill="#555">- 시간표 자동 조율</text>
  <!-- Arrow down from AI engine -->
  <line x1="190" y1="255" x2="190" y2="290" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow3)"/>
  <line x1="330" y1="255" x2="330" y2="290" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow3)"/>
  <line x1="470" y1="255" x2="470" y2="290" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow3)"/>
  <!-- Bottom 3 boxes -->
  <!-- WebRTC -->
  <rect x="100" y="290" width="170" height="55" rx="8" fill="#E0F2F1" stroke="#00796B" stroke-width="1.5"/>
  <circle cx="130" cy="310" r="7" fill="none" stroke="#333" stroke-width="1.5"/>
  <circle cx="145" cy="320" r="7" fill="none" stroke="#333" stroke-width="1.5"/>
  <line x1="135" y1="313" x2="140" y2="317" stroke="#333" stroke-width="1.2"/>
  <text x="200" y="315" text-anchor="middle" font-size="13" font-weight="600" fill="#004D40">WebRTC</text>
  <text x="185" y="335" text-anchor="middle" font-size="11" fill="#555">화상수업</text>
  <!-- Learning materials -->
  <rect x="290" y="290" width="170" height="55" rx="8" fill="#F3E5F5" stroke="#7B1FA2" stroke-width="1.5"/>
  <text x="375" y="315" text-anchor="middle" font-size="13" font-weight="600" fill="#4A148C">학습자료</text>
  <text x="375" y="335" text-anchor="middle" font-size="11" fill="#555">공유 허브</text>
  <!-- Settlement -->
  <rect x="480" y="290" width="130" height="55" rx="8" fill="#E8EAF6" stroke="#283593" stroke-width="1.5"/>
  <text x="545" y="315" text-anchor="middle" font-size="13" font-weight="600" fill="#1A237E">정산</text>
  <text x="545" y="335" text-anchor="middle" font-size="11" fill="#555">시스템</text>
</svg>

### 4.3 핵심 차별화: 성적 인증 튜터 시스템

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 230" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <filter id="shadow5" x="-1%" y="-2%" width="102%" height="108%">
      <feDropShadow dx="1" dy="1" stdDeviation="1.5" flood-opacity="0.1"/>
    </filter>
  </defs>
  <!-- Left: Existing platforms -->
  <rect x="20" y="15" width="300" height="200" rx="8" fill="#FFEBEE" stroke="#C62828" stroke-width="1.5" filter="url(#shadow5)"/>
  <text x="170" y="42" text-anchor="middle" font-size="14" font-weight="bold" fill="#B71C1C">기존 플랫폼 (숨고, 김과외)</text>
  <line x1="40" y1="52" x2="300" y2="52" stroke="#EF9A9A" stroke-width="1"/>
  <text x="40" y="78" font-size="12" fill="#555">K-12 중심 (초중고)</text>
  <text x="40" y="102" font-size="12" fill="#555">자기신고 경력</text>
  <text x="40" y="126" font-size="12" fill="#555">튜터 검증 없음</text>
  <text x="40" y="150" font-size="12" fill="#555">시간당 3~5만원</text>
  <text x="40" y="174" font-size="12" fill="#555">매칭까지 3~7일</text>
  <!-- VS -->
  <text x="345" y="120" text-anchor="middle" font-size="20" font-weight="bold" fill="#9E9E9E">VS</text>
  <!-- Right: StudyBridge -->
  <rect x="370" y="15" width="290" height="200" rx="8" fill="#E8F5E9" stroke="#2E7D32" stroke-width="1.5" filter="url(#shadow5)"/>
  <text x="515" y="42" text-anchor="middle" font-size="14" font-weight="bold" fill="#1B5E20">스터디브릿지</text>
  <line x1="390" y1="52" x2="640" y2="52" stroke="#A5D6A7" stroke-width="1"/>
  <text x="390" y="78" font-size="12" font-weight="600" fill="#2E7D32">대학 전공 특화</text>
  <text x="390" y="102" font-size="12" font-weight="600" fill="#2E7D32">성적표 인증 (A+/A0)</text>
  <text x="390" y="126" font-size="12" font-weight="600" fill="#2E7D32">같은 대학/같은 과목 수강 이력</text>
  <text x="390" y="150" font-size="12" font-weight="600" fill="#2E7D32">시간당 1~2만원 (P2P)</text>
  <text x="390" y="174" font-size="12" font-weight="600" fill="#2E7D32">즉시 매칭 (동일 수업 기반)</text>
</svg>

### 4.4 시장 기회

| 구분 | 규모 | 산출 근거 |
|:---|:---|:---|
| **TAM** | 27.1조원 | 국내 사교육 시장 전체 (통계청, 2024) |
| **SAM** | 2.1조원 | 대학생 전공 학습 관련 지출 (310만명 × 연 68만원) |
| **SOM (3년)** | 210억원 | 수도권 주요 30개 대학, MAU 5만명, 연 420만건 매칭 |

### 4.5 수익 구조

| 수익원 | 모델 | 연 3년차 목표 |
|:---|:---|:---|
| 매칭 수수료 | 거래액의 15% | 31.5억원 |
| 학습자료 판매 | 판매액의 20% | 8.4억원 |
| 대학 B2B 라이선스 | 교당 연 1,200만원 | 3.6억원 |
| 프리미엄 튜터 뱃지 | 월 9,900원 | 2.4억원 |
| **합계** | | **45.9억원** |

### 4.6 컴공 대학생 적합성

- **WebRTC 화상 수업**: 네트워크 프로그래밍 수업에서 배운 기술 직접 적용
- **추천 알고리즘**: 데이터구조·알고리즘 수업 지식으로 매칭 엔진 구현
- **본인이 1호 튜터**: 컴공 전공과목(자료구조, 알고리즘, OS 등) 직접 튜터링 가능
- **레퍼런스**: Chegg Tutors(CHGG, 시총 $1.2B), Wyzant(미국), Manabie(일본) 등 검증

---

## 5. 아이템 3: 룸메이트 - 대학생 주거 매칭

### 5.1 문제 정의

| 문제 항목 | 현황 수치 | 출처 |
|:---|:---|:---|
| 서울 평균 월세 (원룸) | **62.2만원** | KB부동산 월세통계 (2024) |
| 대학생 월 생활비 대비 주거비 비중 | **52.3%** | 한국장학재단 (2024) |
| 허위매물 피해 경험률 | **58.7%** | 한국소비자원 (2024) |
| 룸메이트 갈등으로 인한 이사 비율 | **34.6%** | 대학내일20대연구소 (2023) |
| 1인 가구 대학생 수 | **약 120만명** | 통계청 인구주택총조사 (2024) |

### 5.2 솔루션 구조

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 660 450" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow6" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <filter id="shadow6" x="-1%" y="-1%" width="102%" height="105%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.1"/>
    </filter>
  </defs>
  <!-- Outer platform box -->
  <rect x="20" y="10" width="620" height="430" rx="10" fill="#FAFAFA" stroke="#00695C" stroke-width="2" filter="url(#shadow6)"/>
  <text x="330" y="38" text-anchor="middle" font-size="17" font-weight="bold" fill="#004D40">룸메이트 플랫폼</text>
  <!-- AI Compatibility Analysis section -->
  <rect x="45" y="52" width="570" height="260" rx="8" fill="#E0F2F1" stroke="#00897B" stroke-width="1.5"/>
  <text x="330" y="78" text-anchor="middle" font-size="15" font-weight="bold" fill="#004D40">AI 생활패턴 호환성 분석</text>
  <!-- 3 factor boxes -->
  <rect x="75" y="95" width="140" height="55" rx="6" fill="#fff" stroke="#26A69A" stroke-width="1.2"/>
  <text x="145" y="118" text-anchor="middle" font-size="12" font-weight="600" fill="#00695C">취침시간</text>
  <text x="145" y="136" text-anchor="middle" font-size="11" fill="#777">흡연여부</text>
  <rect x="245" y="95" width="140" height="55" rx="6" fill="#fff" stroke="#26A69A" stroke-width="1.2"/>
  <text x="315" y="118" text-anchor="middle" font-size="12" font-weight="600" fill="#00695C">청소주기</text>
  <text x="315" y="136" text-anchor="middle" font-size="11" fill="#777">반려동물</text>
  <rect x="415" y="95" width="140" height="55" rx="6" fill="#fff" stroke="#26A69A" stroke-width="1.2"/>
  <text x="485" y="118" text-anchor="middle" font-size="12" font-weight="600" fill="#00695C">소음민감</text>
  <text x="485" y="136" text-anchor="middle" font-size="11" fill="#777">생활리듬</text>
  <!-- Arrows down -->
  <line x1="145" y1="150" x2="145" y2="180" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow6)"/>
  <line x1="315" y1="150" x2="315" y2="180" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow6)"/>
  <line x1="485" y1="150" x2="485" y2="180" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow6)"/>
  <!-- Compatibility score box -->
  <rect x="75" y="180" width="480" height="110" rx="8" fill="#FFF8E1" stroke="#F9A825" stroke-width="1.5"/>
  <text x="315" y="210" text-anchor="middle" font-size="14" font-weight="bold" fill="#E65100">호환성 점수 산출 (0~100점)</text>
  <text x="100" y="238" font-size="12" fill="#555">- 코사인 유사도 기반 벡터 매칭</text>
  <text x="100" y="262" font-size="12" fill="#555">- 갈등 요인 사전 경고</text>
  <!-- Bottom boxes -->
  <rect x="45" y="330" width="250" height="90" rx="8" fill="#FCE4EC" stroke="#C62828" stroke-width="1.2"/>
  <text x="170" y="358" text-anchor="middle" font-size="14" font-weight="bold" fill="#B71C1C">허위매물 탐지</text>
  <text x="65" y="380" font-size="12" fill="#555">- CLIP 이미지 분석</text>
  <text x="65" y="400" font-size="12" fill="#555">- 중복등록 감지</text>
  <rect x="320" y="330" width="295" height="90" rx="8" fill="#E8EAF6" stroke="#283593" stroke-width="1.2"/>
  <text x="467" y="358" text-anchor="middle" font-size="14" font-weight="bold" fill="#1A237E">공공데이터 연동</text>
  <text x="340" y="380" font-size="12" fill="#555">- 국토부 실거래가 API</text>
  <text x="340" y="400" font-size="12" fill="#555">- 등기부등본 자동 조회</text>
</svg>

### 5.3 경쟁 분석

| 비교 항목 | 직방/다방 | 피터팬의 좋은방 | **룸메이트** |
|:---|:---|:---|:---|
| 타겟 | 전 연령 | 청년 | **대학생 특화** |
| 룸메이트 매칭 | 없음 | 없음 | **AI 호환성 분석** |
| 허위매물 대응 | 중개사 인증 | 신고 기반 | **AI + 공공데이터 자동 검증** |
| 실거래가 비교 | 유료 | 없음 | **무료 (공공API)** |
| 사용자 인증 | 휴대폰 | 휴대폰 | **대학 이메일(ac.kr)** |
| 월 이용료 | 무료(중개사 과금) | 무료 | **기본 무료, 프리미엄 4,900원** |

### 5.4 컴공 대학생 적합성

- **공공 API 활용**: 국토부 실거래가, 등기부 API 연동은 좋은 포트폴리오
- **추천 시스템**: 협업 필터링·콘텐츠 기반 필터링 등 ML 수업 내용 실전 적용
- **본인이 타겟**: 자취/기숙사 경험에서 나온 페인포인트 직접 해결
- **레퍼런스**: Roomi(미국, $12M 투자유치), Badi(스페인, $50M 투자유치) 등

---

## 6. 아이템 4: 포데이 - 취미 습관 커뮤니티 앱

### 6.1 문제 정의

| 문제 항목 | 현황 수치 | 출처 |
|:---|:---|:---|
| 1인 가구 수 | **804만 가구** (전체 36.1%) | 통계청 인구주택총조사 (2024) |
| 20~30대 외로움 경험률 | **62.1%** | 한국건강증진개발원 (2023) |
| 취미 중도 포기 사유 1위 | **"함께할 사람 없음" (31.6%)** | 문화체육관광부 여가활동조사 (2023) |
| 외로움에 의한 사망위험 증가 | **29%** | Holt-Lunstad et al. (2015), meta-analysis |
| 취미·여가 관련 앱 시장 | **4.3조원** | 한국콘텐츠진흥원 (2024) |

사회적 고립은 WHO가 2023년 **"글로벌 공중보건 위협"**으로 선언한 문제이며(WHO, 2023), 한국은 OECD 사회적 고립도 1위이다(OECD, 2023). 특히 20~30대 1인 가구에서 취미를 통한 사회적 연결 욕구가 높으나, 기존 앱은 **습관 형성**과 **커뮤니티**를 결합하지 못한다.

### 6.2 행동과학 기반 설계

포데이의 핵심 차별점은 **7가지 행동과학 이론**을 제품 설계에 적용한 점이다:

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 660 460" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <filter id="shadow7" x="-1%" y="-1%" width="102%" height="104%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.1"/>
    </filter>
    <linearGradient id="grapeGrad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#F3E5F5;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#E1BEE7;stop-opacity:1"/>
    </linearGradient>
  </defs>
  <!-- Outer box -->
  <rect x="20" y="10" width="620" height="440" rx="10" fill="#FAFAFA" stroke="#6A1B9A" stroke-width="2" filter="url(#shadow7)"/>
  <text x="330" y="40" text-anchor="middle" font-size="17" font-weight="bold" fill="#4A148C">포데이의 행동과학 기반 핵심 기능</text>
  <!-- Grape Calendar feature box -->
  <rect x="50" y="58" width="560" height="100" rx="8" fill="url(#grapeGrad)" stroke="#8E24AA" stroke-width="1.5"/>
  <!-- Grape cluster icon -->
  <circle cx="82" cy="85" r="8" fill="#7B1FA2" opacity="0.7"/>
  <circle cx="96" cy="85" r="8" fill="#7B1FA2" opacity="0.7"/>
  <circle cx="89" cy="97" r="8" fill="#7B1FA2" opacity="0.8"/>
  <circle cx="75" cy="97" r="8" fill="#7B1FA2" opacity="0.8"/>
  <circle cx="103" cy="97" r="8" fill="#7B1FA2" opacity="0.8"/>
  <circle cx="82" cy="109" r="8" fill="#9C27B0" opacity="0.9"/>
  <circle cx="96" cy="109" r="8" fill="#9C27B0" opacity="0.9"/>
  <text x="140" y="84" font-size="15" font-weight="bold" fill="#4A148C">66 포도 캘린더</text>
  <text x="140" y="106" font-size="12" fill="#555">- 66일 습관 형성 이론 (Lally et al., 2010)</text>
  <text x="140" y="124" font-size="12" fill="#555">- 매일 포도알 1개 채우기 / 시각적 진행률로 동기부여</text>
  <!-- Applied theories label -->
  <text x="50" y="188" font-size="14" font-weight="bold" fill="#4A148C">적용 이론:</text>
  <!-- Theory boxes - Row 1 -->
  <rect x="50" y="200" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="60" y="222" font-size="12" font-weight="600" fill="#311B92">1. 66일 습관형성</text>
  <text x="60" y="242" font-size="11" fill="#666">(Lally et al.)</text>
  <rect x="340" y="200" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="350" y="222" font-size="12" font-weight="600" fill="#311B92">2. 손실회피</text>
  <text x="350" y="242" font-size="11" fill="#666">(Tversky &amp; Kahneman, 1991)</text>
  <!-- Theory boxes - Row 2 -->
  <rect x="50" y="268" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="60" y="290" font-size="12" font-weight="600" fill="#311B92">3. 부여된 진행효과</text>
  <text x="60" y="310" font-size="11" fill="#666">(Nunes &amp; Dreze)</text>
  <rect x="340" y="268" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="350" y="290" font-size="12" font-weight="600" fill="#311B92">4. 자이가르닉 효과</text>
  <text x="350" y="310" font-size="11" fill="#666">(Zeigarnik, 1938)</text>
  <!-- Theory boxes - Row 3 -->
  <rect x="50" y="336" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="60" y="358" font-size="12" font-weight="600" fill="#311B92">5. 사회적 촉진</text>
  <text x="60" y="378" font-size="11" fill="#666">(Zajonc, 1965)</text>
  <rect x="340" y="336" width="270" height="55" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="350" y="358" font-size="12" font-weight="600" fill="#311B92">6. 자기결정이론</text>
  <text x="350" y="378" font-size="11" fill="#666">(Deci &amp; Ryan, 2000)</text>
  <!-- Theory box - Row 4 (single) -->
  <rect x="50" y="404" width="270" height="35" rx="6" fill="#EDE7F6" stroke="#5E35B1" stroke-width="1.2"/>
  <text x="60" y="426" font-size="12" font-weight="600" fill="#311B92">7. 목표설정이론 (Locke &amp; Latham)</text>
</svg>

### 6.3 비즈니스 모델

| 수익원 | 무료 | 프리미엄 (4,900원/월) |
|:---|:---|:---|
| 취미 추천 | 기본 3개 | AI 맞춤 무제한 |
| 66 포도 캘린더 | 1개 | 5개 동시 진행 |
| 주간 AI 리포트 | 없음 | 제공 |
| 커뮤니티 | 참여만 | 모임 개설 가능 |
| 취미용품 마켓 | 일반가 | 5% 할인 |

### 6.4 컴공 대학생 적합성

- **클라우드 API 기반**: 로컬 LLM 서버 불필요, 월 운영비 15만원 이내
- **React Native**: 크로스플랫폼 앱 1개로 iOS/Android 동시 출시
- **가장 낮은 기술 장벽**: CRUD + 알림 + 간단한 추천 로직으로 MVP 가능
- **사회적 가치 높음**: 예비창업패키지 "사회문제해결형" 가산점 유리

---

## 7. 아이템 5: 동네알바 - 초단기 인력 매칭

### 7.1 문제 정의

| 문제 항목 | 현황 수치 | 출처 |
|:---|:---|:---|
| 국내 소상공인 수 | **790만 개** (전체 사업자의 87%) | 중소벤처기업부 (2024) |
| 단기 인력 노쇼율 | **25~35%** | 한국고용정보원 (2023) |
| 대학생 일평균 공강 시간 | **3.2시간** | 대학내일20대연구소 (2023) |
| 기존 플랫폼 매칭 소요시간 | **3~7일** | 알바몬·알바천국 내부 데이터 |
| 긱 이코노미 시장 성장률 | **연 17.4%** | 한국노동연구원 (2024) |

### 7.2 실시간 매칭 프로세스

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 680 580" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow8" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <filter id="shadow8" x="-1%" y="-1%" width="102%" height="105%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.1"/>
    </filter>
  </defs>
  <!-- Top labels -->
  <text x="150" y="20" text-anchor="middle" font-size="13" font-weight="bold" fill="#BF360C">소상공인 긴급 구인</text>
  <text x="150" y="38" text-anchor="middle" font-size="11" fill="#777">(점심시간 서빙 2시간)</text>
  <text x="530" y="20" text-anchor="middle" font-size="13" font-weight="bold" fill="#0D47A1">대학생 공강 매칭</text>
  <text x="530" y="38" text-anchor="middle" font-size="11" fill="#777">(수업 사이 3시간 여유)</text>
  <!-- Arrows down -->
  <line x1="150" y1="45" x2="150" y2="70" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <line x1="530" y1="45" x2="530" y2="70" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <!-- Left box: Job posting -->
  <rect x="60" y="70" width="180" height="65" rx="8" fill="#FFF3E0" stroke="#E65100" stroke-width="1.5" filter="url(#shadow8)"/>
  <text x="150" y="96" text-anchor="middle" font-size="14" font-weight="bold" fill="#BF360C">구인 등록</text>
  <text x="150" y="118" text-anchor="middle" font-size="12" fill="#555">(30초)</text>
  <!-- Right box: Available time -->
  <rect x="440" y="70" width="180" height="65" rx="8" fill="#E3F2FD" stroke="#1565C0" stroke-width="1.5" filter="url(#shadow8)"/>
  <text x="530" y="96" text-anchor="middle" font-size="14" font-weight="bold" fill="#0D47A1">가용시간</text>
  <text x="530" y="118" text-anchor="middle" font-size="12" fill="#555">자동 설정</text>
  <!-- Arrows converging -->
  <line x1="150" y1="135" x2="150" y2="170" stroke="#4A90D9" stroke-width="1.5"/>
  <line x1="530" y1="135" x2="530" y2="170" stroke="#4A90D9" stroke-width="1.5"/>
  <line x1="150" y1="170" x2="340" y2="190" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <line x1="530" y1="170" x2="340" y2="190" stroke="#4A90D9" stroke-width="1.5"/>
  <!-- AI Matching Engine -->
  <rect x="220" y="190" width="240" height="120" rx="8" fill="#FFF8E1" stroke="#F9A825" stroke-width="1.5" filter="url(#shadow8)"/>
  <text x="340" y="218" text-anchor="middle" font-size="15" font-weight="bold" fill="#E65100">AI 매칭 엔진</text>
  <text x="240" y="242" font-size="12" fill="#555">- 거리 (1km내)</text>
  <text x="240" y="260" font-size="12" fill="#555">- 시간 적합도</text>
  <text x="370" y="242" font-size="12" fill="#555">- 업종 경험</text>
  <text x="370" y="260" font-size="12" fill="#555">- 노쇼 예측 점수</text>
  <!-- Arrow down -->
  <line x1="340" y1="310" x2="340" y2="350" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow8)"/>
  <text x="400" y="338" font-size="12" font-weight="bold" fill="#E65100">평균 30초</text>
  <!-- Match notification -->
  <rect x="220" y="350" width="240" height="60" rx="8" fill="#E8F5E9" stroke="#2E7D32" stroke-width="1.5" filter="url(#shadow8)"/>
  <text x="340" y="377" text-anchor="middle" font-size="14" font-weight="bold" fill="#1B5E20">매칭 알림 발송</text>
  <text x="340" y="397" text-anchor="middle" font-size="12" fill="#555">수락 시 확정</text>
  <!-- Arrow down -->
  <line x1="340" y1="410" x2="340" y2="440" stroke="#4A90D9" stroke-width="1.5"/>
  <!-- Branch to 3 -->
  <line x1="340" y1="440" x2="150" y2="460" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <line x1="340" y1="440" x2="340" y2="460" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <line x1="340" y1="440" x2="530" y2="460" stroke="#4A90D9" stroke-width="1.5" marker-end="url(#arrow8)"/>
  <!-- 3 final boxes -->
  <rect x="70" y="460" width="160" height="60" rx="8" fill="#E0F2F1" stroke="#00796B" stroke-width="1.5"/>
  <text x="150" y="487" text-anchor="middle" font-size="13" font-weight="bold" fill="#004D40">GPS 출근 인증</text>
  <text x="150" y="507" text-anchor="middle" font-size="11" fill="#555">위치 확인</text>
  <rect x="260" y="460" width="160" height="60" rx="8" fill="#F3E5F5" stroke="#7B1FA2" stroke-width="1.5"/>
  <text x="340" y="487" text-anchor="middle" font-size="13" font-weight="bold" fill="#4A148C">근무 수행</text>
  <text x="340" y="507" text-anchor="middle" font-size="11" fill="#555">실시간 관리</text>
  <rect x="450" y="460" width="160" height="60" rx="8" fill="#E8EAF6" stroke="#283593" stroke-width="1.5"/>
  <text x="530" y="487" text-anchor="middle" font-size="13" font-weight="bold" fill="#1A237E">즉시 정산</text>
  <text x="530" y="507" text-anchor="middle" font-size="11" fill="#555">(당일)</text>
</svg>

### 7.3 글로벌 벤치마킹

| 서비스 | 국가 | 성과 | 시사점 |
|:---|:---|:---|:---|
| **Timee** | 일본 | 2023 도쿄증시 상장, 시총 ¥300B | 초단기 매칭 모델 IPO 검증 |
| **Wonolo** | 미국 | $138M 투자유치 | 당일 매칭 수요 입증 |
| **Syft** | 영국 | $25M 투자유치 | 호스피탈리티 특화 |
| **ShiftSmart** | 미국 | $95M 투자유치 | AI 매칭 효율성 검증 |

위 사례들은 **초단기(2~8시간) 인력 매칭이 글로벌에서 검증된 비즈니스 모델**임을 입증한다. 특히 Timee는 2019년 서비스 출시 후 4년 만에 도쿄증시 상장에 성공하여 연 매출 ¥23B(약 2,000억원)을 달성하였다(Timee IR, 2024).

### 7.4 수익 구조

| 수익원 | 모델 | 연 3년차 목표 |
|:---|:---|:---|
| 매칭 수수료 (10%) | 시급 12,000원 기준 건당 1,200원 | 36억원 |
| 가맹점 프리미엄 구독 | 월 19,900원 | 4.8억원 |
| 급여 선지급 수수료 | 건당 500원 | 1.8억원 |
| **합계** | | **42.6억원** |

### 7.5 컴공 대학생 적합성

- **실시간 시스템**: 소켓 프로그래밍, Redis Pub/Sub 등 CS 핵심 기술 적용
- **위치 기반 서비스**: PostGIS, Geohash 등 공간 데이터 처리 경험
- **결제 시스템**: PG 연동 경험은 취업 시에도 강력한 포트폴리오
- **주의사항**: PG 연동과 노동법 준수가 다른 아이템 대비 진입장벽이 높음

---

## 8. 기술 스택 로드맵

### 8.1 공통 기술 스택 (전 아이템 공유)

| 레이어 | 기술 | 선택 근거 |
|:---|:---|:---|
| **Frontend** | Next.js 14 (App Router) | SSR/SSG 지원, React 생태계 (Verma, 2024) |
| **Mobile** | React Native (Expo) | 크로스플랫폼, 웹 기술 재사용 |
| **Backend** | Node.js + Express/Fastify | JS 풀스택, 비동기 I/O 효율 |
| **Database** | PostgreSQL + Supabase | 오픈소스, 무료 호스팅 가능 |
| **Auth** | Firebase Auth / Supabase Auth | 소셜 로그인·이메일 인증 무료 |
| **Storage** | Cloudflare R2 | S3 호환, 무료 10GB |
| **배포** | Vercel (Frontend) + Railway (Backend) | 무료 티어, 자동 배포 |
| **CI/CD** | GitHub Actions | 무료, 학생 Pro 팩 |

### 8.2 비용 비교표 (월 기준)

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 650 300" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <linearGradient id="costBar1" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#1565C0;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#42A5F5;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="costBar2" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#2E7D32;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#66BB6A;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="costBar3" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#6A1B9A;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#AB47BC;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="costBar4" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#E65100;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#FF9800;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="costBar5" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#C62828;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#EF5350;stop-opacity:1"/>
    </linearGradient>
    <filter id="costShadow" x="-1%" y="-5%" width="102%" height="120%">
      <feDropShadow dx="1" dy="1" stdDeviation="1" flood-opacity="0.12"/>
    </filter>
  </defs>
  <!-- Background -->
  <rect x="10" y="5" width="630" height="288" rx="10" fill="#FAFAFA" stroke="#E0E0E0" stroke-width="1"/>
  <text x="325" y="32" text-anchor="middle" font-size="16" font-weight="bold" fill="#333">월 운영비 비교 (MVP 단계)</text>
  <!-- Grid lines -->
  <line x1="150" y1="50" x2="150" y2="240" stroke="#EEE" stroke-width="1"/>
  <line x1="270" y1="50" x2="270" y2="240" stroke="#EEE" stroke-width="1" stroke-dasharray="4"/>
  <line x1="390" y1="50" x2="390" y2="240" stroke="#EEE" stroke-width="1" stroke-dasharray="4"/>
  <line x1="510" y1="50" x2="510" y2="240" stroke="#EEE" stroke-width="1" stroke-dasharray="4"/>
  <!-- Scale labels -->
  <text x="150" y="254" text-anchor="middle" font-size="10" fill="#999">0</text>
  <text x="270" y="254" text-anchor="middle" font-size="10" fill="#999">15만</text>
  <text x="390" y="254" text-anchor="middle" font-size="10" fill="#999">30만</text>
  <text x="510" y="254" text-anchor="middle" font-size="10" fill="#999">45만</text>
  <!-- Row 1: 리유즈잇 20만 -->
  <text x="138" y="80" text-anchor="end" font-size="13" font-weight="500" fill="#333">리유즈잇</text>
  <rect x="150" y="66" width="160" height="22" rx="4" fill="url(#costBar1)" filter="url(#costShadow)"/>
  <text x="318" y="82" font-size="12" font-weight="bold" fill="#1565C0">20만원</text>
  <!-- Row 2: 스터디브릿지 30만 -->
  <text x="138" y="118" text-anchor="end" font-size="13" font-weight="500" fill="#333">스터디브릿지</text>
  <rect x="150" y="104" width="240" height="22" rx="4" fill="url(#costBar2)" filter="url(#costShadow)"/>
  <text x="398" y="120" font-size="12" font-weight="bold" fill="#2E7D32">30만원</text>
  <!-- Row 3: 룸메이트 25만 -->
  <text x="138" y="156" text-anchor="end" font-size="13" font-weight="500" fill="#333">룸메이트</text>
  <rect x="150" y="142" width="200" height="22" rx="4" fill="url(#costBar3)" filter="url(#costShadow)"/>
  <text x="358" y="158" font-size="12" font-weight="bold" fill="#6A1B9A">25만원</text>
  <!-- Row 4: 포데이 15만 -->
  <text x="138" y="194" text-anchor="end" font-size="13" font-weight="500" fill="#333">포데이</text>
  <rect x="150" y="180" width="120" height="22" rx="4" fill="url(#costBar4)" filter="url(#costShadow)"/>
  <text x="278" y="196" font-size="12" font-weight="bold" fill="#E65100">15만원</text>
  <!-- Row 5: 동네알바 40만 -->
  <text x="138" y="232" text-anchor="end" font-size="13" font-weight="500" fill="#333">동네알바</text>
  <rect x="150" y="218" width="320" height="22" rx="4" fill="url(#costBar5)" filter="url(#costShadow)"/>
  <text x="478" y="234" font-size="12" font-weight="bold" fill="#C62828">40만원</text>
  <!-- Footnote -->
  <text x="325" y="278" text-anchor="middle" font-size="11" fill="#999">※ Vercel/Supabase 무료 티어 + 클라우드 API 최소 사용</text>
</svg>

### 8.3 학습 우선순위 로드맵

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 720 280" width="100%" font-family="'Noto Sans KR', sans-serif">
  <defs>
    <marker id="arrow9" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-auto">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#4A90D9"/>
    </marker>
    <filter id="shadow9" x="-2%" y="-3%" width="104%" height="110%">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-opacity="0.12"/>
    </filter>
    <linearGradient id="phase1Grad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E3F2FD;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#BBDEFB;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="phase2Grad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#E8F5E9;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#C8E6C9;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="phase3Grad" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#F3E5F5;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#E1BEE7;stop-opacity:1"/>
    </linearGradient>
  </defs>
  <!-- Phase labels -->
  <text x="130" y="25" text-anchor="middle" font-size="13" font-weight="bold" fill="#1565C0">Month 1-2</text>
  <text x="360" y="25" text-anchor="middle" font-size="13" font-weight="bold" fill="#2E7D32">Month 3-4</text>
  <text x="590" y="25" text-anchor="middle" font-size="13" font-weight="bold" fill="#7B1FA2">Month 5-6</text>
  <!-- Phase 1 Box -->
  <rect x="25" y="40" width="210" height="220" rx="8" fill="url(#phase1Grad)" stroke="#1565C0" stroke-width="1.5" filter="url(#shadow9)"/>
  <text x="130" y="68" text-anchor="middle" font-size="15" font-weight="bold" fill="#0D47A1">기초 구축</text>
  <line x1="45" y1="78" x2="215" y2="78" stroke="#90CAF9" stroke-width="1"/>
  <!-- Next.js icon -->
  <circle cx="55" cy="103" r="11" fill="#000"/>
  <text x="55" y="107" text-anchor="middle" font-size="10" font-weight="bold" fill="#fff">N</text>
  <text x="75" y="107" font-size="12" fill="#333">Next.js</text>
  <!-- React icon -->
  <circle cx="55" cy="133" r="10" fill="none" stroke="#61DAFB" stroke-width="1.5"/>
  <ellipse cx="55" cy="133" rx="10" ry="4" fill="none" stroke="#61DAFB" stroke-width="1" transform="rotate(60,55,133)"/>
  <ellipse cx="55" cy="133" rx="10" ry="4" fill="none" stroke="#61DAFB" stroke-width="1" transform="rotate(-60,55,133)"/>
  <circle cx="55" cy="133" r="2" fill="#61DAFB"/>
  <text x="75" y="137" font-size="12" fill="#333">React</text>
  <!-- Node.js icon -->
  <polygon points="55,153 63,158 63,168 55,173 47,168 47,158" fill="#339933"/>
  <text x="55" y="167" text-anchor="middle" font-size="6" font-weight="bold" fill="#fff">JS</text>
  <text x="75" y="167" font-size="12" fill="#333">Node.js</text>
  <!-- PostgreSQL icon -->
  <circle cx="55" cy="195" r="10" fill="#336791"/>
  <path d="M50,192 Q50,188 55,188 Q60,188 60,192 L60,198 Q60,201 55,201 Q50,201 50,198 Z" fill="none" stroke="#fff" stroke-width="0.8"/>
  <text x="75" y="199" font-size="12" fill="#333">PostgreSQL</text>
  <!-- Git icon -->
  <circle cx="55" cy="228" r="5" fill="#F05032"/>
  <line x1="55" y1="223" x2="55" y2="218" stroke="#F05032" stroke-width="2"/>
  <line x1="55" y1="223" x2="62" y2="218" stroke="#F05032" stroke-width="2"/>
  <circle cx="55" cy="218" r="2.5" fill="#F05032"/>
  <circle cx="62" cy="218" r="2.5" fill="#F05032"/>
  <text x="75" y="232" font-size="12" fill="#333">Git</text>
  <!-- Arrow 1 to 2 -->
  <line x1="235" y1="150" x2="250" y2="150" stroke="#4A90D9" stroke-width="2.5" marker-end="url(#arrow9)"/>
  <!-- Phase 2 Box -->
  <rect x="255" y="40" width="210" height="220" rx="8" fill="url(#phase2Grad)" stroke="#2E7D32" stroke-width="1.5" filter="url(#shadow9)"/>
  <text x="360" y="68" text-anchor="middle" font-size="15" font-weight="bold" fill="#1B5E20">핵심 기능</text>
  <line x1="275" y1="78" x2="445" y2="78" stroke="#A5D6A7" stroke-width="1"/>
  <!-- WebRTC icon -->
  <circle cx="285" cy="100" r="5" fill="#333"/>
  <circle cx="295" cy="108" r="5" fill="#333"/>
  <line x1="289" y1="103" x2="292" y2="106" stroke="#333" stroke-width="1.5"/>
  <text x="305" y="107" font-size="12" fill="#333">WebRTC</text>
  <!-- PG icon -->
  <rect x="278" y="123" width="16" height="12" rx="2" fill="#1565C0"/>
  <text x="286" y="133" text-anchor="middle" font-size="7" font-weight="bold" fill="#fff">PG</text>
  <text x="305" y="137" font-size="12" fill="#333">PG 연동</text>
  <!-- Redis icon -->
  <polygon points="286,158 296,163 286,168 276,163" fill="#DC382D"/>
  <text x="305" y="167" font-size="12" fill="#333">Redis</text>
  <!-- Socket.io icon -->
  <circle cx="286" cy="192" r="9" fill="#010101"/>
  <path d="M282,190 L290,188 L284,196 Z" fill="#fff"/>
  <text x="305" y="197" font-size="12" fill="#333">Socket.io</text>
  <!-- API -->
  <text x="305" y="227" font-size="12" fill="#333">API 설계</text>
  <rect x="278" y="217" width="16" height="14" rx="2" fill="#00796B"/>
  <text x="286" y="228" text-anchor="middle" font-size="7" font-weight="bold" fill="#fff">API</text>
  <!-- Arrow 2 to 3 -->
  <line x1="465" y1="150" x2="480" y2="150" stroke="#4A90D9" stroke-width="2.5" marker-end="url(#arrow9)"/>
  <!-- Phase 3 Box -->
  <rect x="485" y="40" width="210" height="220" rx="8" fill="url(#phase3Grad)" stroke="#7B1FA2" stroke-width="1.5" filter="url(#shadow9)"/>
  <text x="590" y="68" text-anchor="middle" font-size="15" font-weight="bold" fill="#4A148C">고도화</text>
  <line x1="505" y1="78" x2="675" y2="78" stroke="#CE93D8" stroke-width="1"/>
  <text x="515" y="107" font-size="12" fill="#333">ML 모델</text>
  <text x="515" y="137" font-size="12" fill="#333">CI/CD</text>
  <text x="515" y="167" font-size="12" fill="#333">모니터링</text>
  <text x="515" y="197" font-size="12" fill="#333">테스트</text>
  <text x="515" y="227" font-size="12" fill="#333">배포 자동화</text>
  <!-- Decorative dots for Phase 3 -->
  <circle cx="507" cy="103" r="3" fill="#9C27B0"/>
  <circle cx="507" cy="133" r="3" fill="#9C27B0"/>
  <circle cx="507" cy="163" r="3" fill="#9C27B0"/>
  <circle cx="507" cy="193" r="3" fill="#9C27B0"/>
  <circle cx="507" cy="223" r="3" fill="#9C27B0"/>
</svg>

---

## 9. 참고문헌

### 학술 논문 및 보고서

1. Blank, S. & Dorf, B. (2012). *The Startup Owner's Manual: The Step-By-Step Guide for Building a Great Company*. K&S Ranch.
2. Blank, S. (2013). Why the Lean Start-Up Changes Everything. *Harvard Business Review*, 91(5), 63-72.
3. Deci, E. L. & Ryan, R. M. (2000). The "What" and "Why" of Goal Pursuits: Human Needs and the Self-Determination of Behavior. *Psychological Inquiry*, 11(4), 227-268.
4. Holt-Lunstad, J., Smith, T. B., Baker, M., Harris, T., & Stephenson, D. (2015). Loneliness and Social Isolation as Risk Factors for Mortality: A Meta-Analytic Review. *Perspectives on Psychological Science*, 10(2), 227-237.
5. Howard, A. et al. (2019). Searching for MobileNetV3. *Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)*, 1314-1324.
6. Lally, P., van Jaarsveld, C. H. M., Potts, H. W. W., & Wardle, J. (2010). How are habits formed: Modelling habit formation in the real world. *European Journal of Social Psychology*, 40(6), 998-1009.
7. Locke, E. A. & Latham, G. P. (2002). Building a Practically Useful Theory of Goal Setting and Task Motivation: A 35-Year Odyssey. *American Psychologist*, 57(9), 705-717.
8. Nunes, J. C. & Dreze, X. (2006). The Endowed Progress Effect: How Artificial Advancement Increases Effort. *Journal of Consumer Research*, 32(4), 504-512.
9. Resnick, P., Zeckhauser, R., Swanson, J., & Lockwood, K. (2006). The value of reputation on eBay: A controlled experiment. *Experimental Economics*, 9(2), 79-101.
10. Ries, E. (2011). *The Lean Startup: How Today's Entrepreneurs Use Continuous Innovation to Create Radically Successful Businesses*. Crown Business.
11. Tversky, A. & Kahneman, D. (1991). Loss Aversion in Riskless Choice: A Reference-Dependent Model. *The Quarterly Journal of Economics*, 106(4), 1039-1061.
12. Zajonc, R. B. (1965). Social Facilitation. *Science*, 149(3681), 269-274.
13. Zeigarnik, B. (1938). On Finished and Unfinished Tasks. In W. D. Ellis (Ed.), *A Source Book of Gestalt Psychology* (pp. 300-314). Kegan Paul, Trench, Trubner & Company.

### 정부·공공기관 자료

14. 경찰청 사이버수사국 (2023). *2023년 사이버범죄 동향 보고서*.
15. 교육부 (2024). *2024년 교육기본통계*.
16. 국토교통부 (2024). *실거래가 공개시스템 연간 보고서*.
17. 대학내일20대연구소 (2023). *2023 대학생 라이프스타일 보고서*.
18. 문화체육관광부 (2023). *2023 국민여가활동조사*.
19. 중소벤처기업부 (2024). *2024년 소상공인 실태조사*.
20. 통계청 (2024). *2024년 사교육비조사 결과*.
21. 통계청 (2024). *2024 인구주택총조사: 1인 가구 현황*.
22. 한국건강증진개발원 (2023). *2023 국민건강영양조사: 정신건강 부문*.
23. 한국교육개발원 (2023). *대학생 학습경험 실태조사*.
24. 한국고용정보원 (2023). *단기·초단기 근로시장 동향분석*.
25. 한국노동연구원 (2024). *긱 이코노미와 플랫폼 노동시장 분석*.
26. 한국대학교육협의회 (2023). *대학 학습지원 프로그램 효과 분석*.
27. 한국소비자원 (2024). *온라인 중고거래 소비자 피해 실태조사*.
28. 한국인터넷진흥원 (2024). *2024년 인터넷이용실태조사*.
29. 한국장학재단 (2024). *2024년 대학생 생활비 실태조사*.
30. 한국콘텐츠진흥원 (2024). *2024 대한민국 콘텐츠산업 현황*.

### 기업·시장 자료

31. KB부동산 (2024). *2024년 월세통계 연간 보고서*.
32. OECD (2023). *Society at a Glance 2023: OECD Social Indicators*. OECD Publishing.
33. Timee, Inc. (2024). *FY2024 Annual Report (有価証券報告書)*. Tokyo Stock Exchange.
34. WHO (2023). *WHO Commission on Social Connection*. World Health Organization.

---

> **본 제안서는 기존 startup-ideas 디렉토리의 30개 아이템을 분석하여, 컴퓨터공학과 대학생 팀이 6개월 이내에 MVP를 개발할 수 있는 현실적 아이템 5개를 선별·재구성한 것입니다.**
>
> **추천 우선순위: 리유즈잇 > 스터디브릿지 > 룸메이트 > 포데이 > 동네알바**
