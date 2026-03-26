# 소상공인 재고 관리 클라우드 POS 시스템 (StockPOS)

## 2026년 예비창업패키지 사업계획서

---

### 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | 스톡포스(StockPOS) |
| 대표자 | 강매출 |
| 팀 구성 | 4인 (풀스택 2, 데이터 분석 1, 소상공인 컨설팅 1) |
| 창업 아이템 | 소상공인 특화 실시간 재고 관리 및 클라우드 POS 시스템 |
| 아이템 분야 | SaaS / B2B |
| 사업화 단계 | MVP 개발 완료 |
| 목표 시장 | 동네 카페, 음식점, 편의점, 소규모 소매점 |
| 신청 금액 | 1억원 |

> **"사장님의 시간을 아끼고, 매출을 올리는 똑똑한 파트너."**

---

### 창업 아이템 개요

| 항목 | 내용 |
|------|------|
| 아이템명 | StockPOS - 소상공인 재고 관리 클라우드 POS |
| 핵심 기술 | 클라우드 기반 POS, 실시간 재고 추적, AI 자동 발주, 매출 분석 |
| 목표 고객 | 소상공인 (카페, 음식점, 소매점), 프랜차이즈 소규모 가맹점 |
| 문제 정의 | 소상공인 61%가 수기 재고 관리, 재고 오차로 인한 손실 연 평균 420만원 (소상공인진흥공단, 2025) |
| 솔루션 | 태블릿 POS로 판매/재고 실시간 연동 + AI 자동 발주 + 매출/직원 관리 |
| 비즈니스 모델 | SaaS 월 구독 + POS 하드웨어 판매/렌탈 + 부가서비스 |
| 시장 규모 | 국내 POS 시스템 시장 1.2조원 (2025), 클라우드 POS 세그먼트 3,500억원 |
| 경쟁 우위 | 재고 관리 통합, AI 발주 추천, 소상공인 맞춤 UX, 월 2.9만원 저렴한 가격 |
| 수익 목표 | 3년 내 연매출 35억원 |

---

## 1. 문제 인식

### 소상공인 매장 운영 현황

| 지표 | 수치 | 출처 |
|------|------|------|
| 국내 소상공인 수 | 약 790만명 | 중소벤처기업부 (2025) |
| 수기 재고 관리 비율 | 61% | 소상공인진흥공단 (2025) |
| 재고 오차로 인한 연 손실 | 평균 420만원/매장 | 소상공인진흥공단 (2025) |
| POS 미사용 또는 구형 POS 비율 | 47% | 한국정보화진흥원 (2025) |
| 소상공인 폐업률 (1년 내) | 23.4% | 통계청 (2025) |
| 디지털 경영 관리 도구 사용률 | 22% | 중소벤처기업부 (2025) |
| 매출 분석 미실시 비율 | 54% | 소상공인진흥공단 (2025) |

소상공인의 절반 이상이 여전히 수기 또는 엑셀로 재고를 관리하고 있으며, 이로 인한 재고 오차, 품절, 과잉 발주가 빈번하다. 기존 POS 시스템은 결제 기능에 치우쳐 재고 관리와의 연동이 미흡하고, 초기 설치비가 높아 소상공인 도입 장벽이 크다.

### 사용자 페인포인트 분석

**소상공인 인터뷰 (N=150, 2025년 자체 조사)**

> "재고 파악하려면 직접 다 세봐야 해요. 한 달에 한 번 하는데도 반나절이 걸려요." - 강남 카페 사장님 김OO (38세)

> "주말에 재료가 떨어지면 급하게 사러 가야 해요. 미리 알았으면 좋겠어요." - 분식점 사장님 이OO (45세)

> "POS 바꾸려면 200만원 넘게 들어요. 그 돈이면 재료를 더 사죠." - 치킨집 사장님 박OO (52세)

| 페인포인트 | 응답 비율 | 심각도 (5점 만점) |
|-----------|----------|-----------------|
| 재고 파악에 많은 시간 소요 | 81.3% | 4.5 |
| 품절로 인한 매출 손실 | 73.2% | 4.7 |
| 과잉 발주로 인한 폐기 손실 | 68.7% | 4.3 |
| 높은 POS 초기 도입 비용 | 65.4% | 4.0 |
| 매출/수익 분석의 어려움 | 61.8% | 3.9 |
| 직원 관리(출퇴근/급여) 번거로움 | 54.2% | 3.6 |
| 공급업체 발주 과정 비효율 | 48.7% | 3.4 |

### 재고 관리 실패로 인한 손실 구조

| 손실 유형 | 연간 손실액 (매장 평균) | 비율 |
|----------|----------------------|------|
| 과잉 발주로 인한 폐기 | 약 180만원 | 43% |
| 품절로 인한 매출 손실 | 약 150만원 | 36% |
| 재고 오차(도난/파손 미파악) | 약 60만원 | 14% |
| 비효율적 발주(비교 부재) | 약 30만원 | 7% |
| **합계** | **약 420만원** | **100%** |

국내 소상공인 790만명 기준 재고 관리 부실로 인한 총 손실은 연간 약 33조원에 달한다. 이는 소상공인 폐업의 주요 원인 중 하나인 자금 관리 실패와 직결된다.

---

## 2. 솔루션

### 시스템 아키텍처

<div align="center">
<svg width="850" height="530" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow3" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#4A90D9"/>
    </marker>
    <filter id="shadow3" x="-5%" y="-5%" width="110%" height="110%">
      <feDropShadow dx="2" dy="2" stdDeviation="3" flood-opacity="0.15"/>
    </filter>
  </defs>
  <text x="425" y="30" text-anchor="middle" font-size="18" font-weight="bold" fill="#2C3E50">StockPOS 시스템 아키텍처</text>
  <!-- Tablet POS -->
  <rect x="30" y="60" width="170" height="160" rx="12" fill="#E8F4FD" stroke="#4A90D9" stroke-width="2" filter="url(#shadow3)"/>
  <text x="115" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">태블릿 POS</text>
  <text x="115" y="110" text-anchor="middle" font-size="11" fill="#555">Kotlin + Jetpack Compose</text>
  <text x="115" y="130" text-anchor="middle" font-size="11" fill="#555">결제 처리 (카드/간편)</text>
  <text x="115" y="150" text-anchor="middle" font-size="11" fill="#555">바코드 스캔 (입고/판매)</text>
  <text x="115" y="170" text-anchor="middle" font-size="11" fill="#555">주문 관리</text>
  <text x="115" y="190" text-anchor="middle" font-size="11" fill="#555">영수증 출력</text>
  <!-- Arrow -->
  <line x1="200" y1="140" x2="280" y2="140" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow3)"/>
  <text x="240" y="130" text-anchor="middle" font-size="9" fill="#4A90D9">실시간 동기화</text>
  <!-- Cloud Server -->
  <rect x="280" y="60" width="180" height="160" rx="12" fill="#E8F8E8" stroke="#50C878" stroke-width="2" filter="url(#shadow3)"/>
  <text x="370" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#50C878">클라우드 서버</text>
  <text x="370" y="110" text-anchor="middle" font-size="11" fill="#555">Spring Boot + Kafka</text>
  <text x="370" y="130" text-anchor="middle" font-size="11" fill="#555">주문 처리 엔진</text>
  <text x="370" y="150" text-anchor="middle" font-size="11" fill="#555">재고 실시간 갱신</text>
  <text x="370" y="170" text-anchor="middle" font-size="11" fill="#555">AI 발주 추천</text>
  <text x="370" y="190" text-anchor="middle" font-size="11" fill="#555">데이터 백업/동기화</text>
  <!-- Arrow -->
  <line x1="460" y1="140" x2="540" y2="140" stroke="#4A90D9" stroke-width="2" marker-end="url(#arrow3)"/>
  <text x="500" y="130" text-anchor="middle" font-size="9" fill="#4A90D9">대시보드</text>
  <!-- Dashboard -->
  <rect x="540" y="60" width="180" height="160" rx="12" fill="#FFF3E0" stroke="#FF9800" stroke-width="2" filter="url(#shadow3)"/>
  <text x="630" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#FF9800">관리자 대시보드</text>
  <text x="630" y="110" text-anchor="middle" font-size="11" fill="#555">React + Recharts</text>
  <text x="630" y="130" text-anchor="middle" font-size="11" fill="#555">매출 분석 (일/주/월)</text>
  <text x="630" y="150" text-anchor="middle" font-size="11" fill="#555">재고 현황 리포트</text>
  <text x="630" y="170" text-anchor="middle" font-size="11" fill="#555">직원 관리</text>
  <text x="630" y="190" text-anchor="middle" font-size="11" fill="#555">수익성 분석</text>
  <!-- Bottom Row -->
  <rect x="30" y="310" width="170" height="140" rx="12" fill="#F3E5F5" stroke="#9B59B6" stroke-width="2" filter="url(#shadow3)"/>
  <text x="115" y="335" text-anchor="middle" font-size="14" font-weight="bold" fill="#9B59B6">결제 단말기</text>
  <text x="115" y="360" text-anchor="middle" font-size="11" fill="#555">카드 결제 (IC/NFC)</text>
  <text x="115" y="380" text-anchor="middle" font-size="11" fill="#555">삼성페이/카카오페이</text>
  <text x="115" y="400" text-anchor="middle" font-size="11" fill="#555">네이버페이/제로페이</text>
  <text x="115" y="420" text-anchor="middle" font-size="11" fill="#555">현금영수증 발급</text>
  <line x1="115" y1="310" x2="115" y2="225" stroke="#9B59B6" stroke-width="2" marker-end="url(#arrow3)"/>
  <!-- Supplier -->
  <rect x="280" y="310" width="180" height="140" rx="12" fill="#FFEBEE" stroke="#FF6B6B" stroke-width="2" filter="url(#shadow3)"/>
  <text x="370" y="335" text-anchor="middle" font-size="14" font-weight="bold" fill="#FF6B6B">공급업체 연동</text>
  <text x="370" y="360" text-anchor="middle" font-size="11" fill="#555">AI 자동 발주 전송</text>
  <text x="370" y="380" text-anchor="middle" font-size="11" fill="#555">입고 확인/검수</text>
  <text x="370" y="400" text-anchor="middle" font-size="11" fill="#555">거래처 가격 비교</text>
  <text x="370" y="420" text-anchor="middle" font-size="11" fill="#555">발주 이력 관리</text>
  <line x1="370" y1="310" x2="370" y2="225" stroke="#FF6B6B" stroke-width="2" marker-end="url(#arrow3)"/>
  <!-- Owner App -->
  <rect x="540" y="310" width="180" height="140" rx="12" fill="#FFF9C4" stroke="#E6A800" stroke-width="2" filter="url(#shadow3)"/>
  <text x="630" y="335" text-anchor="middle" font-size="14" font-weight="bold" fill="#E6A800">사장님 앱</text>
  <text x="630" y="360" text-anchor="middle" font-size="11" fill="#555">원격 실시간 매출 확인</text>
  <text x="630" y="380" text-anchor="middle" font-size="11" fill="#555">재고 부족 알림</text>
  <text x="630" y="400" text-anchor="middle" font-size="11" fill="#555">직원 출퇴근 관리</text>
  <text x="630" y="420" text-anchor="middle" font-size="11" fill="#555">발주 승인</text>
  <line x1="630" y1="310" x2="630" y2="225" stroke="#E6A800" stroke-width="2" marker-end="url(#arrow3)"/>
  <!-- Security note -->
  <rect x="250" y="480" width="350" height="30" rx="8" fill="#F0F0F0" stroke="#CCC" stroke-width="1"/>
  <text x="425" y="500" text-anchor="middle" font-size="11" fill="#666">PCI DSS 준수 | TLS 1.3 | AES-256 데이터 암호화</text>
</svg>
</div>

### 서비스 흐름도 (사용자 플로우)

<div align="center">
<svg width="820" height="370" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrowf3" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#50C878"/>
    </marker>
  </defs>
  <text x="410" y="25" text-anchor="middle" font-size="16" font-weight="bold" fill="#2C3E50">StockPOS 매장 운영 플로우</text>
  <!-- Morning -->
  <rect x="20" y="55" width="145" height="85" rx="10" fill="#4A90D9"/>
  <text x="92" y="80" text-anchor="middle" font-size="12" font-weight="bold" fill="white">오픈 준비</text>
  <text x="92" y="98" text-anchor="middle" font-size="10" fill="#E0E0E0">직원 출근 체크</text>
  <text x="92" y="114" text-anchor="middle" font-size="10" fill="#E0E0E0">재고 현황 확인</text>
  <text x="92" y="130" text-anchor="middle" font-size="10" fill="#E0E0E0">발주 추천 검토</text>
  <line x1="165" y1="97" x2="190" y2="97" stroke="#50C878" stroke-width="2" marker-end="url(#arrowf3)"/>
  <!-- Sales -->
  <rect x="190" y="55" width="145" height="85" rx="10" fill="#50C878"/>
  <text x="262" y="80" text-anchor="middle" font-size="12" font-weight="bold" fill="white">판매/결제</text>
  <text x="262" y="98" text-anchor="middle" font-size="10" fill="#E0E0E0">POS 주문 입력</text>
  <text x="262" y="114" text-anchor="middle" font-size="10" fill="#E0E0E0">카드/간편결제</text>
  <text x="262" y="130" text-anchor="middle" font-size="10" fill="#E0E0E0">재고 자동 차감</text>
  <line x1="335" y1="97" x2="360" y2="97" stroke="#50C878" stroke-width="2" marker-end="url(#arrowf3)"/>
  <!-- Stock -->
  <rect x="360" y="55" width="145" height="85" rx="10" fill="#FF9800"/>
  <text x="432" y="80" text-anchor="middle" font-size="12" font-weight="bold" fill="white">재고 관리</text>
  <text x="432" y="98" text-anchor="middle" font-size="10" fill="#E0E0E0">실시간 재고 갱신</text>
  <text x="432" y="114" text-anchor="middle" font-size="10" fill="#E0E0E0">부족 시 알림</text>
  <text x="432" y="130" text-anchor="middle" font-size="10" fill="#E0E0E0">AI 발주 추천</text>
  <line x1="505" y1="97" x2="530" y2="97" stroke="#50C878" stroke-width="2" marker-end="url(#arrowf3)"/>
  <!-- Analysis -->
  <rect x="530" y="55" width="130" height="85" rx="10" fill="#9B59B6"/>
  <text x="595" y="80" text-anchor="middle" font-size="12" font-weight="bold" fill="white">매출 분석</text>
  <text x="595" y="98" text-anchor="middle" font-size="10" fill="#E0E0E0">일간/주간 리포트</text>
  <text x="595" y="114" text-anchor="middle" font-size="10" fill="#E0E0E0">인기 메뉴 분석</text>
  <text x="595" y="130" text-anchor="middle" font-size="10" fill="#E0E0E0">수익성 분석</text>
  <line x1="660" y1="97" x2="685" y2="97" stroke="#50C878" stroke-width="2" marker-end="url(#arrowf3)"/>
  <!-- Close -->
  <rect x="685" y="55" width="115" height="85" rx="10" fill="#FF6B6B"/>
  <text x="742" y="80" text-anchor="middle" font-size="12" font-weight="bold" fill="white">마감</text>
  <text x="742" y="98" text-anchor="middle" font-size="10" fill="#E0E0E0">일 마감 정산</text>
  <text x="742" y="114" text-anchor="middle" font-size="10" fill="#E0E0E0">직원 퇴근 체크</text>
  <text x="742" y="130" text-anchor="middle" font-size="10" fill="#E0E0E0">익일 발주 확정</text>
  <!-- Key benefits -->
  <rect x="80" y="180" width="200" height="55" rx="10" fill="#E8F4FD" stroke="#4A90D9" stroke-width="1.5"/>
  <text x="180" y="203" text-anchor="middle" font-size="12" font-weight="bold" fill="#4A90D9">재고 정확도 98%+</text>
  <text x="180" y="222" text-anchor="middle" font-size="10" fill="#555">실시간 자동 차감/입고</text>
  <rect x="310" y="180" width="200" height="55" rx="10" fill="#E8F8E8" stroke="#50C878" stroke-width="1.5"/>
  <text x="410" y="203" text-anchor="middle" font-size="12" font-weight="bold" fill="#50C878">발주 비용 15% 절감</text>
  <text x="410" y="222" text-anchor="middle" font-size="10" fill="#555">AI 수요 예측 기반 최적 발주</text>
  <rect x="540" y="180" width="200" height="55" rx="10" fill="#FFF3E0" stroke="#FF9800" stroke-width="1.5"/>
  <text x="640" y="203" text-anchor="middle" font-size="12" font-weight="bold" fill="#FF9800">관리 시간 60% 절감</text>
  <text x="640" y="222" text-anchor="middle" font-size="10" fill="#555">수기 → 자동 재고/매출 관리</text>
  <!-- Bottom impact -->
  <rect x="230" y="275" width="360" height="55" rx="15" fill="#2C3E50"/>
  <text x="410" y="300" text-anchor="middle" font-size="14" font-weight="bold" fill="#FFD93D">매장당 연 420만원 손실 방지</text>
  <text x="410" y="320" text-anchor="middle" font-size="11" fill="#CCC">재고 오차 제거 + AI 발주 최적화 + 매출 분석</text>
</svg>
</div>

### 주요 기능 상세

- **실시간 재고 추적**: POS 판매 시 자동 재고 차감, 바코드/수기 입고로 실시간 재고 현황 파악. 재고 수량이 안전 재고 이하로 떨어지면 즉시 알림을 발송하며, 재고 실사 기능으로 주기적 검수도 지원한다. 유통기한이 있는 식자재의 경우 FIFO(선입선출) 관리를 자동 적용한다.

- **AI 자동 발주 추천**: Prophet 시계열 예측 모델이 판매 패턴, 요일별/계절별 수요 변동, 날씨, 주변 이벤트를 분석하여 최적 발주 시점과 수량을 추천한다. XGBoost 최적화 모델이 과잉 발주와 품절 사이의 최적 재고 수준을 산출하며, 원클릭 발주로 공급업체에 바로 주문을 전송할 수 있다.

- **매출 분석 대시보드**: 일별/주별/월별 매출 추이, 인기 메뉴 TOP10, 시간대별 매출 히트맵, 메뉴별 수익성(원가율) 분석을 직관적 시각화로 제공한다. 전년 동기 대비, 전월 대비 성장률을 자동 산출하고, 매출 목표 달성률을 실시간 트래킹한다.

- **직원 관리**: 직원별 출퇴근 기록(QR코드/앱), 근무 시간 자동 집계, 주휴수당 포함 급여 자동 계산, 스케줄 관리를 제공한다. 직원별 매출 기여도 분석으로 성과 관리도 가능하다.

- **다지점 통합 관리**: 여러 매장을 운영하는 소규모 프랜차이즈의 경우 본사에서 전체 지점의 매출, 재고, 직원을 통합 관리할 수 있다. 지점별 비교 분석 리포트를 자동 생성한다.

- **오프라인 모드**: 인터넷 끊김 시에도 POS 결제와 주문이 정상 동작하며, 연결 복구 시 자동으로 클라우드에 동기화한다.

### AI 수요 예측 알고리즘 상세

| 단계 | 모델 | 입력 데이터 | 출력 | 정확도 |
|------|------|-----------|------|--------|
| 1단계: 수요 예측 | Prophet | 과거 판매량, 요일, 계절, 공휴일 | 향후 7일 품목별 수요 | MAPE 12.5% |
| 2단계: 발주 최적화 | XGBoost | 수요 예측, 리드타임, 안전재고, 단가 | 최적 발주량/시점 | 과잉 발주 23% 감소 |
| 3단계: 외부 변수 보정 | Linear Regression | 날씨, 주변 이벤트, 경쟁점 개점 | 수요 보정 계수 | 예측 정확도 8% 향상 |

---

## 3. 시장 분석

### TAM / SAM / SOM 시장 규모

<div align="center">
<svg width="600" height="420" xmlns="http://www.w3.org/2000/svg">
  <text x="300" y="30" text-anchor="middle" font-size="16" font-weight="bold" fill="#2C3E50">시장 규모 (TAM / SAM / SOM)</text>
  <!-- TAM -->
  <circle cx="300" cy="230" r="170" fill="#E8F4FD" stroke="#4A90D9" stroke-width="2.5"/>
  <text x="300" y="85" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">TAM: 1.2조원</text>
  <text x="300" y="103" text-anchor="middle" font-size="11" fill="#666">국내 POS 시스템 전체 시장</text>
  <!-- SAM -->
  <circle cx="300" cy="260" r="110" fill="#E8F8E8" stroke="#50C878" stroke-width="2.5"/>
  <text x="300" y="185" text-anchor="middle" font-size="14" font-weight="bold" fill="#50C878">SAM: 3,500억원</text>
  <text x="300" y="203" text-anchor="middle" font-size="11" fill="#666">클라우드 POS 시장</text>
  <!-- SOM -->
  <circle cx="300" cy="290" r="55" fill="#FFF3E0" stroke="#FF9800" stroke-width="2.5"/>
  <text x="300" y="283" text-anchor="middle" font-size="13" font-weight="bold" fill="#FF9800">SOM</text>
  <text x="300" y="300" text-anchor="middle" font-size="12" font-weight="bold" fill="#FF9800">150억원</text>
  <!-- Growth -->
  <rect x="180" y="380" width="240" height="30" rx="8" fill="#2C3E50"/>
  <text x="300" y="400" text-anchor="middle" font-size="12" font-weight="bold" fill="#FFD93D">연평균 성장률(CAGR): 18%</text>
</svg>
</div>

| 구분 | 규모 | 산출 근거 |
|------|------|-----------|
| TAM (전체 시장) | 1.2조원 | 국내 POS 시스템 전체 시장 |
| SAM (유효 시장) | 3,500억원 | 클라우드 기반 POS 시스템 시장 |
| SOM (목표 시장) | 150억원 | 수도권 카페/음식점 소상공인 초기 공략 |

### 시장 성장 동력

소상공인의 디지털 전환 지원 정책과 클라우드 POS의 낮은 초기 비용 장점으로 시장 전환이 가속화되고 있다. 중소벤처기업부의 스마트상점 보급 사업이 시장 성장을 직접 견인한다.

| 성장 동력 | 상세 내용 | 영향도 |
|----------|----------|--------|
| 정부 스마트상점 지원 | 2026년 스마트상점 보급 목표 5만개, 클라우드 POS 도입 보조금 | 매우 높음 |
| 클라우드 전환 가속 | 구형 POS → 클라우드 POS 교체 수요 증가 | 높음 |
| 간편결제 확산 | 카카오페이/네이버페이 등 간편결제 의무 지원 요구 | 높음 |
| 배달앱 연동 수요 | 배달의민족/쿠팡이츠 등 배달 주문 POS 연동 필요 | 중간 |
| 인건비 상승 | 최저임금 인상으로 효율적 직원 관리 도구 수요 | 중간 |
| 데이터 경영 인식 확산 | 소상공인 데이터 기반 경영 관심 증가 | 중간 |

---

## 4. 비즈니스 모델

### 비즈니스 모델 다이어그램

<div align="center">
<svg width="850" height="400" xmlns="http://www.w3.org/2000/svg">
  <text x="425" y="25" text-anchor="middle" font-size="16" font-weight="bold" fill="#2C3E50">StockPOS 수익 모델 구조</text>
  <!-- SaaS Tiers -->
  <rect x="30" y="55" width="250" height="120" rx="10" fill="#E8F4FD" stroke="#4A90D9" stroke-width="2"/>
  <text x="155" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#4A90D9">Lite (월 2.9만원)</text>
  <text x="155" y="100" text-anchor="middle" font-size="10" fill="#555">POS 결제, 기본 재고, 일간 매출</text>
  <text x="155" y="116" text-anchor="middle" font-size="10" fill="#555">직원 2명, 단일 POS</text>
  <rect x="80" y="130" width="150" height="22" rx="5" fill="#4A90D9"/>
  <text x="155" y="145" text-anchor="middle" font-size="10" fill="white">SaaS 수익 57%</text>
  <!-- Standard -->
  <rect x="300" y="55" width="250" height="120" rx="10" fill="#E8F8E8" stroke="#50C878" stroke-width="2"/>
  <text x="425" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#50C878">Standard (월 5.9만원)</text>
  <text x="425" y="100" text-anchor="middle" font-size="10" fill="#555">AI 발주, 상세 분석, 공급업체 연동</text>
  <text x="425" y="116" text-anchor="middle" font-size="10" fill="#555">직원 10명, 멀티 POS</text>
  <rect x="350" y="130" width="150" height="22" rx="5" fill="#50C878"/>
  <text x="425" y="145" text-anchor="middle" font-size="10" fill="white">핵심 성장 요금제</text>
  <!-- Premium -->
  <rect x="570" y="55" width="250" height="120" rx="10" fill="#FFF3E0" stroke="#FF9800" stroke-width="2"/>
  <text x="695" y="80" text-anchor="middle" font-size="14" font-weight="bold" fill="#FF9800">Premium (월 9.9만원)</text>
  <text x="695" y="100" text-anchor="middle" font-size="10" fill="#555">다지점 통합, 수익성 분석, CRM</text>
  <text x="695" y="116" text-anchor="middle" font-size="10" fill="#555">전담 매니저, 맞춤 리포트</text>
  <rect x="620" y="130" width="150" height="22" rx="5" fill="#FF9800"/>
  <text x="695" y="145" text-anchor="middle" font-size="10" fill="white">높은 ARPU 고객</text>
  <!-- Hardware -->
  <rect x="30" y="210" width="380" height="65" rx="10" fill="#F3E5F5" stroke="#9B59B6" stroke-width="1.5"/>
  <text x="220" y="237" text-anchor="middle" font-size="13" font-weight="bold" fill="#9B59B6">하드웨어 렌탈/판매 (23%)</text>
  <text x="220" y="258" text-anchor="middle" font-size="11" fill="#555">태블릿 POS 세트: 렌탈 월 3만원 / 구매 59만원</text>
  <!-- Additional -->
  <rect x="440" y="210" width="380" height="65" rx="10" fill="#FFEBEE" stroke="#FF6B6B" stroke-width="1.5"/>
  <text x="630" y="237" text-anchor="middle" font-size="13" font-weight="bold" fill="#FF6B6B">부가서비스 (20%)</text>
  <text x="630" y="258" text-anchor="middle" font-size="11" fill="#555">결제 수수료 차익 | 대출/보험 중개 | 공급업체 매칭</text>
  <!-- Unit economics -->
  <rect x="150" y="310" width="550" height="70" rx="12" fill="#2C3E50"/>
  <text x="425" y="337" text-anchor="middle" font-size="13" font-weight="bold" fill="#FFD93D">유닛 이코노믹스</text>
  <text x="425" y="360" text-anchor="middle" font-size="11" fill="#CCC">ARPU: 월 5.8만원 | LTV: 278만원 (48개월) | CAC: 35만원 | LTV/CAC: 7.9x</text>
</svg>
</div>

| 요금제 | 월 비용 | 포함 내용 |
|--------|---------|-----------|
| Lite | 월 2.9만원 | POS 결제, 기본 재고 관리, 일간 매출 요약, 직원 2명 |
| Standard | 월 5.9만원 | AI 자동 발주, 상세 매출 분석, 공급업체 연동, 직원 10명, 멀티 POS |
| Premium | 월 9.9만원 | 다지점 통합 관리, 수익성 분석, 맞춤 리포트, CRM, 전담 매니저 |

### 유닛 이코노믹스

| 지표 | 수치 | 비고 |
|------|------|------|
| 월 ARPU (가중 평균) | 5.8만원 | Lite 40% / Standard 40% / Premium 20% |
| 평균 구독 유지 기간 | 48개월 | B2B SaaS 특성상 높은 유지율 |
| 월 해지율 (Churn) | 2.1% | 업계 평균 4~5% 대비 양호 |
| LTV (고객 생애 가치) | 약 278만원 | ARPU x 유지기간 |
| CAC (고객 획득 비용) | 약 35만원 | 현장 영업 + 디지털 마케팅 |
| LTV / CAC 비율 | 7.9x | 매우 건전 |
| 하드웨어 마진율 | 35% | 렌탈 모델 기준 |

하드웨어: 태블릿 POS 세트(태블릿+거치대+프린터) 렌탈 월 3만원 또는 구매 59만원. 추가 수익: 카드 결제 수수료 차익, 소상공인 대출/보험 중개, 공급업체 매칭 수수료.

### 고객 획득 전략

| 단계 | 전략 | 목표 | 예상 CAC |
|------|------|------|---------|
| 1단계 (파일럿) | 서울 카페 골목 직접 영업, 무료 체험 1개월 | 50곳 | 10만원 |
| 2단계 (초기 성장) | 소상공인 커뮤니티(아프니까 사장이다), 네이버 카페 | 500곳 | 25만원 |
| 3단계 (스마트상점) | 정부 스마트상점 보급사업 참여, 보조금 활용 | 1,000곳 | 15만원 |
| 4단계 (B2B) | 소규모 프랜차이즈 본사 직접 영업 | 3,000곳 | 40만원 |

---

## 5. 기술 스택

### 기술 스택 다이어그램

<div align="center">
<svg width="800" height="430" xmlns="http://www.w3.org/2000/svg">
  <text x="400" y="25" text-anchor="middle" font-size="16" font-weight="bold" fill="#2C3E50">StockPOS 기술 스택</text>
  <!-- POS App -->
  <rect x="50" y="50" width="700" height="60" rx="10" fill="#E8F4FD" stroke="#4A90D9" stroke-width="2"/>
  <text x="95" y="78" font-size="13" font-weight="bold" fill="#4A90D9">POS 앱</text>
  <rect x="160" y="62" width="130" height="28" rx="6" fill="#4A90D9"/>
  <text x="225" y="81" text-anchor="middle" font-size="10" fill="white">Kotlin</text>
  <rect x="300" y="62" width="140" height="28" rx="6" fill="#4A90D9"/>
  <text x="370" y="81" text-anchor="middle" font-size="10" fill="white">Jetpack Compose</text>
  <rect x="450" y="62" width="130" height="28" rx="6" fill="#4A90D9"/>
  <text x="515" y="81" text-anchor="middle" font-size="10" fill="white">Flutter (사장님앱)</text>
  <rect x="590" y="62" width="140" height="28" rx="6" fill="#4A90D9"/>
  <text x="660" y="81" text-anchor="middle" font-size="10" fill="white">React (대시보드)</text>
  <!-- Backend -->
  <rect x="50" y="125" width="700" height="60" rx="10" fill="#E8F8E8" stroke="#50C878" stroke-width="2"/>
  <text x="95" y="153" font-size="13" font-weight="bold" fill="#50C878">백엔드</text>
  <rect x="160" y="137" width="120" height="28" rx="6" fill="#50C878"/>
  <text x="220" y="156" text-anchor="middle" font-size="10" fill="white">Spring Boot</text>
  <rect x="290" y="137" width="110" height="28" rx="6" fill="#50C878"/>
  <text x="345" y="156" text-anchor="middle" font-size="10" fill="white">PostgreSQL</text>
  <rect x="410" y="137" width="80" height="28" rx="6" fill="#50C878"/>
  <text x="450" y="156" text-anchor="middle" font-size="10" fill="white">Redis</text>
  <rect x="500" y="137" width="110" height="28" rx="6" fill="#50C878"/>
  <text x="555" y="156" text-anchor="middle" font-size="10" fill="white">Apache Kafka</text>
  <rect x="620" y="137" width="110" height="28" rx="6" fill="#50C878"/>
  <text x="675" y="156" text-anchor="middle" font-size="10" fill="white">Recharts</text>
  <!-- AI -->
  <rect x="50" y="200" width="700" height="60" rx="10" fill="#FFF3E0" stroke="#FF9800" stroke-width="2"/>
  <text x="95" y="228" font-size="13" font-weight="bold" fill="#FF9800">AI/ML</text>
  <rect x="160" y="212" width="130" height="28" rx="6" fill="#FF9800"/>
  <text x="225" y="231" text-anchor="middle" font-size="10" fill="white">Prophet (수요예측)</text>
  <rect x="300" y="212" width="150" height="28" rx="6" fill="#FF9800"/>
  <text x="375" y="231" text-anchor="middle" font-size="10" fill="white">XGBoost (발주최적화)</text>
  <rect x="460" y="212" width="130" height="28" rx="6" fill="#FF9800"/>
  <text x="525" y="231" text-anchor="middle" font-size="10" fill="white">Linear Regression</text>
  <rect x="600" y="212" width="130" height="28" rx="6" fill="#FF9800"/>
  <text x="665" y="231" text-anchor="middle" font-size="10" fill="white">날씨 API 연동</text>
  <!-- Payment -->
  <rect x="50" y="275" width="700" height="60" rx="10" fill="#F3E5F5" stroke="#9B59B6" stroke-width="2"/>
  <text x="95" y="303" font-size="13" font-weight="bold" fill="#9B59B6">결제</text>
  <rect x="160" y="287" width="110" height="28" rx="6" fill="#9B59B6"/>
  <text x="215" y="306" text-anchor="middle" font-size="10" fill="white">KG이니시스</text>
  <rect x="280" y="287" width="100" height="28" rx="6" fill="#9B59B6"/>
  <text x="330" y="306" text-anchor="middle" font-size="10" fill="white">NHN KCP</text>
  <rect x="390" y="287" width="100" height="28" rx="6" fill="#9B59B6"/>
  <text x="440" y="306" text-anchor="middle" font-size="10" fill="white">삼성페이</text>
  <rect x="500" y="287" width="100" height="28" rx="6" fill="#9B59B6"/>
  <text x="550" y="306" text-anchor="middle" font-size="10" fill="white">카카오페이</text>
  <rect x="610" y="287" width="120" height="28" rx="6" fill="#9B59B6"/>
  <text x="670" y="306" text-anchor="middle" font-size="10" fill="white">네이버/제로페이</text>
  <!-- Cloud -->
  <rect x="50" y="350" width="700" height="60" rx="10" fill="#FFEBEE" stroke="#FF6B6B" stroke-width="2"/>
  <text x="95" y="378" font-size="13" font-weight="bold" fill="#FF6B6B">인프라</text>
  <rect x="160" y="362" width="100" height="28" rx="6" fill="#FF6B6B"/>
  <text x="210" y="381" text-anchor="middle" font-size="10" fill="white">AWS ECS</text>
  <rect x="270" y="362" width="90" height="28" rx="6" fill="#FF6B6B"/>
  <text x="315" y="381" text-anchor="middle" font-size="10" fill="white">RDS</text>
  <rect x="370" y="362" width="80" height="28" rx="6" fill="#FF6B6B"/>
  <text x="410" y="381" text-anchor="middle" font-size="10" fill="white">S3</text>
  <rect x="460" y="362" width="100" height="28" rx="6" fill="#FF6B6B"/>
  <text x="510" y="381" text-anchor="middle" font-size="10" fill="white">Lambda</text>
  <rect x="570" y="362" width="90" height="28" rx="6" fill="#FF6B6B"/>
  <text x="615" y="381" text-anchor="middle" font-size="10" fill="white">Docker</text>
  <rect x="670" y="362" width="100" height="28" rx="6" fill="#FF6B6B"/>
  <text x="720" y="381" text-anchor="middle" font-size="10" fill="white">Terraform</text>
</svg>
</div>

| 구분 | 기술 |
|------|------|
| POS 앱 | Kotlin (Android 태블릿), Jetpack Compose |
| 백엔드 | Java (Spring Boot), PostgreSQL, Redis, Apache Kafka |
| AI/ML | Prophet (수요 예측), XGBoost (발주 최적화) |
| 프론트엔드 | React (관리 대시보드), Recharts (시각화) |
| 모바일 앱 | Flutter (사장님 앱, iOS/Android) |
| 결제 | KG이니시스, NHN KCP, 삼성페이/카카오페이/네이버페이 연동 |
| 클라우드 | AWS (ECS, RDS, S3, Lambda), Docker, Terraform |
| 보안 | PCI DSS 준수, TLS 1.3, 데이터 암호화 (AES-256) |

---

## 6. 경쟁사 분석

| 항목 | StockPOS (자사) | 키오스크뱅크 | 토스페이먼츠 POS | 페이코 POS |
|------|----------------|------------|----------------|-----------|
| 실시간 재고 연동 | O | △ | X | X |
| AI 자동 발주 | O | X | X | X |
| 매출 상세 분석 | O | O | O | △ |
| 직원 관리 | O | X | △ | X |
| 공급업체 연동 | O | X | X | X |
| 다지점 관리 | O | O | O | X |
| 오프라인 모드 | O | △ | X | △ |
| 월 비용 (기본) | 2.9만원 | 3.5만원 | 무료* | 3만원 |
| 초기 하드웨어 비용 | 렌탈 3만원/월 | 80만원 | 50만원 | 60만원 |

### 경쟁 우위 요약

1. **재고 관리 통합**: 결제와 재고가 실시간 연동되는 유일한 소상공인 POS (품절/과잉발주 방지)
2. **AI 발주**: 데이터 기반 수요 예측으로 발주 비용 15% 절감, 품절률 80% 감소
3. **낮은 진입 장벽**: 렌탈 월 3만원으로 초기 비용 제로, 5분 이내 설치/셋업
4. **오프라인 지원**: 인터넷 끊김에도 결제 가능, 소상공인 실정에 맞는 안정성
5. **확장성**: 단일 매장에서 다지점 프랜차이즈까지 확장 가능한 아키텍처

---

## 7. 로드맵

### 성장 로드맵 타임라인

<div align="center">
<svg width="850" height="380" xmlns="http://www.w3.org/2000/svg">
  <text x="425" y="25" text-anchor="middle" font-size="16" font-weight="bold" fill="#2C3E50">StockPOS 성장 로드맵 (2026~2028)</text>
  <!-- Timeline -->
  <line x1="50" y1="200" x2="800" y2="200" stroke="#CCC" stroke-width="3"/>
  <!-- Markers -->
  <circle cx="120" cy="200" r="12" fill="#4A90D9"/>
  <text x="120" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q3</text>
  <text x="120" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#4A90D9">2026 Q3</text>
  <circle cx="220" cy="200" r="12" fill="#4A90D9"/>
  <text x="220" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q4</text>
  <text x="220" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#4A90D9">2026 Q4</text>
  <circle cx="340" cy="200" r="12" fill="#50C878"/>
  <text x="340" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q1</text>
  <text x="340" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#50C878">2027 Q1</text>
  <circle cx="440" cy="200" r="12" fill="#50C878"/>
  <text x="440" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q2</text>
  <text x="440" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#50C878">2027 Q2</text>
  <circle cx="540" cy="200" r="12" fill="#FF9800"/>
  <text x="540" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q3</text>
  <text x="540" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#FF9800">2027 Q3</text>
  <circle cx="620" cy="200" r="12" fill="#FF9800"/>
  <text x="620" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q4</text>
  <text x="620" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#FF9800">2027 Q4</text>
  <circle cx="710" cy="200" r="12" fill="#FF6B6B"/>
  <text x="710" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q1</text>
  <text x="710" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#FF6B6B">2028 Q1</text>
  <circle cx="780" cy="200" r="12" fill="#FF6B6B"/>
  <text x="780" y="204" text-anchor="middle" font-size="8" fill="white" font-weight="bold">Q2</text>
  <text x="780" y="230" text-anchor="middle" font-size="10" font-weight="bold" fill="#FF6B6B">2028 Q2</text>
  <!-- Milestones -->
  <rect x="55" y="100" width="130" height="65" rx="6" fill="#E8F4FD" stroke="#4A90D9" stroke-width="1"/>
  <text x="120" y="118" text-anchor="middle" font-size="9" font-weight="bold" fill="#4A90D9">MVP 파일럿</text>
  <text x="120" y="133" text-anchor="middle" font-size="8" fill="#555">서울 카페 50곳</text>
  <text x="120" y="148" text-anchor="middle" font-size="8" fill="#555">결제 단말 연동</text>
  <line x1="120" y1="165" x2="120" y2="188" stroke="#4A90D9" stroke-width="1.5"/>
  <rect x="275" y="100" width="130" height="65" rx="6" fill="#E8F8E8" stroke="#50C878" stroke-width="1"/>
  <text x="340" y="118" text-anchor="middle" font-size="9" font-weight="bold" fill="#50C878">AI 발주 출시</text>
  <text x="340" y="133" text-anchor="middle" font-size="8" fill="#555">공급업체 연동</text>
  <text x="340" y="148" text-anchor="middle" font-size="8" fill="#555">매장 500곳</text>
  <line x1="340" y1="165" x2="340" y2="188" stroke="#50C878" stroke-width="1.5"/>
  <rect x="475" y="100" width="130" height="65" rx="6" fill="#FFF3E0" stroke="#FF9800" stroke-width="1"/>
  <text x="540" y="118" text-anchor="middle" font-size="9" font-weight="bold" fill="#FF9800">스케일업</text>
  <text x="540" y="133" text-anchor="middle" font-size="8" fill="#555">시리즈 A 유치</text>
  <text x="540" y="148" text-anchor="middle" font-size="8" fill="#555">매장 3,000곳</text>
  <line x1="540" y1="165" x2="540" y2="188" stroke="#FF9800" stroke-width="1.5"/>
  <rect x="710" y="100" width="130" height="65" rx="6" fill="#FFEBEE" stroke="#FF6B6B" stroke-width="1"/>
  <text x="775" y="118" text-anchor="middle" font-size="9" font-weight="bold" fill="#FF6B6B">확장</text>
  <text x="775" y="133" text-anchor="middle" font-size="8" fill="#555">매장 8,000곳</text>
  <text x="775" y="148" text-anchor="middle" font-size="8" fill="#555">매출 35억 달성</text>
  <line x1="775" y1="165" x2="775" y2="188" stroke="#FF6B6B" stroke-width="1.5"/>
  <!-- Phase labels -->
  <rect x="55" y="280" width="200" height="25" rx="5" fill="#4A90D9" opacity="0.2"/>
  <text x="155" y="297" text-anchor="middle" font-size="10" font-weight="bold" fill="#4A90D9">Phase 1: PMF 검증</text>
  <rect x="270" y="280" width="240" height="25" rx="5" fill="#50C878" opacity="0.2"/>
  <text x="390" y="297" text-anchor="middle" font-size="10" font-weight="bold" fill="#50C878">Phase 2: 성장</text>
  <rect x="525" y="280" width="290" height="25" rx="5" fill="#FF6B6B" opacity="0.2"/>
  <text x="670" y="297" text-anchor="middle" font-size="10" font-weight="bold" fill="#FF6B6B">Phase 3: 스케일업</text>
</svg>
</div>

| 분기 | 마일스톤 |
|------|----------|
| 2026 Q3 | MVP 출시, 서울 카페/음식점 50곳 파일럿 |
| 2026 Q4 | 사용자 피드백 반영 v2, 결제 단말기 연동, 가입 매장 200곳 |
| 2027 Q1 | AI 자동 발주 v1 출시, 공급업체 연동, 가입 매장 500곳 |
| 2027 Q2 | 직원 관리 기능, 스마트상점 보급사업 참여, 가입 매장 1,000곳 |
| 2027 Q3 | 다지점 관리 출시, 소규모 프랜차이즈 공략, 시리즈 A 투자 유치 |
| 2027 Q4 | 가입 매장 3,000곳, 월 거래액 100억원 돌파 |
| 2028 Q1 | 소매점(편의점/마트) 확장, CRM 마케팅 기능 출시 |
| 2028 Q2 | 연 매출 35억원 달성, 가입 매장 8,000곳 |

---

## 8. 팀 구성

| 이름 | 역할 | 주요 경력 |
|------|------|-----------|
| 강매출 | 대표 / 풀스택 리드 | 서울대 컴퓨터공학, 토스 결제 시스템 개발 인턴, 핀테크 전문 |
| 박풀스 | 풀스택 개발 | 한양대 소프트웨어학과, POS 개발 경험, Spring Boot 전문 |
| 이데이 | 데이터 분석 / AI | KAIST 데이터사이언스, 수요예측 모델 연구, Kaggle Top 5% |
| 최사장 | 소상공인 컨설팅 / 사업개발 | 고려대 경영학과, 소상공인진흥공단 인턴, 자영업 컨설팅 경력 |

### 역할별 상세 책임

| 팀원 | 담당 영역 | 1차 목표 (6개월) |
|------|----------|-----------------|
| 강매출 (대표) | POS 앱 개발, 결제 연동, 전체 아키텍처 | POS v1 출시, PCI DSS 준수, 50곳 파일럿 |
| 박풀스 | 백엔드 API, 관리 대시보드, 인프라 운영 | Kafka 이벤트 처리, 재고 실시간 동기화 |
| 이데이 | AI 수요 예측, 발주 최적화, 데이터 파이프라인 | Prophet 모델 MAPE 15% 이하, 발주 추천 |
| 최사장 | 소상공인 현장 영업, 피드백 수집, 정부 사업 연계 | 파일럿 50곳 확보, 스마트상점 사업 참여 신청 |

### 채용 계획 (2027년)

| 직무 | 인원 | 시기 | 주요 역할 |
|------|------|------|----------|
| Android POS 개발자 | 1명 | 2027 Q1 | POS 앱 고도화, 하드웨어 연동 |
| 영업/CS 매니저 | 2명 | 2027 Q2 | 수도권 현장 영업, 고객 지원 |
| 프론트엔드 개발자 | 1명 | 2027 Q3 | 관리 대시보드 고도화, UX 개선 |
| 마케팅 매니저 | 1명 | 2027 Q3 | 스마트상점 사업 연계, 온라인 마케팅 |

---

## 9. 재무 계획

| 항목 | 2026년 | 2027년 | 2028년 |
|------|--------|--------|--------|
| 매출 | 1.5억원 | 12억원 | 35억원 |
| SaaS 구독 | 0.8억원 | 7억원 | 20억원 |
| 하드웨어 렌탈/판매 | 0.5억원 | 3억원 | 8억원 |
| 부가서비스 | 0.2억원 | 2억원 | 7억원 |
| 영업비용 | 2.5억원 | 8억원 | 22억원 |
| 영업이익 | -1억원 | 4억원 | 13억원 |
| 영업이익률 | -67% | 33% | 37% |

### 비용 구조 상세

| 비용 항목 | 2026년 | 2027년 | 2028년 | 비고 |
|----------|--------|--------|--------|------|
| 인건비 | 1.2억원 | 4.5억원 | 10억원 | 4인 -> 8인 -> 15인 |
| 하드웨어 조달 | 0.3억원 | 1.5억원 | 5억원 | 태블릿/프린터 원가 |
| 클라우드/인프라 | 0.2억원 | 0.5억원 | 1.5억원 | AWS 비용 |
| 영업/마케팅 | 0.5억원 | 1억원 | 3.5억원 | 현장 영업 + 마케팅 |
| 사무실/운영비 | 0.2억원 | 0.3억원 | 1억원 | 오피스 |
| PCI DSS 인증/법무 | 0.1억원 | 0.2억원 | 1억원 | 보안 인증 유지 |

### 손익분기점 분석

| 지표 | 수치 |
|------|------|
| 월 고정비 | 약 4,500만원 (2027년 기준) |
| 매장당 월 기여 마진 | 약 4.2만원 (SaaS + HW 렌탈) |
| 손익분기 매장 수 | 약 1,070곳 |
| 손익분기 예상 시점 | 2027년 Q2 (출시 후 약 12개월) |

---

## 10. 리스크 관리

| 리스크 | 발생 확률 | 영향도 | 대응 전략 |
|--------|----------|--------|----------|
| 결제 시스템 장애 | 낮음 | 매우 높음 | 이중화 아키텍처, 오프라인 모드 기본 탑재, 24시간 모니터링 |
| PCI DSS 인증 미취득 | 중간 | 높음 | PG사 위임결제 구조 활용, 카드정보 비저장 방식 |
| 소상공인 디지털 리터러시 | 높음 | 중간 | 5분 설치 UX, 현장 설치 지원, 전화 CS 운영 |
| 경쟁사 무료 POS 공세 | 높음 | 중간 | 재고 관리/AI 발주 차별화, 전환 비용(switching cost) 강화 |
| 하드웨어 불량/AS | 중간 | 중간 | 교체 당일 배송 보장, 안전 재고 확보 |
| 소상공인 폐업으로 인한 이탈 | 높음 | 중간 | 장기 계약 할인, 다양한 업종 확장으로 포트폴리오 분산 |
| 개인정보/결제정보 유출 | 낮음 | 매우 높음 | AES-256 암호화, 보안 감사 연 2회, 사고 대응 플랜 수립 |

---

## 11. 사회적 가치 및 임팩트

| 지표 | 목표 (2028년) |
|------|-------------|
| 관리 매장 수 | 8,000곳 |
| 매장당 연간 재고 손실 절감 | 약 300만원 |
| 총 재고 손실 절감 효과 | 연 약 240억원 |
| 소상공인 폐업 예방 기여 | 관리 매장 폐업률 15% 이하 (업계 23% 대비) |
| 디지털 전환 지원 매장 | 5,000곳+ |

StockPOS는 소상공인의 경영 효율화를 통해 자영업 생태계의 지속 가능성을 높인다. 재고 관리 자동화와 데이터 기반 의사결정 지원으로 소상공인 폐업률 감소에 기여하며, 정부의 소상공인 디지털 전환 정책의 실질적 수행 도구 역할을 한다.

---

## 12. 참고문헌

1. 소상공인진흥공단, "2025 소상공인 매장 운영 실태 조사," 2025.
2. 중소벤처기업부, "2025 소상공인 현황 통계," 2025.
3. 통계청, "2025 소상공인 생멸 현황 분석," 2025.
4. 한국정보화진흥원, "소상공인 디지털 전환 실태 보고서," 2025.
5. Taylor, S. J. & Letham, B., "Forecasting at Scale," PeerJ Preprints, 2024.
6. 중소벤처기업부, "2025 스마트상점 보급사업 추진계획," 2025.
7. Grand View Research, "Cloud POS Market Size and Growth Analysis 2025-2030," 2025.
8. PCI Security Standards Council, "PCI DSS v4.0 Requirements," 2024.
9. 한국은행, "2025 간편결제 이용 현황 보고서," 2025.
10. 소상공인시장진흥공단, "소상공인 경영개선 사례집," 2025.
