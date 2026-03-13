# 로컬 LLM 인프라 — 공통 기술 사양 및 구동 방식

> **이 문서는 본 프로젝트의 모든 창업 아이템(01~10)에서 공통으로 사용하는 로컬 LLM 인프라의 기술 사양, 구동 방식, 비용 분석을 상세히 기술한 공통 참조 문서입니다.**

---

## 1. 왜 로컬 LLM인가?

### 1.1 클라우드 API의 한계

| 구분 | 문제점 | 영향받는 서비스 예시 |
|------|--------|---------------------|
| **개인정보 유출 위험** | 사용자의 정신건강 상담 내용, 금융 정보, 건강 데이터, 장애 정보 등 민감 데이터가 외부 서버로 전송됨 | 마음이음(정신건강), 시니어톡(건강), 배리어프리고(장애정보) |
| **API 호출 비용 누적** | GPT-4o 기준 입력 $2.5/1M tokens, 출력 $10/1M tokens. MAU 10만 기준 월 500~2,000만원 | 전 서비스 |
| **네트워크 의존성** | 인터넷 연결 필수, 평균 지연시간 200~800ms, 오프라인 환경 불가 | 시니어톡(농촌 지역), 동네알바(현장) |
| **서비스 종속성** | OpenAI API 정책 변경, 가격 인상, 서비스 중단 리스크. 2024년 GPT-4 Turbo → GPT-4o 전환 시 API 변경 사례 | 전 서비스 |
| **한국어 특화 제약** | 클라우드 모델은 한국어 fine-tuning 불가. 도메인 특화 용어(상담 용어, 법률 용어, 요리 용어 등) 학습 제한 | 마음이음(상담용어), 그린바이트(식재료) |

#### 실제 비용 시뮬레이션: GPT-4o API 사용 시

```
[마음이음 AI 멘탈케어 — 월간 API 비용 추정]

- MAU: 100,000명
- 1인당 월 평균 대화: 20회
- 1회 대화 평균 토큰: 입력 500 + 출력 800 = 1,300 tokens
- 월간 총 토큰: 100,000 × 20 × 1,300 = 2,600,000,000 tokens (26억)

비용 계산:
- 입력: 100,000 × 20 × 500 / 1,000,000 × $2.50 = $2,500
- 출력: 100,000 × 20 × 800 / 1,000,000 × $10.00 = $16,000
- 월 합계: $18,500 ≈ 약 2,400만원/월
- 연간: 약 2.88억원

→ 예비창업패키지 사업비(최대 1억원)로는 4개월도 운영 불가
```

### 1.2 로컬 LLM의 장점

**1) 데이터 주권 확보**

모든 사용자 데이터(상담 기록, 건강 정보, 금융 데이터, 장애 정보 등)가 물리적으로 자체 서버 내부에서만 처리됩니다. 네트워크를 통해 외부로 전송되는 데이터가 전무하므로, 개인정보보호법 제17조(개인정보의 제공) 위반 소지가 원천 차단됩니다.

```
[클라우드 API 방식]
사용자 → 인터넷 → OpenAI 서버(미국) → 인터넷 → 사용자
         ↑ 데이터 유출 위험 구간 ↑

[로컬 LLM 방식]
사용자 → 내부 네트워크 → 자체 서버 → 내부 네트워크 → 사용자
         ↑ 외부 노출 없음 ↑
```

**2) 비용 효율**

초기 하드웨어 투자(약 800~1,200만원) 이후 추론 비용이 사실상 0원입니다. 전기료(월 약 5만원)만 발생하며, 사용량 증가에 따른 추가 비용이 없습니다.

**3) 커스터마이징**

한국어 도메인 특화 LoRA fine-tuning을 자유롭게 수행할 수 있습니다:
- 마음이음: 심리상담 대화 코퍼스로 공감 능력 향상
- 그린바이트: 한국 식재료·레시피 데이터로 음식 인식 정확도 향상
- 시니어톡: 경상도/전라도 등 방언 이해 능력 추가
- 배리어프리고: 장애 유형별 접근성 가이드 전문 지식 학습

**4) 독립성**

외부 API 장애, 정책 변경, 가격 인상과 무관하게 24/7 서비스 운영이 가능합니다. 망분리 환경(공공기관, 병원 등)에서도 배포할 수 있어 B2G/B2B 확장에 유리합니다.

---

## 2. 하드웨어 사양 — 128GB 메모리 서버 구성

### 2.1 권장 서버 사양

#### Option A: AMD 기반 워크스테이션 (권장)

| 구성요소 | 사양 | 역할 | 예상 가격 |
|----------|------|------|-----------|
| **CPU** | AMD EPYC 7313P 16코어/32스레드 3.0GHz | 데이터 전처리, CPU 추론 offload, 서버 운영 | 약 120만원 |
| **메인보드** | Supermicro H12SSL-i (SP3 소켓) | EPYC 호환, PCIe 4.0 x128 레인 | 약 80만원 |
| **RAM** | DDR4 ECC RDIMM 128GB (32GB × 4) | 모델 offload, KV-cache, 멀티모델 동시 로드 | 약 100만원 |
| **GPU** | NVIDIA RTX 4090 24GB GDDR6X | LLM 추론 엔진, 핵심 연산 처리 | 약 280만원 |
| **Storage** | Samsung 990 PRO 2TB NVMe SSD | 모델 파일, 데이터베이스, 로그 | 약 25만원 |
| **PSU** | Seasonic PRIME TX-1000W 80+ Titanium | RTX 4090 전력 요구(450W TDP) 대응 | 약 35만원 |
| **케이스** | Fractal Design Define 7 XL | 풀타워, 충분한 공기 흐름 | 약 25만원 |
| **쿨링** | Noctua NH-D15 + 케이스팬 4개 | 24/7 서버 안정성 확보 | 약 15만원 |
| **OS** | Ubuntu 22.04.4 LTS Server | 무료, CUDA 호환, 장기 지원(2027년까지) | 0원 |
| **UPS** | APC Smart-UPS 1500VA | 정전 시 안전 종료 | 약 40만원 |
| **합계** | | | **약 720만원** |

#### Option B: Intel 기반 워크스테이션

| 구성요소 | 사양 | 예상 가격 |
|----------|------|-----------|
| **CPU** | Intel Xeon w5-2465X 16코어/32스레드 3.1GHz | 약 150만원 |
| **메인보드** | ASUS Pro WS W790E-SAGE SE | 약 120만원 |
| **RAM** | DDR5 ECC RDIMM 128GB (32GB × 4) | 약 140만원 |
| **GPU** | NVIDIA RTX 4090 24GB | 약 280만원 |
| **기타** | Storage, PSU, 케이스, 쿨링, UPS | 약 140만원 |
| **합계** | | **약 830만원** |

#### Option C: 대형 모델 전용 (RTX A6000)

RTX A6000(48GB VRAM)을 사용하면 70B 모델을 더 많은 레이어 GPU에 적재할 수 있어 추론 속도가 약 2배 향상됩니다.

| 구성요소 | 변경점 | 가격 차이 |
|----------|--------|-----------|
| **GPU** | RTX A6000 48GB (RTX 4090 대신) | +약 350만원 |
| **합계** | | **약 1,070~1,180만원** |

> **예비창업패키지 1단계 사업비(최대 1억원)에서 서버 구축비로 800~1,200만원은 충분히 충당 가능합니다.**

### 2.2 128GB RAM의 역할

128GB RAM은 단순히 "많은 메모리"가 아니라, 로컬 LLM 운영의 **핵심 인프라**입니다.

#### 역할 1: GPU VRAM Offload

대형 LLM(70B 파라미터)은 양자화(Q4_K_M) 후에도 약 40GB의 메모리가 필요합니다. RTX 4090의 VRAM은 24GB이므로, 나머지 ~16GB는 시스템 RAM으로 offload해야 합니다.

```
[Llama 3.1 70B Q4_K_M 모델 레이어 분배]

GPU VRAM (24GB):          ████████████████████████  Layer 0~34 (35개 레이어)
                          ↑ 고속 추론 (GPU 메모리 대역폭: 1,008 GB/s)

System RAM (~40GB):       ████████████████████████████████████████  Layer 35~79 (45개 레이어)
                          ↑ 저속이지만 동작 (DDR4: 51.2 GB/s, DDR5: 76.8 GB/s)

→ llama.cpp 실행 시: --n-gpu-layers 35 옵션으로 GPU 레이어 수 지정
→ 나머지 레이어는 자동으로 RAM에서 처리
→ 128GB RAM이 없으면 70B 모델 구동 자체가 불가능
```

#### 역할 2: 멀티모델 동시 로드

본 프로젝트의 서비스들은 LLM 외에도 여러 AI 모델을 동시에 운영해야 합니다:

```
[128GB RAM 메모리 맵 — 전체 서비스 운영 시]

┌─────────────────────────────────────────────────────────────┐
│                    128GB System RAM                         │
├─────────────────────────────────────────────────────────────┤
│ Llama 3.1 70B Q4 offload layers     │ ~40GB               │
│ Whisper large-v3 버퍼               │ ~2GB                │
│ Embedding 모델 (BGE-M3)             │ ~1GB                │
│ VITS TTS 모델 캐시                  │ ~1GB                │
│ PostgreSQL + pgvector               │ ~8GB                │
│ Redis 캐시                          │ ~4GB                │
│ Node.js 서버 (V8 힙)                │ ~4GB                │
│ KV-cache (긴 대화 컨텍스트)          │ ~8GB                │
│ OS + 시스템 프로세스                 │ ~8GB                │
│ 배치 처리 / 전처리 버퍼             │ ~10GB               │
│ ─────────────────────────────────── │ ───────             │
│ 여유 (안전 마진 + 스파이크 대응)      │ ~42GB               │
└─────────────────────────────────────────────────────────────┘
→ 합계: 128GB | 64GB RAM으로는 70B 모델 운영이 물리적으로 불가능
```

#### 역할 3: KV-Cache 저장

LLM 추론 시 이전 토큰의 Key-Value 벡터를 캐싱하면 반복 계산을 피할 수 있습니다. 긴 대화(수천 토큰)에서는 KV-cache가 수 GB까지 증가할 수 있으며, 이를 RAM에 저장합니다.

```python
# KV-cache 메모리 계산 공식
kv_cache_size = 2 × num_layers × num_heads × head_dim × seq_len × batch_size × dtype_size

# Llama 3.1 8B, 4096 토큰 컨텍스트, batch_size=1 기준
# 2 × 32 × 32 × 128 × 4096 × 1 × 2 bytes (FP16) = ~2.1GB

# Llama 3.1 70B, 4096 토큰 컨텍스트, batch_size=4 기준
# 2 × 80 × 64 × 128 × 4096 × 4 × 2 bytes = ~42GB
# → 128GB RAM이 있어야 여러 사용자의 KV-cache를 동시에 유지 가능
```

#### 모델 양자화 전후 비교 테이블

| 모델 | 정밀도 | 모델 크기 | VRAM 사용 | RAM 필요 | 추론 속도 (tok/s) | 품질 (MMLU) |
|------|--------|-----------|-----------|----------|-------------------|-------------|
| Llama 3.1 70B | FP16 | ~140GB | 140GB (불가) | — | — | 86.0% |
| Llama 3.1 70B | Q8_0 | ~70GB | 24GB + 46GB RAM | 70GB | ~8 tok/s | 85.5% |
| **Llama 3.1 70B** | **Q4_K_M** | **~40GB** | **24GB + 16GB RAM** | **40GB** | **~15 tok/s** | **84.8%** |
| Llama 3.1 8B | FP16 | ~16GB | 16GB (GPU only) | 최소 | ~45 tok/s | 73.0% |
| **Llama 3.1 8B** | **Q4_K_M** | **~5GB** | **5GB (GPU only)** | **최소** | **~80 tok/s** | **72.1%** |
| Mistral 7B | Q4_K_M | ~4.5GB | 4.5GB | 최소 | ~85 tok/s | 64.2% |
| Gemma 2 9B | Q4_K_M | ~6GB | 6GB | 최소 | ~70 tok/s | 71.3% |
| Qwen 2.5 7B | Q4_K_M | ~5GB | 5GB | 최소 | ~75 tok/s | 74.2% |

> **핵심**: Q4_K_M 양자화는 FP16 대비 품질 손실이 1~2%에 불과하면서 모델 크기를 3.5배 줄입니다. 128GB RAM과 결합하면 70B급 대형 모델도 소비자용 GPU(RTX 4090)에서 실용적 속도로 구동할 수 있습니다.

### 2.3 대안 구성 (예산 절감)

#### Option D: Apple Mac Studio M2 Ultra (128GB 통합메모리)

| 항목 | 사양 | 가격 |
|------|------|------|
| 모델 | Mac Studio M2 Ultra, 128GB 통합메모리 | 약 600만원 |
| CPU | M2 Ultra 24코어 | 포함 |
| GPU | 76코어 Apple GPU | 포함 |
| 메모리 | 128GB 통합메모리 (CPU+GPU 공유) | 포함 |
| 메모리 대역폭 | 800GB/s | — |
| Storage | 2TB SSD | 포함 |

**장점:**
- CPU와 GPU가 동일한 128GB 메모리를 공유 → offload 없이 70B 모델 전체 로드 가능
- 메모리 대역폭 800GB/s (DDR4의 15배) → offload 시에도 빠른 추론
- 소음/발열/전력 소비 최소 (맥미니급 크기)
- macOS에서 Ollama 네이티브 지원

**단점:**
- CUDA 미지원 → vLLM, 일부 PyTorch 최적화 사용 불가
- GPU 메모리와 시스템 메모리가 통합이므로 대형 모델 로드 시 시스템 메모리 부족 가능
- 확장성 제한 (GPU 추가 불가)

```bash
# Mac Studio에서 Ollama 설치 및 70B 모델 실행
brew install ollama
ollama serve &
ollama run llama3.1:70b

# Metal 가속으로 ~20 tok/s 달성 가능 (통합메모리 덕분)
```

#### Option E: 클라우드 GPU 인스턴스

| 플랫폼 | GPU | VRAM | 시간당 비용 | 월 비용 (24/7) |
|---------|-----|------|-------------|----------------|
| RunPod | RTX 4090 | 24GB | $0.44 | 약 42만원 |
| RunPod | A100 80GB | 80GB | $1.64 | 약 160만원 |
| Vast.ai | RTX 4090 | 24GB | $0.30 | 약 29만원 |
| Vast.ai | RTX 3090 | 24GB | $0.20 | 약 19만원 |
| Lambda | A100 80GB | 80GB | $1.29 | 약 125만원 |

**활용 시나리오:**
- 초기 MVP 개발 단계: Vast.ai RTX 4090 (월 ~30만원)
- fine-tuning 전용: RunPod A100 (학습 시간만 사용, 시간당 과금)
- 서비스 운영: 자체 서버 구축이 6개월 이상 사용 시 경제적

#### Option F: Google Colab Pro+

| 항목 | 사양 |
|------|------|
| 가격 | 월 약 7만원 ($49.99) |
| GPU | T4/A100 (배정에 따라 다름) |
| RAM | 최대 51GB |
| 용도 | 프로토타입 개발, fine-tuning 실험 |
| 제한 | 세션 24시간 제한, 안정적 서빙 불가 |

---

## 3. 소프트웨어 스택 및 구동 방식

### 3.1 LLM 서빙 프레임워크

#### 3.1.1 Ollama — 간편한 로컬 LLM 서빙

Ollama는 로컬 LLM을 Docker처럼 간편하게 관리할 수 있는 프레임워크입니다. 모델 다운로드, 실행, API 서빙을 단일 명령으로 처리합니다.

**설치 및 기본 사용:**

```bash
# Linux 설치 (한 줄)
curl -fsSL https://ollama.com/install.sh | sh

# GPU 드라이버 확인
nvidia-smi

# 모델 다운로드 및 실행
ollama pull llama3.1:8b          # 8B 모델 다운로드 (~4.7GB)
ollama pull llama3.1:70b-q4_K_M  # 70B 양자화 모델 (~40GB)
ollama run llama3.1:8b           # 대화형 실행

# 백그라운드 서빙 (systemd)
sudo systemctl enable ollama
sudo systemctl start ollama

# REST API 호출
curl http://localhost:11434/api/generate \
  -d '{
    "model": "llama3.1:8b",
    "prompt": "우울한 기분이 2주 이상 지속되고 있어요.",
    "stream": true
  }'

# 채팅 API (대화 컨텍스트 유지)
curl http://localhost:11434/api/chat \
  -d '{
    "model": "llama3.1:8b",
    "messages": [
      {"role": "system", "content": "당신은 공감 능력이 뛰어난 심리상담 AI입니다."},
      {"role": "user", "content": "요즘 잠을 잘 못 자고 있어요."}
    ],
    "stream": true
  }'
```

**Modelfile로 커스텀 모델 생성:**

```dockerfile
# Modelfile.mentalcare
FROM llama3.1:8b

# 시스템 프롬프트 설정
SYSTEM """
당신은 '마음이음' 서비스의 AI 심리상담 도우미입니다.
- 항상 공감적이고 따뜻한 톤으로 대화합니다.
- 전문 상담사가 아님을 명시하고, 심각한 경우 전문 상담 연계를 안내합니다.
- 한국어로만 대화합니다.
- CBT(인지행동치료) 기법을 활용한 질문을 합니다.
"""

# 파라미터 튜닝
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER top_k 40
PARAMETER repeat_penalty 1.1
PARAMETER num_ctx 4096

# LoRA 어댑터 적용 (fine-tuning 후)
# ADAPTER ./adapters/mentalcare-lora.gguf
```

```bash
# 커스텀 모델 빌드 및 실행
ollama create mentalcare -f Modelfile.mentalcare
ollama run mentalcare
```

**Node.js에서 Ollama 연동:**

```javascript
// ollama-client.js
import { Ollama } from 'ollama';

const ollama = new Ollama({ host: 'http://localhost:11434' });

// 스트리밍 응답
async function chat(userMessage, conversationHistory) {
  const response = await ollama.chat({
    model: 'mentalcare',  // 커스텀 모델
    messages: [
      ...conversationHistory,
      { role: 'user', content: userMessage }
    ],
    stream: true,
    options: {
      temperature: 0.7,
      num_predict: 512,    // 최대 출력 토큰
      num_ctx: 4096,       // 컨텍스트 윈도우
    }
  });

  let fullResponse = '';
  for await (const chunk of response) {
    process.stdout.write(chunk.message.content);
    fullResponse += chunk.message.content;
  }
  return fullResponse;
}

// Embedding 생성 (벡터 검색용)
async function getEmbedding(text) {
  const response = await ollama.embeddings({
    model: 'bge-m3',      // 다국어 embedding 모델
    prompt: text
  });
  return response.embedding;  // 1024차원 벡터
}
```

#### 3.1.2 vLLM — 고성능 배치 추론 엔진

vLLM은 PagedAttention 기술로 GPU 메모리를 효율적으로 관리하여, 동시에 많은 요청을 처리할 수 있는 프로덕션급 LLM 서빙 엔진입니다.

**설치 및 서빙:**

```bash
# 설치 (CUDA 12.1 필요)
pip install vllm

# OpenAI 호환 API 서버 실행
python -m vllm.entrypoints.openai.api_server \
  --model meta-llama/Llama-3.1-8B-Instruct \
  --host 0.0.0.0 \
  --port 8000 \
  --max-model-len 4096 \
  --gpu-memory-utilization 0.90 \
  --dtype auto \
  --quantization awq \
  --tensor-parallel-size 1 \
  --swap-space 16 \
  --max-num-seqs 64

# API 호출 (OpenAI SDK 호환)
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Llama-3.1-8B-Instruct",
    "messages": [
      {"role": "system", "content": "한국어 심리상담 AI입니다."},
      {"role": "user", "content": "요즘 의욕이 없어요."}
    ],
    "max_tokens": 512,
    "temperature": 0.7,
    "stream": true
  }'
```

**vLLM의 PagedAttention 동작 원리:**

```
[기존 방식 — 연속 메모리 할당]
GPU VRAM: |████ Req1 KV-cache ████|    빈공간    |████ Req2 ████|  낭비  |
→ 메모리 단편화 발생, 동시 요청 수 제한

[vLLM PagedAttention — 페이지 단위 할당]
GPU VRAM: |R1|R2|R1|R3|R2|R1|R3|R2|R3|R1|R2|R3|  ← 빈틈없이 채움
→ 메모리 활용률 ~95%, 동시 요청 2~4배 증가
→ OS의 가상 메모리 페이징과 동일한 원리
```

**Node.js에서 vLLM 연동 (OpenAI SDK 호환):**

```javascript
// vllm-client.js
import OpenAI from 'openai';

const openai = new OpenAI({
  baseURL: 'http://localhost:8000/v1',  // vLLM 서버
  apiKey: 'not-needed',                 // 로컬이므로 키 불필요
});

async function streamChat(messages) {
  const stream = await openai.chat.completions.create({
    model: 'meta-llama/Llama-3.1-8B-Instruct',
    messages: messages,
    max_tokens: 512,
    temperature: 0.7,
    stream: true,
  });

  for await (const chunk of stream) {
    const content = chunk.choices[0]?.delta?.content || '';
    process.stdout.write(content);
  }
}
```

#### 3.1.3 llama.cpp (GGUF) — CPU+GPU 하이브리드 추론

llama.cpp는 C/C++로 작성된 경량 LLM 추론 엔진으로, GGUF 형식의 양자화 모델을 CPU와 GPU를 함께 사용하여 추론합니다. **128GB RAM 활용의 핵심입니다.**

**설치 및 구동:**

```bash
# 소스 빌드 (CUDA 지원)
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make LLAMA_CUDA=1

# 70B Q4_K_M 모델 실행 — CPU+GPU 하이브리드
./llama-server \
  --model models/llama-3.1-70b-instruct-q4_k_m.gguf \
  --host 0.0.0.0 \
  --port 8080 \
  --n-gpu-layers 35 \
  --ctx-size 4096 \
  --batch-size 512 \
  --threads 16 \
  --parallel 4 \
  --mlock \
  --cont-batching

# 옵션 설명:
# --n-gpu-layers 35   : 80개 레이어 중 35개를 GPU(24GB)에 로드
#                        나머지 45개 레이어는 RAM(~16GB)에서 처리
# --ctx-size 4096     : 컨텍스트 윈도우 크기
# --batch-size 512    : 배치 처리 크기
# --threads 16        : CPU 스레드 수 (EPYC 16코어)
# --parallel 4        : 동시 요청 처리 수
# --mlock             : 모델을 RAM에 고정 (swap 방지) ← 128GB RAM의 핵심 활용
# --cont-batching     : 연속 배치 처리로 throughput 향상
```

**GPU 레이어 수 최적화 가이드:**

```
[RTX 4090 24GB VRAM — 모델별 최적 --n-gpu-layers 설정]

┌──────────────────────┬────────┬─────────────┬───────────┬──────────┐
│ 모델                 │ 총 레이어│ GPU 레이어  │ VRAM 사용 │ RAM 사용 │
├──────────────────────┼────────┼─────────────┼───────────┼──────────┤
│ Llama 3.1 8B Q4     │ 32     │ 33 (전체)   │ ~5GB      │ 최소     │
│ Llama 3.1 8B FP16   │ 32     │ 33 (전체)   │ ~16GB     │ 최소     │
│ Llama 3.1 70B Q4    │ 80     │ 35          │ ~22GB     │ ~18GB    │
│ Llama 3.1 70B Q4    │ 80     │ 45          │ ~24GB     │ ~16GB    │
│ Llama 3.1 70B Q8    │ 80     │ 20          │ ~22GB     │ ~48GB    │
│ Qwen 2.5 72B Q4     │ 80     │ 35          │ ~22GB     │ ~18GB    │
└──────────────────────┴────────┴─────────────┴───────────┴──────────┘

→ 최적 전략: VRAM을 ~22GB까지 사용하고, 나머지를 RAM으로 offload
→ 128GB RAM이 있으면 여유롭게 offload + 다른 모델 동시 운영 가능
```

### 3.2 모델별 구동 방식 상세

#### 3.2.1 대화형 LLM (Llama 3.1 8B / 70B)

모든 서비스의 핵심 AI 엔진입니다.

```
┌─────────────────────────────────────────────────────────┐
│                  대화형 LLM 추론 파이프라인                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  [사용자]                                               │
│     │                                                   │
│     ▼                                                   │
│  [React/Flutter 앱]                                     │
│     │ WebSocket / HTTP                                  │
│     ▼                                                   │
│  [Nginx 리버스 프록시] (:443)                            │
│     │                                                   │
│     ▼                                                   │
│  [Node.js API 서버] (:3000)                             │
│     │ ① 사용자 인증                                     │
│     │ ② 대화 이력 조회 (PostgreSQL)                     │
│     │ ③ RAG 컨텍스트 검색 (pgvector)                    │
│     │ ④ 프롬프트 조립                                   │
│     ▼                                                   │
│  [Ollama/vLLM API] (:11434 / :8000)                    │
│     │ ⑤ GPU 추론 (RTX 4090)                            │
│     │ ⑥ 토큰 스트리밍 응답                              │
│     ▼                                                   │
│  [Node.js] → 응답 후처리                                │
│     │ ⑦ 안전성 필터링                                   │
│     │ ⑧ 대화 기록 저장                                  │
│     ▼                                                   │
│  [사용자] ← SSE/WebSocket 스트리밍                      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**모델 선택 전략:**

| 용도 | 모델 | 이유 |
|------|------|------|
| 일반 대화 | Llama 3.1 8B Q4 | 빠른 응답(~80 tok/s), 충분한 품질 |
| 심층 상담 | Llama 3.1 70B Q4 | 더 깊은 이해력, 복잡한 맥락 파악 |
| 한국어 특화 | Qwen 2.5 7B / EXAONE 3.0 7.8B | 한국어 벤치마크 우수 |
| 코드/분석 | DeepSeek-Coder-V2 16B | 데이터 분석, 탄소 계산 등 |

**응답 시간 벤치마크 (RTX 4090, Q4_K_M):**

| 모델 | 첫 토큰 지연 (TTFT) | 토큰 생성 속도 | 512 토큰 응답 시간 |
|------|---------------------|---------------|-------------------|
| Llama 3.1 8B | ~50ms | ~80 tok/s | ~6.4초 |
| Llama 3.1 70B | ~200ms | ~15 tok/s | ~34초 |
| Qwen 2.5 7B | ~45ms | ~85 tok/s | ~6.0초 |
| Mistral 7B | ~40ms | ~90 tok/s | ~5.7초 |

#### 3.2.2 Vision 모델 (YOLOv8 / CLIP)

그린바이트(음식물 인식), 배리어프리고(장애물 인식), 리유즈잇(물품 인식) 등에서 사용합니다.

```
┌──────────────────────────────────────────────────────────┐
│                Vision 추론 파이프라인                      │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  [사진 업로드] (JPEG/PNG, 최대 10MB)                     │
│     │                                                    │
│     ▼                                                    │
│  [이미지 전처리]                                         │
│     │ ① 리사이즈: 640×640 (YOLOv8) / 224×224 (CLIP)    │
│     │ ② 정규화: [0,1] 범위 변환                         │
│     │ ③ 텐서 변환: NCHW 형식                            │
│     ▼                                                    │
│  [YOLOv8-nano] (GPU, ~6MB)                              │
│     │ ④ 객체 탐지: 바운딩박스 + 클래스 + 신뢰도          │
│     │    예: {class: "rice", bbox: [x,y,w,h], conf: 0.92}│
│     │ ⑤ 추론 시간: <10ms                                │
│     ▼                                                    │
│  [CLIP ViT-B/32] (GPU, ~600MB) — 선택적                 │
│     │ ⑥ 이미지-텍스트 유사도 계산                        │
│     │    예: "남은 밥" ↔ 이미지 = 0.87 유사도            │
│     ▼                                                    │
│  [Llama 3.1 8B]                                          │
│     │ ⑦ 탐지 결과 + 텍스트 분석                         │
│     │    "사진에서 밥 150g, 김치 50g이 남았습니다.        │
│     │     내일 김치볶음밥을 만들어 보시는 건 어떨까요?"   │
│     ▼                                                    │
│  [응답 반환]                                             │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**YOLOv8 구동 코드:**

```python
# vision_inference.py
from ultralytics import YOLO
import torch

# YOLOv8-nano 모델 로드 (한국 음식 fine-tuned)
model = YOLO('models/yolov8n-korean-food.pt')

# GPU 추론
results = model.predict(
    source='uploaded_image.jpg',
    conf=0.5,           # 최소 신뢰도
    iou=0.45,           # NMS IoU 임계값
    device='cuda:0',    # GPU 사용
    imgsz=640,
    verbose=False
)

# 결과 파싱
for result in results:
    for box in result.boxes:
        cls_name = model.names[int(box.cls)]  # "rice", "kimchi", etc.
        confidence = float(box.conf)
        bbox = box.xyxy[0].tolist()
        print(f"{cls_name}: {confidence:.2f} at {bbox}")
```

**VRAM 사용량:**

| 모델 | VRAM 사용 | 추론 시간 | 정확도 (mAP50) |
|------|-----------|-----------|----------------|
| YOLOv8-nano | ~6MB | <10ms | 37.3% |
| YOLOv8-small | ~22MB | ~15ms | 44.9% |
| YOLOv8-medium | ~52MB | ~25ms | 50.2% |
| CLIP ViT-B/32 | ~600MB | ~30ms | — |

→ YOLOv8-nano는 VRAM 사용이 극소량이므로 LLM과 동시에 GPU에 로드해도 무리가 없습니다.

#### 3.2.3 STT/TTS (Whisper / VITS)

시니어톡(독거노인 음성 대화), 마음이음(음성 상담), 배리어프리고(시각장애인 음성 안내) 등에서 사용합니다.

```
┌──────────────────────────────────────────────────────────┐
│               음성 처리 파이프라인                         │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  [사용자 음성 입력] (마이크 / 전화)                       │
│     │                                                    │
│     ▼                                                    │
│  [VAD — 음성 활동 감지]                                  │
│     │ Silero VAD (~2MB, CPU)                             │
│     │ 음성 구간만 추출, 무음 제거                         │
│     ▼                                                    │
│  [Whisper large-v3] (GPU, ~3GB VRAM)                    │
│     │ STT: 음성 → 한국어 텍스트                          │
│     │ 정확도: WER ~5% (한국어)                           │
│     │ 추론 시간: 실시간의 ~0.5배 (30초 음성 → ~15초)     │
│     ▼                                                    │
│  [텍스트 전처리]                                         │
│     │ 맞춤법 교정, 비속어 필터링                          │
│     ▼                                                    │
│  [Llama 3.1 8B] (GPU)                                   │
│     │ 대화 응답 생성                                     │
│     ▼                                                    │
│  [VITS Korean TTS] (GPU, ~200MB VRAM)                   │
│     │ TTS: 텍스트 → 음성 합성                            │
│     │ 자연스러운 한국어 음성 (남성/여성 선택)             │
│     │ 합성 시간: 실시간의 ~0.1배 (매우 빠름)             │
│     ▼                                                    │
│  [음성 출력] (스피커 / 전화)                              │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Whisper 구동 코드:**

```python
# stt_service.py
import whisper
import torch

# Whisper large-v3 로드 (GPU)
model = whisper.load_model("large-v3", device="cuda")

def transcribe(audio_path: str) -> dict:
    """음성 파일을 한국어 텍스트로 변환"""
    result = model.transcribe(
        audio_path,
        language="ko",
        task="transcribe",
        fp16=True,              # FP16으로 VRAM 절약
        beam_size=5,
        best_of=5,
        temperature=0.0,
        condition_on_previous_text=True,
        initial_prompt="다음은 한국어 대화입니다.",
    )
    return {
        "text": result["text"],
        "segments": result["segments"],
        "language": result["language"]
    }
```

**VITS TTS 구동 코드:**

```python
# tts_service.py
from TTS.api import TTS
import torch

# 한국어 VITS 모델 로드
tts = TTS(model_name="tts_models/ko/cv/vits", gpu=True)

def synthesize(text: str, output_path: str, speaker: str = "female"):
    """텍스트를 한국어 음성으로 합성"""
    tts.tts_to_file(
        text=text,
        file_path=output_path,
        speaker=speaker,
        speed=0.95,      # 시니어 대상: 약간 느리게
    )
    return output_path
```

**음성 모델 리소스 사용량:**

| 모델 | VRAM | RAM 버퍼 | 추론 속도 |
|------|------|----------|-----------|
| Whisper large-v3 | ~3GB | ~2GB | RTF 0.5 (실시간의 2배 빠름) |
| Whisper medium | ~1.5GB | ~1GB | RTF 0.3 |
| Whisper small | ~0.5GB | ~0.5GB | RTF 0.15 |
| VITS Korean | ~200MB | ~100MB | RTF 0.1 (실시간의 10배 빠름) |
| Silero VAD | CPU only | ~50MB | RTF 0.01 |

#### 3.2.4 Embedding 모델 (검색/매칭)

룸메이트(주거 매칭), 스터디브릿지(학습 매칭), 동네알바(인력 매칭) 등 매칭 서비스의 핵심입니다.

```
┌──────────────────────────────────────────────────────────┐
│               벡터 검색 파이프라인 (RAG)                   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  [데이터 인덱싱 — 오프라인 배치]                          │
│     │                                                    │
│     ▼                                                    │
│  [텍스트 청킹]                                           │
│     │ 문서를 512 토큰 단위로 분할                         │
│     │ 50 토큰 오버랩 (문맥 유지)                         │
│     ▼                                                    │
│  [Embedding 모델]                                        │
│     │ all-MiniLM-L6-v2: 384차원, ~80MB, CPU 추론 가능   │
│     │ BGE-M3: 1024차원, ~570MB, 다국어 지원 우수         │
│     │ KoSimCSE: 768차원, ~440MB, 한국어 특화             │
│     ▼                                                    │
│  [pgvector 저장]                                         │
│     │ PostgreSQL의 vector 확장                           │
│     │ HNSW 인덱스로 밀리초 단위 유사도 검색               │
│     ▼                                                    │
│                                                          │
│  [실시간 검색 — 사용자 쿼리]                              │
│     │                                                    │
│     ▼                                                    │
│  [쿼리 Embedding]                                        │
│     │ 사용자 질문을 동일 모델로 벡터화                    │
│     ▼                                                    │
│  [pgvector 코사인 유사도 검색]                            │
│     │ SELECT * FROM documents                            │
│     │ ORDER BY embedding <=> query_vector                │
│     │ LIMIT 5;                                           │
│     ▼                                                    │
│  [검색 결과 + 사용자 질문 → LLM 프롬프트]                │
│     │ "다음 문서를 참고하여 답변해주세요: {context}"       │
│     ▼                                                    │
│  [Llama 3.1 8B] → 최종 응답                              │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**pgvector 설정 및 사용:**

```sql
-- PostgreSQL에 pgvector 확장 설치
CREATE EXTENSION vector;

-- 벡터 테이블 생성 (BGE-M3 1024차원 기준)
CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    metadata JSONB,
    embedding vector(1024),
    created_at TIMESTAMP DEFAULT NOW()
);

-- HNSW 인덱스 생성 (빠른 유사도 검색)
CREATE INDEX ON documents
USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 200);

-- 유사도 검색 쿼리
SELECT id, content, metadata,
       1 - (embedding <=> $1::vector) AS similarity
FROM documents
ORDER BY embedding <=> $1::vector
LIMIT 5;
```

**Embedding 모델 비교:**

| 모델 | 차원 | 크기 | 한국어 성능 | 추론 장치 | 속도 (문장/초) |
|------|------|------|-------------|-----------|---------------|
| all-MiniLM-L6-v2 | 384 | ~80MB | 보통 | CPU | ~1,000 |
| BGE-M3 | 1024 | ~570MB | 우수 | CPU/GPU | ~500 |
| KoSimCSE-roberta | 768 | ~440MB | 최고 | CPU/GPU | ~600 |
| multilingual-e5-large | 1024 | ~1.1GB | 우수 | GPU 권장 | ~300 |

### 3.3 동시 운영 시 메모리 배분 상세

#### 시나리오 A: 8B 모델 운영 (일반 서비스)

```
┌─────────────────────────────────────────────────────┐
│          GPU VRAM 24GB (RTX 4090) 배분              │
├─────────────────────────────────────────────────────┤
│ Llama 3.1 8B Q4_K_M          │ 5.0GB              │
│ Whisper large-v3              │ 3.0GB              │
│ YOLOv8-nano                   │ 0.1GB              │
│ VITS TTS                      │ 0.2GB              │
│ CUDA 커널 + 오버헤드          │ 1.5GB              │
│ KV-cache (GPU)                │ 4.0GB              │
│ ──────────────────────────── │ ─────              │
│ 여유                          │ 10.2GB             │
└─────────────────────────────────────────────────────┘
→ GPU 여유 넉넉. 추가 모델 로드 가능.

┌─────────────────────────────────────────────────────┐
│             System RAM 128GB 배분                   │
├─────────────────────────────────────────────────────┤
│ PostgreSQL + pgvector          │ 8GB               │
│ Redis 캐시                     │ 4GB               │
│ Node.js 서버 (V8 힙)           │ 4GB               │
│ Embedding 모델 (BGE-M3)        │ 1GB               │
│ Whisper 오디오 버퍼             │ 2GB               │
│ OS + 시스템                     │ 8GB               │
│ ──────────────────────────── │ ─────              │
│ 여유                           │ 101GB             │
└─────────────────────────────────────────────────────┘
→ RAM 대폭 여유. 128GB가 아닌 32~64GB로도 운영 가능.
→ 단, 70B 모델 필요 시 아래 시나리오 B 참조.
```

#### 시나리오 B: 70B 모델 운영 (고품질 서비스)

```
┌─────────────────────────────────────────────────────┐
│          GPU VRAM 24GB (RTX 4090) 배분              │
├─────────────────────────────────────────────────────┤
│ Llama 3.1 70B Q4 (35 layers)  │ 22.0GB            │
│ CUDA 커널 + 오버헤드          │ 1.5GB              │
│ KV-cache (소량)               │ 0.5GB              │
│ ──────────────────────────── │ ─────              │
│ 여유                          │ 0.0GB (꽉 참)      │
└─────────────────────────────────────────────────────┘
→ GPU에 LLM만 로드. Whisper/YOLO는 CPU 또는 시간 분할 사용.

┌─────────────────────────────────────────────────────┐
│             System RAM 128GB 배분                   │
├─────────────────────────────────────────────────────┤
│ Llama 3.1 70B Q4 (45 layers)  │ 18GB  ← offload   │
│ KV-cache (RAM)                │ 8GB                │
│ Whisper large-v3 (CPU 모드)   │ 6GB                │
│ Embedding 모델                 │ 1GB                │
│ VITS TTS (CPU 모드)           │ 1GB                │
│ PostgreSQL + pgvector          │ 8GB                │
│ Redis 캐시                     │ 4GB                │
│ Node.js 서버                   │ 4GB                │
│ OS + 시스템                     │ 8GB                │
│ 배치 처리 버퍼                  │ 10GB               │
│ ──────────────────────────── │ ─────              │
│ 여유 (안전 마진)               │ 60GB               │
└─────────────────────────────────────────────────────┘
→ 128GB RAM 필수. 64GB로는 70B offload + 다른 모델 동시 운영 불가.
→ 여유 60GB는 사용자 증가 시 KV-cache 확장, 배치 처리에 활용.
```

#### 시나리오 C: 멀티 서비스 동시 운영

10개 서비스를 모두 하나의 서버에서 운영하는 경우:

```
[시간대별 모델 스케줄링 전략]

06:00~09:00  아침  → 그린바이트(음식 인식 집중) → YOLOv8 + 8B LLM
09:00~18:00  주간  → 동네알바, 스터디브릿지(매칭) → Embedding + 8B LLM
18:00~22:00  저녁  → 마음이음, 시니어톡(상담) → 70B LLM + Whisper
22:00~06:00  야간  → 배치 처리, fine-tuning → 70B QLoRA 학습

→ 시간대별로 GPU에 로드하는 모델을 동적으로 변경
→ Ollama의 모델 핫스왑 기능 활용 (모델 교체 ~10초)
```

---

## 4. Fine-tuning 파이프라인

### 4.1 LoRA / QLoRA 방식

#### LoRA (Low-Rank Adaptation) 개요

전체 모델의 가중치를 재학습하지 않고, 작은 "어댑터" 행렬만 학습하여 도메인 특화 성능을 얻는 기법입니다.

```
[LoRA 원리]

기존 방식 (Full Fine-tuning):
W_new = W_original + ΔW          (ΔW: 전체 파라미터 크기, 수십 GB)
→ 70B 모델 학습 시 ~280GB VRAM 필요 (불가능)

LoRA 방식:
W_new = W_original + A × B       (A: d×r, B: r×d, r=16~64)
→ r(rank)이 작으므로 어댑터 크기 수 MB ~ 수십 MB
→ 원본 모델은 고정(frozen), 어댑터만 학습

QLoRA 방식 (Quantized LoRA):
W_original을 4bit 양자화 상태로 유지 + LoRA 어댑터 학습
→ 70B 모델도 24GB VRAM + 128GB RAM으로 학습 가능!

┌──────────────────────────────────────────────────────┐
│                  QLoRA 메모리 사용량                   │
├──────────────────────────────────────────────────────┤
│ 70B 모델 (4bit 양자화)        │ ~35GB (VRAM+RAM)    │
│ LoRA 어댑터 (r=64)            │ ~1GB                │
│ 옵티마이저 상태 (AdamW)       │ ~4GB                │
│ 그래디언트                     │ ~2GB                │
│ 활성화 값 (배치 크기에 비례)   │ ~4GB                │
│ ──────────────────────────── │ ─────               │
│ 합계                          │ ~46GB               │
│ → GPU 24GB + RAM offload 22GB │ 128GB RAM에서 가능  │
└──────────────────────────────────────────────────────┘
```

#### 서비스별 Fine-tuning 계획

| 서비스 | 학습 데이터 | 데이터 양 | 모델 | 학습 시간 |
|--------|------------|-----------|------|-----------|
| 마음이음 | 심리상담 대화 코퍼스 (AI Hub 상담 데이터) | 10만 쌍 | Llama 3.1 8B | ~4시간 |
| 그린바이트 | 한국 레시피 + 식재료 데이터 | 5만 건 | Llama 3.1 8B | ~2시간 |
| 시니어톡 | 고령자 대화체 + 방언 데이터 | 8만 쌍 | Llama 3.1 8B | ~3시간 |
| 배리어프리고 | 장애 유형별 접근성 가이드 | 3만 건 | Llama 3.1 8B | ~1.5시간 |
| 동네알바 | 채용 공고 / 이력서 매칭 데이터 | 20만 건 | Embedding 모델 | ~6시간 |
| 탄소다이어리 | 활동별 탄소 배출량 데이터 | 2만 건 | Llama 3.1 8B | ~1시간 |

#### 학습 데이터 형식

```jsonl
// mentalcare_train.jsonl
{"instruction": "사용자가 우울감을 호소합니다. 공감하며 CBT 기법으로 응답하세요.",
 "input": "요즘 아무것도 하기 싫고 침대에만 있고 싶어요.",
 "output": "그런 마음이 드시는 게 정말 힘드시겠어요. 아무것도 하고 싶지 않은 느낌이 들 때가 있죠. 혹시 이런 기분이 언제부터 시작됐는지 기억나시나요? 그리고 그 전에는 즐겼던 활동이 있으셨다면 어떤 것이었는지 이야기해 주실 수 있을까요?"}

{"instruction": "사용자가 불면증을 호소합니다.",
 "input": "밤에 잠이 안 와서 새벽 3시까지 뒤척여요.",
 "output": "잠이 잘 오지 않으면 정말 고통스럽죠. 수면은 우리 몸과 마음의 회복에 매우 중요한데, 어려움을 겪고 계시군요. 혹시 잠들기 전에 주로 어떤 활동을 하시나요? 그리고 낮 동안 카페인(커피, 차 등)은 얼마나 드시는지도 궁금해요."}
```

### 4.2 학습 프레임워크 및 실행 코드

#### Unsloth (권장 — 2~5배 빠른 학습)

```python
# train_mentalcare_lora.py
from unsloth import FastLanguageModel
from trl import SFTTrainer
from transformers import TrainingArguments
from datasets import load_dataset

# 1. 모델 로드 (4bit 양자화)
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="unsloth/Meta-Llama-3.1-8B-Instruct-bnb-4bit",
    max_seq_length=4096,
    dtype=None,           # 자동 감지
    load_in_4bit=True,    # QLoRA
)

# 2. LoRA 어댑터 설정
model = FastLanguageModel.get_peft_model(
    model,
    r=64,                          # LoRA rank
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj",
                     "gate_proj", "up_proj", "down_proj"],
    lora_alpha=16,
    lora_dropout=0.05,
    bias="none",
    use_gradient_checkpointing="unsloth",  # 메모리 절약
)

# 3. 데이터셋 로드
dataset = load_dataset("json", data_files="mentalcare_train.jsonl", split="train")

# 4. 프롬프트 템플릿
prompt_template = """<|begin_of_text|><|start_header_id|>system<|end_header_id|>
당신은 마음이음 AI 심리상담 도우미입니다. 공감적이고 따뜻하게 대화합니다.<|eot_id|>
<|start_header_id|>user<|end_header_id|>
{input}<|eot_id|>
<|start_header_id|>assistant<|end_header_id|>
{output}<|eot_id|>"""

def format_prompt(example):
    return {"text": prompt_template.format(**example)}

dataset = dataset.map(format_prompt)

# 5. 학습 실행
trainer = SFTTrainer(
    model=model,
    tokenizer=tokenizer,
    train_dataset=dataset,
    dataset_text_field="text",
    max_seq_length=4096,
    packing=True,                  # 짧은 샘플 묶어서 효율 향상
    args=TrainingArguments(
        per_device_train_batch_size=4,
        gradient_accumulation_steps=4,
        warmup_steps=100,
        num_train_epochs=3,
        learning_rate=2e-4,
        fp16=True,
        logging_steps=10,
        output_dir="outputs/mentalcare-lora",
        optim="adamw_8bit",        # 8bit 옵티마이저로 메모리 절약
        seed=42,
    ),
)

trainer.train()

# 6. LoRA 어댑터 저장
model.save_pretrained("outputs/mentalcare-lora")

# 7. GGUF 변환 (Ollama에서 사용하기 위해)
model.save_pretrained_gguf(
    "outputs/mentalcare-lora-gguf",
    tokenizer,
    quantization_method="q4_k_m"
)
```

```bash
# 학습 실행
python train_mentalcare_lora.py

# 학습 완료 후 Ollama에 커스텀 모델 등록
cat > Modelfile.mentalcare-finetuned << 'EOF'
FROM outputs/mentalcare-lora-gguf/unsloth.Q4_K_M.gguf
SYSTEM "당신은 마음이음 AI 심리상담 도우미입니다."
PARAMETER temperature 0.7
PARAMETER top_p 0.9
EOF

ollama create mentalcare-finetuned -f Modelfile.mentalcare-finetuned
ollama run mentalcare-finetuned
```

#### Axolotl (설정 기반 간편 학습)

```yaml
# axolotl_config.yml
base_model: meta-llama/Meta-Llama-3.1-8B-Instruct
model_type: LlamaForCausalLM
load_in_4bit: true
adapter: qlora
lora_r: 64
lora_alpha: 16
lora_dropout: 0.05
lora_target_modules:
  - q_proj
  - k_proj
  - v_proj
  - o_proj
  - gate_proj
  - up_proj
  - down_proj

datasets:
  - path: mentalcare_train.jsonl
    type: instruction
    input_field: input
    output_field: output

sequence_len: 4096
micro_batch_size: 4
gradient_accumulation_steps: 4
num_epochs: 3
learning_rate: 0.0002
optimizer: adamw_bnb_8bit
lr_scheduler: cosine
warmup_steps: 100

output_dir: outputs/mentalcare-axolotl
```

```bash
# Axolotl 실행
accelerate launch -m axolotl.cli.train axolotl_config.yml
```

---

## 5. 배포 아키텍처

### 5.1 개발 단계 (MVP — 단일 서버)

```
┌──────────────────────────────────────────────────────────────┐
│                    단일 128GB 서버                            │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │ Docker Compose                                          │ │
│  │                                                         │ │
│  │  ┌─────────┐  ┌──────────┐  ┌────────────────────────┐ │ │
│  │  │ Nginx   │→│ Node.js  │→│ Ollama                  │ │ │
│  │  │ :443    │  │ :3000    │  │ :11434                  │ │ │
│  │  │ SSL/TLS │  │ Express  │  │ Llama 3.1 8B            │ │ │
│  │  │ Rate    │  │ 인증/인가 │  │ + custom Modelfile     │ │ │
│  │  │ Limit   │  │ 비즈로직  │  │                        │ │ │
│  │  └─────────┘  └──────────┘  └────────────────────────┘ │ │
│  │       │            │                    │               │ │
│  │       │       ┌────┴────┐          ┌────┴────┐         │ │
│  │       │       │ Postgres│          │ Whisper │         │ │
│  │       │       │ :5432   │          │ (Python)│         │ │
│  │       │       │ +pgvec  │          │ STT/TTS │         │ │
│  │       │       └─────────┘          └─────────┘         │ │
│  │       │            │                                    │ │
│  │       │       ┌────┴────┐                               │ │
│  │       │       │ Redis   │                               │ │
│  │       │       │ :6379   │                               │ │
│  │       │       │ 캐싱    │                               │ │
│  │       │       └─────────┘                               │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                              │
│  [GPU: RTX 4090 24GB]  [RAM: 128GB]  [SSD: 2TB NVMe]       │
└──────────────────────────────────────────────────────────────┘
```

**Docker Compose 설정:**

```yaml
# docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - api

  api:
    build: ./api
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - OLLAMA_HOST=http://ollama:11434
      - DATABASE_URL=postgresql://user:pass@postgres:5432/app
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    deploy:
      resources:
        limits:
          memory: 4G

  ollama:
    image: ollama/ollama:latest
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
        limits:
          memory: 80G    # 70B 모델 offload용 RAM 확보

  postgres:
    image: pgvector/pgvector:pg16
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=app
    volumes:
      - pg_data:/var/lib/postgresql/data
    deploy:
      resources:
        limits:
          memory: 8G
    command: >
      postgres
      -c shared_buffers=2GB
      -c effective_cache_size=6GB
      -c work_mem=256MB
      -c maintenance_work_mem=512MB

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    deploy:
      resources:
        limits:
          memory: 4G
    command: redis-server --maxmemory 3gb --maxmemory-policy allkeys-lru

  whisper:
    build: ./whisper-service
    ports:
      - "8001:8001"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
        limits:
          memory: 8G

volumes:
  ollama_data:
  pg_data:
  redis_data:
```

### 5.2 확장 단계 (사용자 증가 시)

```
┌──────────────────────────────────────────────────────────────────┐
│                      프로덕션 아키텍처                            │
│                                                                  │
│  [사용자]                                                        │
│     │                                                            │
│     ▼                                                            │
│  ┌──────────────────┐                                            │
│  │ 로드밸런서       │  HAProxy / Nginx                           │
│  │ (L7 + WebSocket) │  SSL 종단, Rate Limiting                  │
│  └────────┬─────────┘                                            │
│           │                                                      │
│     ┌─────┴─────┬──────────────┐                                 │
│     ▼           ▼              ▼                                 │
│  ┌──────┐  ┌──────┐     ┌──────────────┐                        │
│  │API 1 │  │API 2 │     │ API N        │  Node.js 클러스터      │
│  │:3000 │  │:3000 │ ... │ :3000        │  (PM2 / Kubernetes)    │
│  └──┬───┘  └──┬───┘     └──────┬───────┘                        │
│     │         │                │                                 │
│     └─────────┼────────────────┘                                 │
│               │                                                  │
│     ┌─────────┴─────────┐                                        │
│     ▼                   ▼                                        │
│  ┌──────────────┐  ┌──────────────┐                              │
│  │ GPU 서버 1   │  │ GPU 서버 2   │                              │
│  │ RTX 4090     │  │ RTX 4090     │   vLLM 클러스터             │
│  │ 128GB RAM    │  │ 128GB RAM    │   (라운드로빈 분배)          │
│  │ vLLM :8000   │  │ vLLM :8000   │                              │
│  └──────────────┘  └──────────────┘                              │
│               │                                                  │
│     ┌─────────┴─────────┐                                        │
│     ▼                   ▼                                        │
│  ┌──────────────┐  ┌──────────────┐                              │
│  │ PostgreSQL   │  │ Redis        │                              │
│  │ Primary      │  │ Cluster      │   데이터 계층               │
│  │ + Replica    │  │ (3 nodes)    │                              │
│  └──────────────┘  └──────────────┘                              │
│                                                                  │
│  ┌──────────────────────────────────┐                            │
│  │ 모니터링                          │                            │
│  │ Grafana + Prometheus + AlertMgr  │                            │
│  │ GPU 사용률, 추론 지연시간,       │                            │
│  │ 에러율, 메모리 사용량 대시보드    │                            │
│  └──────────────────────────────────┘                            │
└──────────────────────────────────────────────────────────────────┘
```

### 5.3 동시 사용자 처리 성능

| 구성 | 모델 | 서빙 엔진 | 동시 요청 | 평균 응답 시간 | 월 비용 |
|------|------|-----------|-----------|---------------|---------|
| RTX 4090 × 1 | 8B Q4 | Ollama | ~20 | ~8초 (512 tok) | 전기 5만원 |
| RTX 4090 × 1 | 8B Q4 | vLLM | ~50 | ~6초 (512 tok) | 전기 5만원 |
| RTX 4090 × 1 | 70B Q4 | llama.cpp | ~10 | ~35초 (512 tok) | 전기 5만원 |
| RTX 4090 × 2 | 8B Q4 | vLLM 클러스터 | ~100 | ~6초 | 전기 10만원 |
| RTX 4090 × 4 | 8B Q4 | vLLM 클러스터 | ~200 | ~6초 | 전기 20만원 |

**성능 최적화 기법:**

```
1. 응답 캐싱 (Redis)
   - 동일/유사 질문에 대한 응답 캐싱
   - 캐시 적중률 ~30% → 실질 처리량 1.4배 향상

2. 프롬프트 프리필 (Prompt Caching)
   - 시스템 프롬프트 + 공통 컨텍스트를 미리 계산
   - TTFT(첫 토큰 지연) ~50% 감소

3. Speculative Decoding
   - 작은 모델(1B)이 초안 생성 → 큰 모델(8B)이 검증
   - 전체 추론 속도 ~2배 향상

4. Continuous Batching (vLLM)
   - 요청이 끝나는 즉시 새 요청 배치에 추가
   - GPU 유휴 시간 최소화
```

---

## 6. 비용 비교 분석

### 6.1 로컬 vs 클라우드 3년 TCO (Total Cost of Ownership)

#### 전제 조건

| 항목 | 값 |
|------|-----|
| 서비스 대상 MAU | 100,000명 |
| 1인당 월 평균 AI 호출 | 20회 |
| 1회 평균 토큰 | 입력 500 + 출력 800 = 1,300 |
| 월간 총 토큰 | 26억 (2.6B) |
| 서버 가동률 | 24/7 (365일) |
| 전기 요금 | kWh당 120원 (산업용) |

#### 3년 비용 비교표

| 항목 | 로컬 128GB 서버 | OpenAI GPT-4o API | OpenAI GPT-4o-mini API | AWS Bedrock (Claude) |
|------|-----------------|-------------------|----------------------|---------------------|
| **초기 투자** | 1,000만원 | 0원 | 0원 | 0원 |
| **월 운영비** | | | | |
| - 전기료 | 5만원 | — | — | — |
| - API 비용 | 0원 | ~2,400만원 | ~240만원 | ~1,800만원 |
| - 인터넷 | 3만원 | 3만원 | 3만원 | 3만원 |
| - 관리 인력 | 포함* | 포함* | 포함* | 포함* |
| **월 소계** | **8만원** | **~2,403만원** | **~243만원** | **~1,803만원** |
| **3년 총 비용** | **1,288만원** | **약 8.65억원** | **약 8,748만원** | **약 6.49억원** |
| **GPT-4o 대비** | **1배 (기준)** | **67배** | **6.8배** | **50배** |

> *관리 인력 비용은 어떤 방식이든 동일하게 필요하므로 비교에서 제외

#### 상세 계산

```
[로컬 서버 3년 TCO]
초기 투자: 1,000만원 (서버 구축)
전기료:   5만원 × 36개월 = 180만원
  - RTX 4090: 450W × 0.7 (평균 부하) = 315W
  - 시스템:   200W
  - 합계:     515W × 24h × 30일 = 371kWh/월
  - 비용:     371 × 120원 = 44,520원 ≈ 5만원/월
인터넷:   3만원 × 36개월 = 108만원
──────────────────────────────────
3년 합계: 1,000 + 180 + 108 = 1,288만원

[OpenAI GPT-4o 3년 TCO]
입력: 26억 tokens × 50% (입력비율) / 1M × $2.50 = $3,250/월
출력: 26억 tokens × 50% (출력비율) / 1M × $10.00 = $13,000/월
월 합계: $16,250 ≈ 약 2,112만원/월
  (실제 프롬프트 오버헤드 포함 시 ~2,400만원)
3년 합계: 2,400만원 × 36개월 = 약 8.64억원
```

#### 손익분기점 분석

```
[MAU별 월 비용 비교]

MAU      │ 로컬 (월)  │ GPT-4o (월)  │ GPT-4o-mini (월) │ 로컬 유리?
─────────┼───────────┼─────────────┼─────────────────┼──────────
1,000    │ 8만원     │ 24만원       │ 2.4만원          │ mini가 유리
5,000    │ 8만원     │ 120만원      │ 12만원           │ 로컬 유리
10,000   │ 8만원     │ 240만원      │ 24만원           │ 로컬 유리
50,000   │ 8만원     │ 1,200만원    │ 120만원          │ 로컬 유리
100,000  │ 8만원     │ 2,400만원    │ 240만원          │ 로컬 유리
500,000  │ 16만원*   │ 12,000만원   │ 1,200만원        │ 로컬 유리

* 50만 MAU 시 서버 2대 운영 가정

→ 손익분기점: MAU ~3,000 이상이면 로컬이 경제적
→ 예비창업패키지 목표 MAU(1만~10만)에서는 로컬이 압도적 유리
```

### 6.2 예비창업패키지 사업비 배분 제안

```
[총 사업비 1억원 기준 — AI 인프라 배분]

┌──────────────────────┬──────────┬────────────────────────────┐
│ 항목                 │ 금액     │ 세부 내역                   │
├──────────────────────┼──────────┼────────────────────────────┤
│ 서버 하드웨어        │ 1,000만원│ 128GB RAM + RTX 4090 서버   │
│ 클라우드 백업        │ 200만원  │ Vast.ai 개발/학습용 (10개월)│
│ 데이터 수집/정제     │ 300만원  │ 학습 데이터 구축             │
│ 개발 인건비          │ 5,000만원│ 개발자 2~3명 (10개월)       │
│ 운영비 (전기/인터넷) │ 100만원  │ 10개월 운영                  │
│ 마케팅/사용자 확보   │ 1,000만원│ 베타 테스트, 홍보            │
│ 기타 (법률/회계/SW)  │ 400만원  │ 라이센스, 전문가 자문        │
│ 예비비               │ 1,000만원│ 긴급 대응                    │
├──────────────────────┼──────────┼────────────────────────────┤
│ 합계                 │ 10,000만원│                             │
└──────────────────────┴──────────┴────────────────────────────┘

→ 서버 구축비(1,000만원)는 전체 사업비의 10%로 합리적인 투자
→ 클라우드 API 방식이었다면 API 비용만으로 사업비 전부 소진
```

---

## 7. 보안 및 개인정보보호

### 7.1 데이터 보안 아키텍처

```
┌──────────────────────────────────────────────────────────────┐
│                     보안 아키텍처                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  [인터넷]                                                    │
│     │                                                        │
│     ▼                                                        │
│  ┌──────────────────┐                                        │
│  │ 방화벽 (UFW)     │  허용 포트: 443(HTTPS), 22(SSH)만     │
│  └────────┬─────────┘                                        │
│           │                                                  │
│  ┌────────▼─────────┐                                        │
│  │ Nginx (TLS 1.3)  │  Let's Encrypt 인증서                 │
│  │ + WAF Rules      │  OWASP ModSecurity                    │
│  └────────┬─────────┘                                        │
│           │                                                  │
│  ┌────────▼─────────────────────────────────────────┐       │
│  │              내부 네트워크 (격리)                   │       │
│  │                                                   │       │
│  │  [Node.js API]                                    │       │
│  │     │ JWT 인증                                    │       │
│  │     │ RBAC 권한 관리                              │       │
│  │     │ 입력값 검증/sanitize                        │       │
│  │     ▼                                             │       │
│  │  [Ollama] ← localhost만 접근 허용                 │       │
│  │     │ 외부 네트워크 차단                           │       │
│  │     │ 모든 추론이 내부에서 완결                    │       │
│  │     ▼                                             │       │
│  │  [PostgreSQL]                                     │       │
│  │     │ AES-256 암호화 (at-rest)                    │       │
│  │     │ TLS 암호화 (in-transit)                     │       │
│  │     │ 민감 컬럼 개별 암호화 (pgcrypto)            │       │
│  │                                                   │       │
│  └───────────────────────────────────────────────────┘       │
│                                                              │
│  ★ 핵심: AI 추론 데이터가 서버 외부로 나가지 않음            │
│  ★ 클라우드 API 대비 개인정보 유출 위험 원천 차단            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 7.2 개인정보보호법 준수

| 법 조항 | 요구사항 | 로컬 LLM 대응 |
|---------|---------|---------------|
| 제15조 (수집·이용) | 목적 내 최소 수집 | 서비스 목적 외 데이터 미수집, 서버 내 처리 |
| 제17조 (제3자 제공) | 동의 없이 제3자 제공 금지 | **외부 API 미사용 → 제3자 제공 자체가 없음** |
| 제21조 (파기) | 목적 달성 시 파기 | 서버 내 직접 삭제, 완전 파기 보장 |
| 제24조의2 (주민번호) | 원칙적 처리 제한 | 수집하지 않음 |
| 제29조 (안전조치) | 기술적·관리적 보호조치 | 암호화, 접근 통제, 침입 탐지 |

### 7.3 민감정보별 보호 방안

| 서비스 | 민감정보 유형 | 보호 수준 | 구체적 조치 |
|--------|-------------|-----------|-------------|
| 마음이음 | 정신건강 상담 기록 | 최고 | E2E 암호화, 익명화 처리, 자동 파기(1년) |
| 시니어톡 | 건강 상태, 복약 정보 | 최고 | 의료 데이터 별도 암호화, 접근 로그 |
| 배리어프리고 | 장애 유형, 위치 정보 | 높음 | 위치 정보 해상도 제한(동 단위) |
| 룸메이트 | 생활 습관, 성별 | 보통 | 매칭 후 상세정보 파기 |
| 탄소다이어리 | 소비/이동 패턴 | 보통 | 통계 처리 후 개인 식별 불가 처리 |

### 7.4 망분리 환경 배포

공공기관, 병원, 학교 등 망분리 환경에서도 로컬 LLM은 문제 없이 배포할 수 있습니다:

```bash
# 오프라인 설치 절차

# 1. 인터넷 연결 환경에서 패키지 준비
ollama pull llama3.1:8b
# ~/.ollama/models/ 폴더를 USB/외장하드에 복사

# 2. 망분리 환경 서버로 이동
# USB를 통해 모델 파일 복사

# 3. Ollama 오프라인 설치 (사전 다운로드한 바이너리)
sudo cp ollama-linux-amd64 /usr/local/bin/ollama
sudo chmod +x /usr/local/bin/ollama

# 4. 모델 파일 배치
mkdir -p ~/.ollama/models/
cp -r /media/usb/models/* ~/.ollama/models/

# 5. 서비스 시작 (인터넷 불필요)
ollama serve &
ollama run llama3.1:8b  # 로컬 모델 파일에서 직접 로드
```

---

## 8. 서비스별 AI 모델 매핑 요약

본 프로젝트의 10개 서비스가 공통 인프라에서 어떤 AI 모델을 사용하는지 일목요연하게 정리합니다.

| # | 서비스 | 대화 LLM | Vision | STT/TTS | Embedding | 특화 모델 |
|---|--------|----------|--------|---------|-----------|-----------|
| 01 | 마음이음 | ★ 70B (심층상담) | — | ★ Whisper | ● BGE-M3 | 감정 분류 |
| 02 | 그린바이트 | ● 8B | ★ YOLOv8 | — | ● BGE-M3 | 식재료 인식 |
| 03 | 시니어톡 | ★ 8B (방언) | — | ★ Whisper+VITS | ● BGE-M3 | 이상 징후 감지 |
| 04 | 룸메이트 | ● 8B | — | — | ★ KoSimCSE | 매칭 알고리즘 |
| 05 | 스터디브릿지 | ● 8B | — | — | ★ BGE-M3 | 학습 스타일 분류 |
| 06 | 동네알바 | ● 8B | — | — | ★ BGE-M3 | 스킬 매칭 |
| 07 | 펫커넥트 | ● 8B | ● YOLOv8 | — | ● BGE-M3 | 동물 행동 분석 |
| 08 | 리유즈잇 | ● 8B | ★ CLIP | — | ★ CLIP | 물품 상태 평가 |
| 09 | 탄소다이어리 | ● 8B | — | — | ● BGE-M3 | 탄소 계산 엔진 |
| 10 | 배리어프리고 | ● 8B | ★ YOLOv8 | ★ Whisper+VITS | ● BGE-M3 | 장애물 감지 |

> ★ = 핵심 의존, ● = 보조 활용

---

## 참고문헌

### LLM 모델

1. Dubey, A., et al. (2024). "The Llama 3 Herd of Models." *Meta AI*. https://arxiv.org/abs/2407.21783
2. Jiang, A. Q., et al. (2023). "Mistral 7B." *Mistral AI*. https://arxiv.org/abs/2310.06825
3. Yang, A., et al. (2024). "Qwen2.5 Technical Report." *Alibaba Cloud*. https://arxiv.org/abs/2412.15115
4. Kim, S., et al. (2024). "EXAONE 3.0 7.8B Instruction Tuned Language Model." *LG AI Research*. https://arxiv.org/abs/2412.04862

### 로컬 LLM 서빙

5. Ollama. (2024). "Ollama — Get up and running with large language models locally." https://ollama.com
6. Kwon, W., et al. (2023). "Efficient Memory Management for Large Language Model Serving with PagedAttention." *SOSP 2023*. https://arxiv.org/abs/2309.06180
7. Gerganov, G. (2024). "llama.cpp — LLM inference in C/C++." https://github.com/ggerganov/llama.cpp

### 양자화 및 Fine-tuning

8. Dettmers, T., et al. (2023). "QLoRA: Efficient Finetuning of Quantized Large Language Models." *NeurIPS 2023*. https://arxiv.org/abs/2305.14314
9. Hu, E. J., et al. (2021). "LoRA: Low-Rank Adaptation of Large Language Models." *ICLR 2022*. https://arxiv.org/abs/2106.09685
10. Han, D., et al. (2024). "Unsloth — Fine-tune LLMs 2-5x faster." https://github.com/unslothai/unsloth

### Vision 모델

11. Jocher, G., et al. (2023). "Ultralytics YOLOv8." https://github.com/ultralytics/ultralytics
12. Radford, A., et al. (2021). "Learning Transferable Visual Models From Natural Language Supervision (CLIP)." *ICML 2021*. https://arxiv.org/abs/2103.00020

### 음성 모델

13. Radford, A., et al. (2023). "Robust Speech Recognition via Large-Scale Weak Supervision (Whisper)." *ICML 2023*. https://arxiv.org/abs/2212.04356
14. Kim, J., et al. (2021). "Conditional Variational Autoencoder with Adversarial Learning for End-to-End Text-to-Speech (VITS)." *ICML 2021*. https://arxiv.org/abs/2106.06103

### 벡터 검색

15. pgvector Contributors. (2024). "pgvector — Open-source vector similarity search for Postgres." https://github.com/pgvector/pgvector
16. Chen, J., et al. (2024). "BGE M3-Embedding: Multi-Lingual, Multi-Functionality, Multi-Granularity Text Embeddings Through Self-Knowledge Distillation." https://arxiv.org/abs/2402.03216

### 보안 및 법률

17. 개인정보보호위원회. (2023). "개인정보 보호법 해설서." https://www.pipc.go.kr
18. 한국인터넷진흥원(KISA). (2024). "AI 서비스 개인정보보호 자율점검표." https://www.kisa.or.kr

---

> **본 문서는 프로젝트의 모든 창업 아이템에서 공통으로 참조합니다.**
> 각 서비스의 개별 기술 문서에서는 `00-로컬LLM-인프라-공통기술.md` 참조로 중복 설명을 생략합니다.
