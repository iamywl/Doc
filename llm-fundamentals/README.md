# LLM(Large Language Model) 동작원리 종합 분석

> 본 문서는 주요 논문들을 근거로 LLM의 핵심 동작 원리를 수식, 코드와 함께 체계적으로 설명합니다.
> 전문용어는 영문을 우선하여 표기하며, 모든 내용은 논문의 정식 인용과 함께 제공됩니다.

---

## 문서 구조

```
llm-fundamentals/
├── README.md                          ← 현재 파일 (목차 + 전체 흐름도)
├── 01-overview.md                     ← 전체 아키텍처 개요
├── 02-tokenization.md                 ← BPE, SentencePiece
├── 03-embedding-positional-encoding.md← Token Embedding, Sinusoidal PE, RoPE
├── 04-transformer-architecture.md     ← Decoder-only Transformer 구조
├── 05-self-attention.md               ← Scaled Dot-Product Attention (Q, K, V)
├── 06-multi-head-attention.md         ← MHA, MQA, GQA
├── 07-ffn-normalization.md            ← SwiGLU, RMSNorm
├── 08-pretraining.md                  ← Next Token Prediction, 학습 데이터
├── 09-scaling-laws.md                 ← Kaplan Scaling Laws, Chinchilla
├── 10-rlhf-alignment.md              ← SFT, Reward Model, PPO, DPO
├── 11-constitutional-ai.md           ← Anthropic CAI, RLAIF
├── 12-inference-generation.md         ← Sampling, KV Cache
├── 13-model-comparison.md            ← GPT / PaLM / LLaMA / Claude / Gemini 비교
└── 14-references.md                   ← 전체 논문 인용 목록 (정식 서지 형식)
```

---

## 전체 파이프라인 흐름도

```
┌──────────────────────────────────────────────────────────────────────┐
│                        LLM 전체 파이프라인                            │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. TOKENIZATION       (→ 02-tokenization.md)                        │
│     "Hello world" → [15496, 995]  (BPE / SentencePiece)             │
│                         ↓                                            │
│  2. EMBEDDING + POSITION (→ 03-embedding-positional-encoding.md)     │
│     [15496, 995] → [[0.12, -0.34, ...], [0.56, 0.78, ...]]         │
│     + Positional Info (Sinusoidal / RoPE)                            │
│                         ↓                                            │
│  3. TRANSFORMER LAYERS × N  (→ 04, 05, 06, 07)                      │
│     ┌─────────────────────────────────┐                              │
│     │  Multi-Head Self-Attention      │  ← Q, K, V로 문맥 파악      │
│     │  + Residual + LayerNorm         │                              │
│     ├─────────────────────────────────┤                              │
│     │  Feed-Forward Network (SwiGLU)  │  ← 비선형 변환              │
│     │  + Residual + LayerNorm         │                              │
│     └─────────────────────────────────┘                              │
│     (이 블록을 32~120회 반복)                                         │
│                         ↓                                            │
│  4. OUTPUT HEAD          (→ 12-inference-generation.md)               │
│     Hidden State → Linear → Logits → Softmax → P(next token)       │
│                         ↓                                            │
│  5. SAMPLING                                                         │
│     Temperature, Top-k, Top-p → 다음 토큰 선택                      │
│     → 1번으로 돌아가서 반복 (autoregressive)                          │
│                                                                      │
├──────────────────────────────────────────────────────────────────────┤
│  학습 과정:                                                          │
│  Pre-training (→ 08) → SFT (→ 10) → RLHF/CAI/DPO (→ 10, 11)       │
│                                                                      │
│  Scaling Laws (→ 09): 모델 크기, 데이터, 연산량의 최적 비율          │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 빠른 탐색 가이드

| 궁금한 주제 | 파일 |
|-------------|------|
| LLM이 텍스트를 어떻게 읽는가? | `02-tokenization.md` |
| Attention이 정확히 뭔가? | `05-self-attention.md` |
| GPT와 LLaMA의 차이는? | `13-model-comparison.md` |
| RLHF가 어떻게 작동하는가? | `10-rlhf-alignment.md` |
| Claude는 뭐가 다른가? | `11-constitutional-ai.md` |
| 모델을 키우면 왜 좋아지는가? | `09-scaling-laws.md` |
| 전체 논문 목록 | `14-references.md` |

---

> **Note**: 이 문서는 2024-2025년까지의 공개된 논문과 기술 보고서를 기반으로 작성되었습니다.
> Claude, GPT-4 등 최신 모델의 구체적 아키텍처는 비공개이며, 위 내용은 공개된 연구를 종합한 것입니다.
