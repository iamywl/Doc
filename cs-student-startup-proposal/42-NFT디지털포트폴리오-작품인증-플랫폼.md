# ArtMint - NFT 디지털 포트폴리오 & 작품 인증 플랫폼

## 2026년 예비창업패키지 사업계획서

---

## 일반현황

| 항목 | 내용 |
|------|------|
| 팀명 | ArtMint (아트민트) |
| 대표자 | 나연우 (컴퓨터공학과 석사) |
| 팀 구성 | 대표 1명, 블록체인 개발 1명, 풀스택 개발 1명, 디자인/UX 1명 |
| 창업 아이템 | 디자인/코드/논문 NFT 발행 및 저작권 보호 포트폴리오 플랫폼 |
| 아이템 분야 | FinTech / 블록체인 / 크리에이터 이코노미 |
| 사업화 단계 | 시제품 개발 단계 |
| 목표 시장 | 대학생/신진 크리에이터 (디자이너, 개발자, 연구자) |
| 신청 금액 | 1억원 |

> **"당신의 창작물에 불변의 소유권을 — NFT로 증명하는 나만의 포트폴리오"**

---

## 창업 아이템 개요

| 항목 | 내용 |
|------|------|
| 아이템명 | ArtMint (아트민트) |
| 핵심 기술 | L2 블록체인 NFT 민팅, 콘텐츠 지문 기술, 포트폴리오 빌더 |
| 목표 고객 | 디자인/개발/연구 분야 대학생 및 신진 크리에이터 |
| 문제 정의 | 대학생 작품 도용 경험 34.2%, 저작권 보호 방법 인지 12.8% (2025 한국저작권위원회) |
| 솔루션 | 원클릭 NFT 발행 → 저작권 타임스탬프 → 포트폴리오 전시 → 라이선스 관리 |
| 비즈니스 모델 | NFT 발행 수수료 + 프리미엄 포트폴리오 구독 + 라이선스 중개 수수료 |
| 시장 규모 | 글로벌 디지털 자산 인증 시장 12억 달러, 국내 크리에이터 포트폴리오 시장 800억원 |
| 경쟁 우위 | 가스비 무료 L2 민팅, 코드/논문 특화, 학교 인증 연동, 한글 포트폴리오 |
| 수익 목표 | 1차년도 1.5억원, 2차년도 7억원, 3차년도 22억원 |

---

## 1. 문제 인식

### 대학생 창작물 보호 현황

| 지표 | 수치 | 출처 |
|------|------|------|
| 대학생 창작물 보유 비율 | 72.6% | 한국콘텐츠진흥원 2025 |
| 작품 도용/표절 경험 | 34.2% | 한국저작권위원회 2025 |
| 저작권 보호 방법 인지 비율 | 12.8% | 한국저작권위원회 2025 |
| 포트폴리오 관리 어려움 | 67.3% | 자체 설문 (n=1,200) |
| 작품 인증 필요성 공감 | 85.4% | 자체 설문 (n=1,200) |
| NFT 활용 의향 | 53.7% | 오픈서베이 2025 |
| 작품 도용 후 법적 대응 비율 | 4.1% | 한국저작권위원회 2025 |
| 디지털 포트폴리오 운영 비율 | 31.5% | 자체 설문 (n=1,200) |

디자인 전공자의 졸업 작품, 개발자의 오픈소스 프로젝트, 연구자의 논문 등 대학생은 재학 중 다양한 창작물을 만든다. 그러나 이를 체계적으로 보호하고 전시할 수단이 부족하다. 기존 포트폴리오 사이트는 저작권 증명 기능이 없고, NFT 발행은 가스비와 기술적 장벽이 높다. ArtMint는 이 격차를 해소한다.

### 사용자 인터뷰 주요 발언

> "졸업 전시회 작품 사진이 해외 사이트에서 버젓이 사용되고 있었어요. 증명할 방법이 없어서 포기했습니다." — 시각디자인과 졸업생 A (25세)

> "깃허브에 올린 코드가 다른 사람 이름으로 포트폴리오에 들어가 있는 걸 봤어요. 커밋 기록만으로는 부족합니다." — 컴퓨터공학과 대학원생 B (27세)

> "NFT 민팅하려면 메타마스크 설정부터 가스비까지... 비전공자인 저에겐 너무 어렵습니다." — 산업디자인과 학생 C (23세)

> "포트폴리오 사이트를 따로 만들고 관리하는 게 너무 번거로워요. 작품과 이력을 한 곳에서 관리하고 싶어요." — 건축학과 학생 D (24세)

### Pain Point 상세 분석

| Pain Point | 현재 대안 | 한계점 | ArtMint 솔루션 |
|------------|-----------|--------|----------------|
| 작품 도용 증명 불가 | 워터마크, 스크린샷 보관 | 법적 효력 미약 | NFT 타임스탬프 (블록체인 증명) |
| 포트폴리오 분산 관리 | 비핸스, 깃허브, 노션 분산 | 통합 관리 불가 | 통합 포트폴리오 빌더 |
| NFT 발행 기술 장벽 | OpenSea 직접 민팅 | 가스비, 지갑 설정 복잡 | 원클릭 가스비 무료 L2 민팅 |
| 라이선스 관리 부재 | 수동 계약 | 추적 불가, 수익화 어려움 | 자동 라이선스 관리/수익 분배 |
| 코드/논문 인증 불가 | 깃허브 커밋, 논문 제출일 | NFT 인증 미지원 | 코드/논문 전용 NFT 민팅 |

<div align="center">
<svg width="800" height="420" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_bg1" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#1a1a2e;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#16213e;stop-opacity:1" />
    </linearGradient>
    <filter id="am_s1">
      <feDropShadow dx="2" dy="2" stdDeviation="3" flood-color="#000" flood-opacity="0.3"/>
    </filter>
  </defs>
  <rect width="800" height="420" rx="15" fill="url(#am_bg1)"/>
  <text x="400" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">대학생 창작물 보호 현황 분석</text>
  <!-- Stats bars -->
  <text x="50" y="80" fill="#aaa" font-size="11">창작물 보유율</text>
  <rect x="200" y="65" width="435" height="25" rx="5" fill="#4A90D9" opacity="0.8"/>
  <text x="625" y="83" fill="#fff" font-size="12" font-weight="bold">72.6%</text>
  <text x="50" y="120" fill="#aaa" font-size="11">작품 도용 경험</text>
  <rect x="200" y="105" width="205" height="25" rx="5" fill="#FF6B6B" opacity="0.8"/>
  <text x="395" y="123" fill="#fff" font-size="12" font-weight="bold">34.2%</text>
  <text x="50" y="160" fill="#aaa" font-size="11">보호 방법 인지</text>
  <rect x="200" y="145" width="77" height="25" rx="5" fill="#FFD93D" opacity="0.8"/>
  <text x="267" y="163" fill="#333" font-size="12" font-weight="bold">12.8%</text>
  <text x="50" y="200" fill="#aaa" font-size="11">관리 어려움</text>
  <rect x="200" y="185" width="404" height="25" rx="5" fill="#9B59B6" opacity="0.8"/>
  <text x="594" y="203" fill="#fff" font-size="12" font-weight="bold">67.3%</text>
  <text x="50" y="240" fill="#aaa" font-size="11">인증 필요성 공감</text>
  <rect x="200" y="225" width="512" height="25" rx="5" fill="#50C878" opacity="0.8"/>
  <text x="702" y="243" fill="#fff" font-size="12" font-weight="bold">85.4%</text>
  <text x="50" y="280" fill="#aaa" font-size="11">NFT 활용 의향</text>
  <rect x="200" y="265" width="322" height="25" rx="5" fill="#4A90D9" opacity="0.8"/>
  <text x="512" y="283" fill="#fff" font-size="12" font-weight="bold">53.7%</text>
  <!-- Insight box -->
  <rect x="50" y="310" width="700" height="90" rx="12" fill="#fff" opacity="0.08"/>
  <text x="400" y="338" text-anchor="middle" fill="#FFD93D" font-size="14" font-weight="bold">핵심 인사이트</text>
  <text x="400" y="362" text-anchor="middle" fill="#ddd" font-size="11">대학생의 85.4%가 작품 인증 필요성에 공감하지만, 실제 보호 방법을 아는 비율은 12.8%에 불과</text>
  <text x="400" y="382" text-anchor="middle" fill="#ddd" font-size="11">= 72.6%의 잠재 고객이 적절한 솔루션을 찾지 못하고 있는 거대한 미충족 수요</text>
</svg>
</div>

---

## 2. 솔루션

### 시스템 아키텍처

<div align="center">
<svg width="820" height="560" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_arch_bg" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#0f0c29;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#302b63;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#24243e;stop-opacity:1" />
    </linearGradient>
    <marker id="am_arr" markerWidth="8" markerHeight="6" refX="8" refY="3" orient="auto">
      <polygon points="0 0, 8 3, 0 6" fill="#FFD93D"/>
    </marker>
    <filter id="am_s2">
      <feDropShadow dx="1" dy="2" stdDeviation="2" flood-color="#000" flood-opacity="0.4"/>
    </filter>
  </defs>
  <rect width="820" height="560" rx="15" fill="url(#am_arch_bg)"/>
  <text x="410" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">ArtMint 시스템 아키텍처</text>
  <!-- Client -->
  <rect x="30" y="55" width="760" height="70" rx="12" fill="#4A90D9" opacity="0.85" filter="url(#am_s2)"/>
  <text x="410" y="75" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">클라이언트 레이어 (Next.js + Three.js)</text>
  <rect x="50" y="85" width="120" height="30" rx="6" fill="#fff" opacity="0.2"/>
  <text x="110" y="105" text-anchor="middle" fill="#fff" font-size="10">NFT 민팅</text>
  <rect x="185" y="85" width="120" height="30" rx="6" fill="#fff" opacity="0.2"/>
  <text x="245" y="105" text-anchor="middle" fill="#fff" font-size="10">포트폴리오 빌더</text>
  <rect x="320" y="85" width="120" height="30" rx="6" fill="#fff" opacity="0.2"/>
  <text x="380" y="105" text-anchor="middle" fill="#fff" font-size="10">라이선스 관리</text>
  <rect x="455" y="85" width="120" height="30" rx="6" fill="#fff" opacity="0.2"/>
  <text x="515" y="105" text-anchor="middle" fill="#fff" font-size="10">갤러리/마켓</text>
  <rect x="590" y="85" width="120" height="30" rx="6" fill="#fff" opacity="0.2"/>
  <text x="650" y="105" text-anchor="middle" fill="#fff" font-size="10">3D 뷰어</text>
  <!-- API Gateway -->
  <rect x="30" y="140" width="760" height="50" rx="12" fill="#50C878" opacity="0.85" filter="url(#am_s2)"/>
  <text x="410" y="170" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">API Gateway (GraphQL - Apollo Federation)</text>
  <!-- Microservices -->
  <rect x="30" y="205" width="760" height="95" rx="12" fill="#9B59B6" opacity="0.75" filter="url(#am_s2)"/>
  <text x="410" y="225" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">마이크로서비스 레이어 (NestJS)</text>
  <rect x="45" y="238" width="130" height="50" rx="8" fill="#fff" opacity="0.15"/>
  <text x="110" y="258" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">Minting Service</text>
  <text x="110" y="275" text-anchor="middle" fill="#ddd" font-size="8">NFT 발행/관리</text>
  <rect x="190" y="238" width="130" height="50" rx="8" fill="#fff" opacity="0.15"/>
  <text x="255" y="258" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">Portfolio Service</text>
  <text x="255" y="275" text-anchor="middle" fill="#ddd" font-size="8">포트폴리오 빌드</text>
  <rect x="335" y="238" width="130" height="50" rx="8" fill="#fff" opacity="0.15"/>
  <text x="400" y="258" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">License Service</text>
  <text x="400" y="275" text-anchor="middle" fill="#ddd" font-size="8">라이선스/수익분배</text>
  <rect x="480" y="238" width="130" height="50" rx="8" fill="#fff" opacity="0.15"/>
  <text x="545" y="258" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">Fingerprint Svc</text>
  <text x="545" y="275" text-anchor="middle" fill="#ddd" font-size="8">콘텐츠 지문/도용감지</text>
  <rect x="625" y="238" width="130" height="50" rx="8" fill="#fff" opacity="0.15"/>
  <text x="690" y="258" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">Search Service</text>
  <text x="690" y="275" text-anchor="middle" fill="#ddd" font-size="8">검색/추천</text>
  <!-- Blockchain Layer -->
  <rect x="30" y="315" width="370" height="70" rx="12" fill="#FF6B6B" opacity="0.75" filter="url(#am_s2)"/>
  <text x="215" y="340" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">블록체인 레이어</text>
  <rect x="50" y="350" width="110" height="25" rx="5" fill="#fff" opacity="0.15"/>
  <text x="105" y="367" text-anchor="middle" fill="#fff" font-size="9">Polygon zkEVM</text>
  <rect x="170" y="350" width="100" height="25" rx="5" fill="#fff" opacity="0.15"/>
  <text x="220" y="367" text-anchor="middle" fill="#fff" font-size="9">Solidity</text>
  <rect x="280" y="350" width="100" height="25" rx="5" fill="#fff" opacity="0.15"/>
  <text x="330" y="367" text-anchor="middle" fill="#fff" font-size="9">ERC-721/1155</text>
  <!-- Storage Layer -->
  <rect x="420" y="315" width="370" height="70" rx="12" fill="#FFD93D" opacity="0.75" filter="url(#am_s2)"/>
  <text x="605" y="340" text-anchor="middle" fill="#333" font-size="13" font-weight="bold">분산 저장 레이어</text>
  <rect x="440" y="350" width="80" height="25" rx="5" fill="#fff" opacity="0.3"/>
  <text x="480" y="367" text-anchor="middle" fill="#333" font-size="9">IPFS</text>
  <rect x="530" y="350" width="80" height="25" rx="5" fill="#fff" opacity="0.3"/>
  <text x="570" y="367" text-anchor="middle" fill="#333" font-size="9">Arweave</text>
  <rect x="620" y="350" width="80" height="25" rx="5" fill="#fff" opacity="0.3"/>
  <text x="660" y="367" text-anchor="middle" fill="#333" font-size="9">AWS S3</text>
  <rect x="710" y="350" width="60" height="25" rx="5" fill="#fff" opacity="0.3"/>
  <text x="740" y="367" text-anchor="middle" fill="#333" font-size="9">CDN</text>
  <!-- Data Layer -->
  <rect x="30" y="400" width="760" height="55" rx="12" fill="#4A90D9" opacity="0.6" filter="url(#am_s2)"/>
  <text x="410" y="420" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">데이터베이스</text>
  <rect x="80" y="430" width="120" height="20" rx="4" fill="#fff" opacity="0.15"/>
  <text x="140" y="445" text-anchor="middle" fill="#fff" font-size="9">PostgreSQL</text>
  <rect x="220" y="430" width="120" height="20" rx="4" fill="#fff" opacity="0.15"/>
  <text x="280" y="445" text-anchor="middle" fill="#fff" font-size="9">Elasticsearch</text>
  <rect x="360" y="430" width="100" height="20" rx="4" fill="#fff" opacity="0.15"/>
  <text x="410" y="445" text-anchor="middle" fill="#fff" font-size="9">Redis</text>
  <rect x="480" y="430" width="120" height="20" rx="4" fill="#fff" opacity="0.15"/>
  <text x="540" y="445" text-anchor="middle" fill="#fff" font-size="9">The Graph</text>
  <!-- External -->
  <rect x="30" y="470" width="760" height="35" rx="8" fill="#fff" opacity="0.08"/>
  <text x="410" y="493" text-anchor="middle" fill="#aaa" font-size="10">외부 연동: Web3Auth (소셜 로그인+지갑) | 학교 인증 API | 콘텐츠 크롤러 | 결제 API</text>
  <!-- Security -->
  <rect x="30" y="515" width="760" height="30" rx="8" fill="#333" opacity="0.5"/>
  <text x="410" y="535" text-anchor="middle" fill="#aaa" font-size="10">보안: 콘텐츠 암호화 | 접근 제어 | DDoS 방어 | 감사 로깅 | ISMS-P 준비</text>
</svg>
</div>

### 핵심 기능

1. **원클릭 NFT 민팅**: 가스비 무료 L2 기반, 이미지/코드/PDF/3D 모델 등 다양한 형식 지원. Polygon zkEVM의 배치 처리를 통해 가스비를 0원으로 구현한다. Web3Auth를 통해 소셜 로그인만으로 지갑이 자동 생성되어 블록체인 지식 없이도 NFT를 발행할 수 있다. 이미지(JPG/PNG/SVG), 코드(GitHub 연동), PDF(논문/보고서), 3D 모델(GLB/GLTF), 영상(MP4) 등 13가지 파일 형식을 지원한다.

2. **콘텐츠 지문(Fingerprint)**: 업로드 시 고유 해시 생성, 웹 크롤링으로 도용 모니터링 및 알림. Perceptual Hashing(pHash)과 SSIM(구조적 유사성 지수)을 결합한 이중 지문 기술로, 이미지 리사이즈/필터 적용 등 변조된 이미지도 95.3%의 정확도로 탐지한다. 주 1회 웹 크롤링(100+ 사이트)을 통해 도용 의심 사례를 자동 감지하고 알림을 보낸다.

3. **포트폴리오 빌더**: 드래그앤드롭 방식의 커스텀 포트폴리오 페이지, 개인 URL 제공. 12종의 레이아웃 템플릿, 커스텀 테마/색상, 반응형 디자인을 제공한다. 작품별 설명, 제작 과정, 사용 기술 등 메타데이터를 구조화하여 입력할 수 있다.

4. **라이선스 관리**: Creative Commons 등 라이선스 설정, 상업적 이용 요청 관리, 수익 분배. CC BY, CC BY-SA 등 6종의 라이선스를 원클릭으로 설정하고, 상업적 이용 요청이 들어오면 인앱 알림과 계약 관리 기능을 제공한다.

5. **온라인 갤러리/마켓**: 대학별/분야별 작품 전시, 기업 채용 담당자 열람, 작품 거래 지원. 대학별 갤러리 페이지를 자동 생성하고, AI 기반 작품 추천 시스템으로 채용 담당자에게 관련 포트폴리오를 매칭한다.

### 서비스 흐름도

<div align="center">
<svg width="800" height="400" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_flow_bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#667eea;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#764ba2;stop-opacity:1" />
    </linearGradient>
    <marker id="am_fa" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#FFD93D"/>
    </marker>
  </defs>
  <rect width="800" height="400" rx="15" fill="url(#am_flow_bg)"/>
  <text x="400" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">ArtMint 서비스 흐름도</text>
  <!-- Row 1: Creator flow -->
  <text x="400" y="65" text-anchor="middle" fill="#FFD93D" font-size="13" font-weight="bold">크리에이터 플로우</text>
  <rect x="30" y="80" width="130" height="60" rx="10" fill="#4A90D9" opacity="0.8"/>
  <text x="95" y="105" text-anchor="middle" fill="#fff" font-size="11" font-weight="bold">1. 회원가입</text>
  <text x="95" y="122" text-anchor="middle" fill="#ddd" font-size="9">소셜 로그인</text>
  <line x1="160" y1="110" x2="185" y2="110" stroke="#FFD93D" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="190" y="80" width="130" height="60" rx="10" fill="#50C878" opacity="0.8"/>
  <text x="255" y="105" text-anchor="middle" fill="#fff" font-size="11" font-weight="bold">2. 작품 업로드</text>
  <text x="255" y="122" text-anchor="middle" fill="#ddd" font-size="9">파일 + 메타데이터</text>
  <line x1="320" y1="110" x2="345" y2="110" stroke="#FFD93D" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="350" y="80" width="130" height="60" rx="10" fill="#FFD93D" opacity="0.8"/>
  <text x="415" y="105" text-anchor="middle" fill="#333" font-size="11" font-weight="bold">3. NFT 민팅</text>
  <text x="415" y="122" text-anchor="middle" fill="#555" font-size="9">원클릭 (가스비 0원)</text>
  <line x1="480" y1="110" x2="505" y2="110" stroke="#FFD93D" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="510" y="80" width="130" height="60" rx="10" fill="#FF6B6B" opacity="0.8"/>
  <text x="575" y="105" text-anchor="middle" fill="#fff" font-size="11" font-weight="bold">4. 포트폴리오</text>
  <text x="575" y="122" text-anchor="middle" fill="#ddd" font-size="9">자동 전시 + URL</text>
  <line x1="640" y1="110" x2="665" y2="110" stroke="#FFD93D" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="670" y="80" width="110" height="60" rx="10" fill="#9B59B6" opacity="0.8"/>
  <text x="725" y="105" text-anchor="middle" fill="#fff" font-size="11" font-weight="bold">5. 수익화</text>
  <text x="725" y="122" text-anchor="middle" fill="#ddd" font-size="9">라이선스/거래</text>
  <!-- Row 2: Protection flow -->
  <text x="400" y="180" text-anchor="middle" fill="#50C878" font-size="13" font-weight="bold">저작권 보호 플로우</text>
  <rect x="80" y="195" width="150" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="155" y="218" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">콘텐츠 지문 생성</text>
  <text x="155" y="237" text-anchor="middle" fill="#ddd" font-size="8">pHash + SSIM 이중 해시</text>
  <line x1="230" y1="222" x2="270" y2="222" stroke="#50C878" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="275" y="195" width="150" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="350" y="218" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">블록체인 기록</text>
  <text x="350" y="237" text-anchor="middle" fill="#ddd" font-size="8">타임스탬프 + 소유권 증명</text>
  <line x1="425" y1="222" x2="465" y2="222" stroke="#50C878" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="470" y="195" width="150" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="545" y="218" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">도용 모니터링</text>
  <text x="545" y="237" text-anchor="middle" fill="#ddd" font-size="8">주 1회 크롤링 (100+ 사이트)</text>
  <!-- Row 3: Business flow -->
  <text x="400" y="290" text-anchor="middle" fill="#FF6B6B" font-size="13" font-weight="bold">기업/채용 연동 플로우</text>
  <rect x="100" y="305" width="180" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="190" y="328" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">기업 채용 담당자 검색</text>
  <text x="190" y="347" text-anchor="middle" fill="#ddd" font-size="8">분야/기술/학교별 필터링</text>
  <line x1="280" y1="332" x2="320" y2="332" stroke="#FF6B6B" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="325" y="305" width="150" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="400" y="328" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">포트폴리오 열람</text>
  <text x="400" y="347" text-anchor="middle" fill="#ddd" font-size="8">NFT 인증 작품 확인</text>
  <line x1="475" y1="332" x2="515" y2="332" stroke="#FF6B6B" stroke-width="2" marker-end="url(#am_fa)"/>
  <rect x="520" y="305" width="150" height="55" rx="10" fill="#fff" opacity="0.15"/>
  <text x="595" y="328" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">크리에이터 컨택</text>
  <text x="595" y="347" text-anchor="middle" fill="#ddd" font-size="8">채용 제안 / 프로젝트 의뢰</text>
</svg>
</div>

---

## 3. 시장 분석

### TAM / SAM / SOM

| 구분 | 규모 | 산출 근거 |
|------|------|-----------|
| TAM (전체 시장) | 1.4조원 | 글로벌 디지털 자산 인증 시장 12억 달러 (Grand View Research 2025) |
| SAM (유효 시장) | 800억원 | 국내 크리에이터 포트폴리오 및 작품 인증 시장 |
| SOM (목표 시장) | 40억원 | 대학생/신진 크리에이터 시장 점유율 5% |

<div align="center">
<svg width="600" height="420" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_tam_bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#1a1a2e;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#16213e;stop-opacity:1" />
    </linearGradient>
  </defs>
  <rect width="600" height="420" rx="15" fill="url(#am_tam_bg)"/>
  <text x="300" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">시장 규모 (TAM / SAM / SOM)</text>
  <circle cx="300" cy="220" r="160" fill="#9B59B6" opacity="0.3" stroke="#9B59B6" stroke-width="2"/>
  <text x="300" y="85" text-anchor="middle" fill="#9B59B6" font-size="14" font-weight="bold">TAM: 1.4조원</text>
  <text x="300" y="102" text-anchor="middle" fill="#c49be0" font-size="10">글로벌 디지털 자산 인증 시장</text>
  <circle cx="300" cy="240" r="100" fill="#4A90D9" opacity="0.4" stroke="#4A90D9" stroke-width="2"/>
  <text x="300" y="165" text-anchor="middle" fill="#4A90D9" font-size="14" font-weight="bold">SAM: 800억원</text>
  <text x="300" y="182" text-anchor="middle" fill="#8ab8e8" font-size="10">국내 크리에이터 포트폴리오/인증</text>
  <circle cx="300" cy="260" r="40" fill="#50C878" opacity="0.6" stroke="#50C878" stroke-width="2"/>
  <text x="300" y="255" text-anchor="middle" fill="#fff" font-size="13" font-weight="bold">SOM</text>
  <text x="300" y="272" text-anchor="middle" fill="#fff" font-size="11">40억원</text>
  <rect x="420" y="350" width="15" height="15" rx="3" fill="#9B59B6" opacity="0.5"/>
  <text x="440" y="363" fill="#aaa" font-size="10">TAM - 글로벌 시장</text>
  <rect x="420" y="372" width="15" height="15" rx="3" fill="#4A90D9" opacity="0.5"/>
  <text x="440" y="385" fill="#aaa" font-size="10">SAM - 국내 시장</text>
  <rect x="420" y="394" width="15" height="15" rx="3" fill="#50C878" opacity="0.7"/>
  <text x="440" y="407" fill="#aaa" font-size="10">SOM - 목표 시장</text>
</svg>
</div>

### 시장 트렌드 및 성장 동인

| 트렌드 | 설명 | 시장 영향 |
|--------|------|-----------|
| 크리에이터 이코노미 성장 | 글로벌 크리에이터 이코노미 시장 1,040억 달러 (2025), 연 22% 성장 | 시장 확대 |
| NFT 실용화 전환 | 투기적 NFT에서 저작권 인증, 자격증, 멤버십 등 실용적 활용으로 전환 | 인식 개선 |
| AI 생성 콘텐츠 증가 | AI로 생성된 콘텐츠와 인간 창작물의 구분/인증 수요 급증 | 신규 수요 |
| 취업 시장 포트폴리오 중요도 | 디자인/개발 직군 채용 시 포트폴리오 필수 요건화 | 사용자 확대 |
| 글로벌 저작권 강화 | EU 디지털 저작권 지침, 한국 저작권법 개정 등 보호 강화 추세 | 규제 수혜 |

### 경쟁 환경 상세 분석

| 항목 | ArtMint | OpenSea | 비핸스 | 노션 포트폴리오 | Rarible |
|------|---------|---------|--------|----------------|---------|
| NFT 저작권 인증 | O | O | X | X | O |
| 가스비 무료 | O (L2) | X | - | - | X |
| 코드/논문 지원 | O | X | X | △ | X |
| 포트폴리오 빌더 | O | X | O | △ | X |
| 도용 모니터링 | O | X | X | X | X |
| 한국어 지원 | O | △ | △ | O | X |
| 대학 연동 | O | X | X | X | X |
| 라이선스 관리 | O | △ | △ | X | △ |
| 채용 연동 | O | X | O | X | X |

---

## 4. 비즈니스 모델

### 요금 체계

| 요금제 | 가격 | 주요 기능 |
|--------|------|-----------|
| 무료 | 0원 | 월 3개 NFT 민팅, 기본 포트폴리오 (1페이지) |
| 크리에이터 | 월 3,900원 | 무제한 민팅, 커스텀 포트폴리오, 도용 모니터링, 개인 URL |
| 프로 | 월 7,900원 | 모든 기능 + 라이선스 관리 + 기업 노출 우선 + 분석 대시보드 |

### 수익 모델 상세

<div align="center">
<svg width="800" height="380" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_bm_bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#0f0c29;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#302b63;stop-opacity:1" />
    </linearGradient>
  </defs>
  <rect width="800" height="380" rx="15" fill="url(#am_bm_bg)"/>
  <text x="400" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">ArtMint 비즈니스 모델 캔버스</text>
  <!-- Revenue streams -->
  <rect x="25" y="55" width="180" height="120" rx="10" fill="#4A90D9" opacity="0.8"/>
  <text x="115" y="78" text-anchor="middle" fill="#fff" font-size="12" font-weight="bold">구독 수익</text>
  <text x="115" y="98" text-anchor="middle" fill="#ddd" font-size="9">크리에이터: 3,900원/월</text>
  <text x="115" y="115" text-anchor="middle" fill="#ddd" font-size="9">프로: 7,900원/월</text>
  <text x="115" y="135" text-anchor="middle" fill="#FFD93D" font-size="11" font-weight="bold">비중: 35%</text>
  <text x="115" y="155" text-anchor="middle" fill="#ddd" font-size="8">ARPU: 4,800원/월</text>
  <rect x="215" y="55" width="180" height="120" rx="10" fill="#50C878" opacity="0.8"/>
  <text x="305" y="78" text-anchor="middle" fill="#fff" font-size="12" font-weight="bold">NFT 거래 수수료</text>
  <text x="305" y="98" text-anchor="middle" fill="#ddd" font-size="9">거래 수수료: 2.5%</text>
  <text x="305" y="115" text-anchor="middle" fill="#ddd" font-size="9">2차 판매 로열티: 5~10%</text>
  <text x="305" y="135" text-anchor="middle" fill="#FFD93D" font-size="11" font-weight="bold">비중: 25%</text>
  <text x="305" y="155" text-anchor="middle" fill="#ddd" font-size="8">평균 거래가: 5만원</text>
  <rect x="405" y="55" width="180" height="120" rx="10" fill="#9B59B6" opacity="0.8"/>
  <text x="495" y="78" text-anchor="middle" fill="#fff" font-size="12" font-weight="bold">기업 채용 연동</text>
  <text x="495" y="98" text-anchor="middle" fill="#ddd" font-size="9">열람 구독: 30만원/월</text>
  <text x="495" y="115" text-anchor="middle" fill="#ddd" font-size="9">채용 매칭 수수료</text>
  <text x="495" y="135" text-anchor="middle" fill="#FFD93D" font-size="11" font-weight="bold">비중: 25%</text>
  <text x="495" y="155" text-anchor="middle" fill="#ddd" font-size="8">기업당: 360만원/년</text>
  <rect x="595" y="55" width="180" height="120" rx="10" fill="#FF6B6B" opacity="0.8"/>
  <text x="685" y="78" text-anchor="middle" fill="#fff" font-size="12" font-weight="bold">기관 라이선스</text>
  <text x="685" y="98" text-anchor="middle" fill="#ddd" font-size="9">대학 기관 라이선스</text>
  <text x="685" y="115" text-anchor="middle" fill="#ddd" font-size="9">라이선스 중개 수수료</text>
  <text x="685" y="135" text-anchor="middle" fill="#FFD93D" font-size="11" font-weight="bold">비중: 15%</text>
  <text x="685" y="155" text-anchor="middle" fill="#ddd" font-size="8">대학당: 500만원/년</text>
  <!-- Unit economics -->
  <rect x="25" y="200" width="750" height="55" rx="10" fill="#fff" opacity="0.08"/>
  <text x="140" y="233" text-anchor="middle" fill="#fff" font-size="11">CAC: 6,000원</text>
  <text x="300" y="233" text-anchor="middle" fill="#fff" font-size="11">LTV: 72,000원</text>
  <text x="460" y="233" text-anchor="middle" fill="#FFD93D" font-size="11" font-weight="bold">LTV/CAC: 12.0x</text>
  <text x="630" y="233" text-anchor="middle" fill="#fff" font-size="11">Payback: 1.3개월</text>
  <!-- Value chain -->
  <rect x="25" y="275" width="750" height="85" rx="10" fill="#fff" opacity="0.05"/>
  <text x="400" y="298" text-anchor="middle" fill="#fff" font-size="12" font-weight="bold">가치 사슬 (Value Chain)</text>
  <text x="120" y="320" text-anchor="middle" fill="#4A90D9" font-size="10">작품 업로드</text>
  <text x="120" y="340" text-anchor="middle" fill="#aaa" font-size="9">→</text>
  <text x="260" y="320" text-anchor="middle" fill="#50C878" font-size="10">NFT 인증</text>
  <text x="260" y="340" text-anchor="middle" fill="#aaa" font-size="9">→</text>
  <text x="400" y="320" text-anchor="middle" fill="#FFD93D" font-size="10">포트폴리오 전시</text>
  <text x="400" y="340" text-anchor="middle" fill="#aaa" font-size="9">→</text>
  <text x="540" y="320" text-anchor="middle" fill="#FF6B6B" font-size="10">기업/채용 연결</text>
  <text x="540" y="340" text-anchor="middle" fill="#aaa" font-size="9">→</text>
  <text x="680" y="320" text-anchor="middle" fill="#9B59B6" font-size="10">라이선스 수익화</text>
</svg>
</div>

---

## 5. 기술 스택

| 분류 | 기술 | 선정 사유 |
|------|------|-----------|
| Frontend | Next.js, TypeScript, Three.js (3D 뷰어) | SSR로 SEO 최적화, 3D 작품 렌더링 |
| Backend | Node.js (NestJS), GraphQL (Apollo) | 유연한 데이터 쿼리, 마이크로서비스 |
| Blockchain | Polygon zkEVM, Solidity, Hardhat | 가스비 최소화, EVM 호환성 |
| 분산저장 | IPFS, Arweave (영구 저장) | 콘텐츠 영구 보존, 탈중앙화 |
| 콘텐츠 지문 | Python, perceptual hashing, SSIM | 도용 탐지 정확도 95%+ |
| Database | PostgreSQL, Elasticsearch | 트랜잭션 무결성, 전문 검색 |
| Infra | AWS EKS, CloudFront, S3 | 글로벌 CDN, 컨테이너 오케스트레이션 |
| 인증 | Web3Auth (소셜 로그인 + 지갑 생성) | 비기술자도 블록체인 접근 가능 |
| CI/CD | GitHub Actions, Vercel | 프론트엔드 자동 배포, PR 프리뷰 |
| 모니터링 | Grafana, Prometheus, Sentry | 블록체인 트랜잭션 모니터링 |

<div align="center">
<svg width="800" height="380" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_tech_bg" x1="0%" y1="0%" x2="0%" y2="100%">
      <stop offset="0%" style="stop-color:#141e30;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#243b55;stop-opacity:1" />
    </linearGradient>
  </defs>
  <rect width="800" height="380" rx="15" fill="url(#am_tech_bg)"/>
  <text x="400" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">ArtMint 기술 스택</text>
  <!-- Layers -->
  <rect x="30" y="55" width="740" height="45" rx="8" fill="#4A90D9" opacity="0.8"/>
  <text x="55" y="82" fill="#fff" font-size="11" font-weight="bold">Frontend</text>
  <text x="170" y="82" fill="#ddd" font-size="10">Next.js | TypeScript | Three.js | TailwindCSS | Framer Motion</text>
  <rect x="30" y="110" width="740" height="45" rx="8" fill="#50C878" opacity="0.8"/>
  <text x="55" y="137" fill="#fff" font-size="11" font-weight="bold">Backend</text>
  <text x="170" y="137" fill="#ddd" font-size="10">NestJS | GraphQL (Apollo Federation) | Bull Queue | Socket.io</text>
  <rect x="30" y="165" width="360" height="45" rx="8" fill="#9B59B6" opacity="0.8"/>
  <text x="55" y="192" fill="#fff" font-size="11" font-weight="bold">Blockchain</text>
  <text x="240" y="192" fill="#ddd" font-size="10">Polygon zkEVM | Solidity | Hardhat | ethers.js</text>
  <rect x="410" y="165" width="360" height="45" rx="8" fill="#FF6B6B" opacity="0.8"/>
  <text x="435" y="192" fill="#fff" font-size="11" font-weight="bold">Storage</text>
  <text x="610" y="192" fill="#ddd" font-size="10">IPFS | Arweave | AWS S3 | CloudFront CDN</text>
  <rect x="30" y="220" width="360" height="45" rx="8" fill="#FFD93D" opacity="0.8"/>
  <text x="55" y="247" fill="#333" font-size="11" font-weight="bold">AI/ML</text>
  <text x="240" y="247" fill="#555" font-size="10">Python | pHash | SSIM | Web Crawler</text>
  <rect x="410" y="220" width="360" height="45" rx="8" fill="#4A90D9" opacity="0.6"/>
  <text x="435" y="247" fill="#fff" font-size="11" font-weight="bold">Database</text>
  <text x="610" y="247" fill="#ddd" font-size="10">PostgreSQL | Elasticsearch | Redis | The Graph</text>
  <rect x="30" y="275" width="740" height="45" rx="8" fill="#333" opacity="0.8"/>
  <text x="55" y="302" fill="#aaa" font-size="11" font-weight="bold">Infra/DevOps</text>
  <text x="200" y="302" fill="#777" font-size="10">AWS EKS | GitHub Actions | Vercel | Terraform | Grafana | Prometheus</text>
  <rect x="30" y="330" width="740" height="35" rx="8" fill="#222" opacity="0.8"/>
  <text x="55" y="352" fill="#aaa" font-size="11" font-weight="bold">Auth/Security</text>
  <text x="250" y="352" fill="#777" font-size="10">Web3Auth | JWT | RBAC | Content Encryption | DDoS Protection</text>
</svg>
</div>

---

## 6. 경쟁사 분석

| 항목 | ArtMint | OpenSea | 비핸스 | 노션 포트폴리오 |
|------|---------|---------|--------|----------------|
| NFT 저작권 인증 | O | O | X | X |
| 가스비 무료 | O (L2) | X | - | - |
| 코드/논문 지원 | O | X | X | △ |
| 포트폴리오 빌더 | O | X | O | △ |
| 도용 모니터링 | O | X | X | X |
| 한국어 지원 | O | △ | △ | O |
| 대학 연동 | O | X | X | X |

### ArtMint의 경쟁 우위 (Moat)

1. **가스비 제로 민팅**: Polygon zkEVM 배치 처리를 통해 완전 무료 NFT 민팅을 제공한다. OpenSea 등 기존 플랫폼의 가스비 부담(평균 $5~50)을 완전히 제거하여 대학생 접근성을 극대화한다.

2. **코드/논문 전용 NFT**: 소스 코드(GitHub 연동), 학술 논문(PDF 메타데이터 추출), 연구 데이터셋 등 기존 NFT 플랫폼이 지원하지 않는 포맷을 전문적으로 처리한다.

3. **이중 콘텐츠 지문**: pHash + SSIM 결합으로 95.3%의 도용 탐지 정확도를 달성한다. 이미지 리사이즈, 필터 적용, 부분 크롭 등 변조에도 강건하다.

4. **대학 생태계 연동**: 대학별 갤러리, 학과별 전시, 졸업 작품전 디지털화 등 대학 특화 기능으로 기관 고객 확보와 학생 유입을 동시에 달성한다.

---

## 7. 로드맵

<div align="center">
<svg width="820" height="380" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="am_road_bg" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#1a1a2e;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#16213e;stop-opacity:1" />
    </linearGradient>
  </defs>
  <rect width="820" height="380" rx="15" fill="url(#am_road_bg)"/>
  <text x="410" y="35" text-anchor="middle" fill="#fff" font-size="18" font-weight="bold">ArtMint 성장 로드맵</text>
  <line x1="60" y1="90" x2="760" y2="90" stroke="#555" stroke-width="3"/>
  <!-- Milestones -->
  <circle cx="120" cy="90" r="14" fill="#4A90D9" stroke="#fff" stroke-width="2"/>
  <text x="120" y="95" text-anchor="middle" fill="#fff" font-size="8" font-weight="bold">Q3</text>
  <text x="120" y="72" text-anchor="middle" fill="#4A90D9" font-size="9" font-weight="bold">2026</text>
  <rect x="55" y="115" width="130" height="80" rx="8" fill="#4A90D9" opacity="0.3"/>
  <text x="120" y="133" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">MVP 출시</text>
  <text x="120" y="150" text-anchor="middle" fill="#ddd" font-size="8">이미지 NFT 민팅</text>
  <text x="120" y="165" text-anchor="middle" fill="#ddd" font-size="8">기본 포트폴리오</text>
  <text x="120" y="180" text-anchor="middle" fill="#ddd" font-size="8">디자인학과 3곳</text>
  <text x="120" y="195" text-anchor="middle" fill="#FFD93D" font-size="8">사용자 500명</text>
  <circle cx="240" cy="90" r="14" fill="#50C878" stroke="#fff" stroke-width="2"/>
  <text x="240" y="95" text-anchor="middle" fill="#fff" font-size="8" font-weight="bold">Q4</text>
  <rect x="175" y="115" width="130" height="80" rx="8" fill="#50C878" opacity="0.3"/>
  <text x="240" y="133" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">코드/PDF 지원</text>
  <text x="240" y="150" text-anchor="middle" fill="#ddd" font-size="8">포트폴리오 빌더 고도화</text>
  <text x="240" y="165" text-anchor="middle" fill="#ddd" font-size="8">GitHub 연동</text>
  <text x="240" y="180" text-anchor="middle" fill="#ddd" font-size="8">크리에이터 요금제</text>
  <text x="240" y="195" text-anchor="middle" fill="#FFD93D" font-size="8">사용자 3,000명</text>
  <circle cx="370" cy="90" r="14" fill="#FFD93D" stroke="#fff" stroke-width="2"/>
  <text x="370" y="95" text-anchor="middle" fill="#333" font-size="8" font-weight="bold">Q1</text>
  <text x="370" y="72" text-anchor="middle" fill="#FFD93D" font-size="9" font-weight="bold">2027</text>
  <rect x="305" y="115" width="130" height="80" rx="8" fill="#FFD93D" opacity="0.2"/>
  <text x="370" y="133" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">도용 모니터링</text>
  <text x="370" y="150" text-anchor="middle" fill="#ddd" font-size="8">콘텐츠 지문 엔진</text>
  <text x="370" y="165" text-anchor="middle" fill="#ddd" font-size="8">라이선스 관리</text>
  <text x="370" y="180" text-anchor="middle" fill="#ddd" font-size="8">프로 요금제</text>
  <text x="370" y="195" text-anchor="middle" fill="#FFD93D" font-size="8">사용자 10,000명</text>
  <circle cx="500" cy="90" r="14" fill="#FF6B6B" stroke="#fff" stroke-width="2"/>
  <text x="500" y="95" text-anchor="middle" fill="#fff" font-size="8" font-weight="bold">Q2</text>
  <rect x="435" y="115" width="130" height="80" rx="8" fill="#FF6B6B" opacity="0.3"/>
  <text x="500" y="133" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">채용 연동</text>
  <text x="500" y="150" text-anchor="middle" fill="#ddd" font-size="8">온라인 갤러리 오픈</text>
  <text x="500" y="165" text-anchor="middle" fill="#ddd" font-size="8">기업 채용 연동</text>
  <text x="500" y="180" text-anchor="middle" fill="#ddd" font-size="8">대학 10곳 제휴</text>
  <text x="500" y="195" text-anchor="middle" fill="#FFD93D" font-size="8">사용자 25,000명</text>
  <circle cx="630" cy="90" r="14" fill="#9B59B6" stroke="#fff" stroke-width="2"/>
  <text x="630" y="95" text-anchor="middle" fill="#fff" font-size="8" font-weight="bold">Q3-4</text>
  <rect x="565" y="115" width="130" height="80" rx="8" fill="#9B59B6" opacity="0.3"/>
  <text x="630" y="133" text-anchor="middle" fill="#fff" font-size="10" font-weight="bold">글로벌 확장</text>
  <text x="630" y="150" text-anchor="middle" fill="#ddd" font-size="8">3D/영상 작품 지원</text>
  <text x="630" y="165" text-anchor="middle" fill="#ddd" font-size="8">해외 시장 진출</text>
  <text x="630" y="180" text-anchor="middle" fill="#ddd" font-size="8">시리즈 A 투자</text>
  <text x="630" y="195" text-anchor="middle" fill="#FFD93D" font-size="8">사용자 60,000명</text>
  <!-- KPI -->
  <rect x="60" y="230" width="700" height="60" rx="10" fill="#fff" opacity="0.08"/>
  <text x="160" y="258" text-anchor="middle" fill="#4A90D9" font-size="10">누적 NFT: 50K</text>
  <text x="160" y="278" text-anchor="middle" fill="#aaa" font-size="8">1차년도</text>
  <text x="330" y="258" text-anchor="middle" fill="#50C878" font-size="10">누적 NFT: 300K</text>
  <text x="330" y="278" text-anchor="middle" fill="#aaa" font-size="8">2차년도</text>
  <text x="500" y="258" text-anchor="middle" fill="#FFD93D" font-size="10">누적 NFT: 1.2M</text>
  <text x="500" y="278" text-anchor="middle" fill="#aaa" font-size="8">3차년도</text>
  <text x="660" y="258" text-anchor="middle" fill="#FF6B6B" font-size="10">ARR: 22억원</text>
  <text x="660" y="278" text-anchor="middle" fill="#aaa" font-size="8">3차년도 매출</text>
  <!-- Funding -->
  <rect x="60" y="310" width="700" height="45" rx="8" fill="#fff" opacity="0.06"/>
  <text x="200" y="338" text-anchor="middle" fill="#aaa" font-size="10">Pre-Seed: 예비창업패키지 1억원</text>
  <text x="430" y="338" text-anchor="middle" fill="#FFD93D" font-size="10">Seed: 3~5억원 (2027 Q1)</text>
  <text x="640" y="338" text-anchor="middle" fill="#50C878" font-size="10">Series A: 15억원 (2027 Q4)</text>
</svg>
</div>

| 분기 | 마일스톤 | 핵심 KPI |
|------|----------|----------|
| 2026 Q3 | MVP 출시 (이미지 NFT 민팅 + 기본 포트폴리오), 디자인 학과 3곳 파일럿 | 사용자 500명, NFT 2,000개 |
| 2026 Q4 | 코드/PDF 민팅 지원, 포트폴리오 빌더 고도화, 사용자 3,000명 | 유료 전환율 8%, NFT 15,000개 |
| 2027 Q1 | 콘텐츠 지문 및 도용 모니터링, 라이선스 관리, 사용자 10,000명 | 도용 탐지율 95%, 제휴 대학 5곳 |
| 2027 Q2 | 온라인 갤러리 오픈, 기업 채용 연동, 프로 요금제, 사용자 25,000명 | 기업 고객 20곳, 채용 매칭 100건 |
| 2027 Q3 | 대학 기관 라이선스 판매, 3D/영상 작품 지원 | 기관 고객 15곳, 지원 포맷 15종 |
| 2027 Q4 | 해외 크리에이터 시장 진출, 시리즈 A 투자 유치 | MAU 60,000명, ARR 22억원 |

---

## 8. 팀 구성

| 역할 | 이름 | 전공/경력 | 담당 업무 | 핵심 역량 |
|------|------|-----------|-----------|-----------|
| 대표/PM | 나연우 | 컴퓨터공학 석사, NFT 프로젝트 경험 | 사업 전략, 블록체인 설계 | 블록체인 DApp 개발 3건, NFT 표준 연구 |
| 블록체인 개발 | 고지원 | 컴퓨터공학 석사, 스마트 컨트랙트 전문 | NFT 컨트랙트, L2 인프라 | Solidity 고급, zk-Rollup 연구, 해커톤 수상 |
| 풀스택 개발 | 안서현 | 소프트웨어공학 4학년, 웹 서비스 경험 | 플랫폼 개발, API 설계 | NestJS/Next.js 숙련, GraphQL, AWS 운영 |
| 디자인/UX | 류지안 | 시각디자인 4학년, 포트폴리오 사이트 운영 | UI/UX 디자인, 사용자 리서치 | Figma 전문가, 사용자 인터뷰 경험, 디자인 시스템 |

### 채용 계획

| 시기 | 포지션 | 인원 | 요구 역량 |
|------|--------|------|-----------|
| 2026 Q4 | ML 엔지니어 | 1명 | 이미지 분석, 콘텐츠 지문 알고리즘 |
| 2027 Q1 | 그로스 마케터 | 1명 | 대학생/크리에이터 커뮤니티 마케팅 |
| 2027 Q2 | 사업개발 매니저 | 1명 | 대학/기업 제휴, B2B 영업 |
| 2027 Q3 | 프론트엔드 개발자 | 1명 | Three.js 3D 렌더링, 웹 성능 최적화 |

---

## 9. 재무 계획

| 항목 | 1차년도 | 2차년도 | 3차년도 |
|------|---------|---------|---------|
| 매출 | 1.5억원 | 7억원 | 22억원 |
| 영업비용 | 4억원 | 6.5억원 | 13억원 |
| 인건비 | 2.4억원 | 4억원 | 7.2억원 |
| 마케팅비 | 0.8억원 | 1.5억원 | 3억원 |
| 서버/블록체인 | 0.6억원 | 1억원 | 2억원 |
| 기타 운영비 | 0.2억원 | 0.3억원 | 0.8억원 |
| 영업이익 | -2.5억원 | 0.5억원 | 9억원 |
| 영업이익률 | -167% | 7.1% | 40.9% |
| 누적 사용자 | 15,000명 | 60,000명 | 200,000명 |
| 누적 NFT 발행 | 50,000개 | 300,000개 | 1,200,000개 |

### 매출 구성 상세 (3차년도 기준)

| 수익원 | 금액 | 비중 |
|--------|------|------|
| 구독 수익 (크리에이터+프로) | 7.7억원 | 35% |
| NFT 거래 수수료 | 5.5억원 | 25% |
| 기업 채용 연동 | 5.5억원 | 25% |
| 기관 라이선스 + 중개 수수료 | 3.3억원 | 15% |

### 손익분기점 분석

- **월 손익분기점**: 유료 사용자 4,000명 + NFT 거래 월 1,000건
- **예상 도달 시점**: 2027년 Q2 (서비스 출시 후 약 9개월)
- **누적 적자 예상**: 약 3억원

---

## 10. 리스크 관리

| 리스크 | 발생 가능성 | 영향도 | 대응 전략 |
|--------|------------|--------|-----------|
| 블록체인 규제 강화 | 중간 | 높음 | NFT를 저작권 인증 도구로 포지셔닝, 투기적 요소 최소화 |
| L2 네트워크 장애 | 낮음 | 높음 | 멀티 체인 지원 준비 (Arbitrum 백업), 오프체인 캐시 |
| 콘텐츠 저작권 분쟁 | 중간 | 중간 | 신고 시스템, 분쟁 중재 프로세스, 법률 자문 |
| 대형 플랫폼 진입 | 높음 | 높음 | 대학생/코드/논문 특화로 차별화, 빠른 시장 선점 |
| 사용자 블록체인 인식 부족 | 중간 | 중간 | Web3Auth로 복잡성 은닉, 교육 콘텐츠 제공 |
| 개인정보 보호 | 중간 | 높음 | 작품 메타데이터만 온체인, 개인정보는 오프체인 관리 |

---

## 11. 사회적 가치

| 가치 | 설명 | 측정 지표 |
|------|------|-----------|
| 창작자 권리 보호 | NFT 인증으로 대학생 창작물 저작권 보호 | 도용 탐지 건수, 권리 회복 건수 |
| 디지털 포트폴리오 민주화 | 무료/저가 포트폴리오 빌더로 진입장벽 제거 | 무료 사용자 수, 포트폴리오 생성 수 |
| 신진 크리에이터 수익화 | 라이선스 관리/거래 플랫폼으로 창작 수익 창출 | 크리에이터 수익 총액, 거래 건수 |
| 취업 역량 가시화 | 인증된 포트폴리오로 취업 기회 확대 | 채용 매칭 건수, 채용 전환율 |

---

## 12. 참고문헌

1. 한국저작권위원회, "2025 대학생 저작권 인식 및 피해 현황 조사", 2025
2. 한국콘텐츠진흥원, "2025 디지털 콘텐츠 산업 현황 보고서", 2025
3. Grand View Research, "Digital Asset Management Market Report 2025", 2025
4. 오픈서베이, "2025 MZ세대 NFT 및 블록체인 인식 조사", 2025
5. Ethereum Foundation, "ERC-721 & ERC-1155 Token Standards", 2024
6. 한국블록체인산업진흥협회, "2025 블록체인 산업 동향 보고서", 2025
7. 문화체육관광부, "2025 콘텐츠 산업 실태 조사", 2025
8. Goldman Sachs, "The Creator Economy: Market Size and Growth Projections", 2025
9. 한국디자인진흥원, "2025 디자인 산업 통계 및 포트폴리오 트렌드", 2025
10. World Intellectual Property Organization, "IP and NFTs: Legal Framework Analysis", 2025
