# 13. 주요 모델별 아키텍처 비교

[← Inference & Generation](12-inference-generation.md) | [다음: References →](14-references.md)

---

## 핵심 접근 (Key Insight)

> **"모든 주요 LLM은 Decoder-only Transformer라는 동일한 뼈대를 공유하지만,
> 세부 설계 선택(attention, normalization, activation, position encoding)에서 차이가 난다."**
>
> GPT-3(2020)는 "크기를 키우면 된다"는 것을 증명했고,
> PaLM(2022)은 MQA와 대규모 학습 인프라(Pathways)를 도입했으며,
> LLaMA(2023)는 Chinchilla 법칙을 적용하여 작은 모델로도 높은 성능을 달성했다.
> Claude는 Constitutional AI라는 독자적 alignment 방법론을 구축했고,
> Gemini는 multimodal 능력을 Transformer에 통합했다.

---

## 전체 비교표

| 특성 | GPT-3 | PaLM | LLaMA-2 | Claude | Gemini |
|------|-------|------|---------|--------|--------|
| **회사** | OpenAI | Google | Meta | Anthropic | Google DeepMind |
| **발표** | 2020.05 | 2022.04 | 2023.07 | 비공개 | 2023.12 |
| **파라미터** | 175B | 540B | 7B / 13B / 70B | 비공개 | 비공개 |
| **아키텍처** | Decoder-only | Decoder-only | Decoder-only | Decoder-only | Decoder-only |
| **Attention** | MHA | **MQA** | **GQA** (70B) | 비공개 | **MQA** |
| **Position** | Learned | **RoPE** | **RoPE** | 비공개 | **RoPE** |
| **Normalization** | Pre-Norm LN | Pre-Norm LN | **Pre-Norm RMSNorm** | 비공개 | **RMSNorm** |
| **Activation** | GELU | **SwiGLU** | **SwiGLU** | 비공개 | **GeGLU** |
| **Context** | 2,048 | 8,192 | 4,096 | 200K | 32K+ |
| **학습 토큰** | 300B | 780B | **2T** | 비공개 | 비공개 |
| **Alignment** | RLHF (PPO) | RLHF | RLHF (PPO) | **Constitutional AI** | RLHF |
| **오픈소스** | No | No | **Yes** | No | No |

---

## 모델별 핵심 기여 (Key Contribution)

### GPT-3 (OpenAI, 2020)

> Brown, T. B. et al. (2020). **Language Models are Few-Shot Learners.** *NeurIPS 2020*. https://arxiv.org/abs/2005.14165

**핵심 기여**: **In-Context Learning의 발견**
- 175B 파라미터로 스케일링하면 few-shot prompting만으로 fine-tuning 없이 다양한 태스크 수행 가능
- "모델 크기가 곧 능력"이라는 패러다임 확립
- Scaling Laws(Kaplan et al., 2020)의 실증

### PaLM (Google, 2022)

> Chowdhery, A. et al. (2022). **PaLM: Scaling Language Modeling with Pathways.** *arXiv preprint*. https://arxiv.org/abs/2204.02311

**핵심 기여**: **효율적 대규모 학습 인프라**
- 540B 파라미터를 6144개의 TPU v4 chip에서 Pathways 시스템으로 학습
- Multi-Query Attention 채택으로 inference 효율 개선
- SwiGLU activation 도입
- Chain-of-thought prompting에서 breakthrough 수준의 추론 능력 시연

### LLaMA / LLaMA-2 (Meta, 2023)

> Touvron, H. et al. (2023a). **LLaMA: Open and Efficient Foundation Language Models.** https://arxiv.org/abs/2302.13971
>
> Touvron, H. et al. (2023b). **Llama 2: Open Foundation and Fine-Tuned Chat Models.** https://arxiv.org/abs/2307.09288

**핵심 기여**: **Chinchilla-optimal 오픈소스 모델**
- 65B 모델이 1.4T 토큰 학습 → Chinchilla 비율(~20x) 준수
- 13B LLaMA가 175B GPT-3와 대부분의 벤치마크에서 동등 이상
- RMSNorm + SwiGLU + RoPE + GQA 조합이 사실상 표준이 됨
- 오픈소스 LLM 생태계를 폭발적으로 성장시킴

### Claude (Anthropic)

> Bai, Y. et al. (2022). **Constitutional AI: Harmlessness from AI Feedback.** https://arxiv.org/abs/2212.08073

**핵심 기여**: **Constitutional AI — 원칙 기반 alignment**
- RLHF를 넘어 RLAIF(RL from AI Feedback) 도입
- 명시적 원칙(Constitution)으로 투명한 alignment
- Helpfulness와 Harmlessness의 균형에 초점
- 장문 context 처리 (200K tokens) 능력

### Gemini (Google DeepMind, 2023)

> Gemini Team (2023). **Gemini: A Family of Highly Capable Multimodal Models.** *arXiv preprint*. https://arxiv.org/abs/2312.11805

**핵심 기여**: **Native Multimodal**
- 텍스트, 이미지, 오디오, 비디오를 단일 모델에서 처리
- 32K context length (Gemini 1.0), 이후 1M+ (Gemini 1.5)
- MQA + RoPE + RMSNorm 등 PaLM의 효율적 설계 계승

---

## 아키텍처 진화 타임라인

```
2017  Transformer (Vaswani et al.)
       ↓
2018  GPT-1 (Radford et al.) — Decoder-only pre-training
       ↓
2019  GPT-2 — Pre-Norm, larger scale
       ↓
2020  GPT-3 — 175B, in-context learning
       ↓
2022  PaLM — MQA, SwiGLU, 540B on TPU v4
       ├── Chinchilla — Compute-optimal scaling
       └── InstructGPT — RLHF
            ├── Constitutional AI (Anthropic)
            ↓
2023  LLaMA — RMSNorm + SwiGLU + RoPE + GQA (오픈소스)
       ├── LLaMA-2 — 2T tokens, GQA
       ├── Claude 2 — Constitutional AI + long context
       ├── Gemini — Native multimodal
       └── Mistral — Sliding Window Attention
            ↓
2024+ Claude 3/3.5, GPT-4o, Gemini 1.5/2.0, LLaMA-3, ...
```

---

## 현대 LLM의 "표준 아키텍처" (2023-2024)

LLaMA가 확립한 조합이 사실상 업계 표준:

| 구성요소 | 표준 선택 | 이전 선택 |
|---------|----------|----------|
| Architecture | Decoder-only | Encoder-Decoder |
| Attention | **GQA** (또는 MQA) | MHA |
| Position | **RoPE** | Learned / Sinusoidal |
| Normalization | **Pre-Norm RMSNorm** | Post-Norm LayerNorm |
| Activation | **SwiGLU** | ReLU / GELU |
| Tokenizer | **SentencePiece (BPE)** | BPE |
| Alignment | **RLHF / DPO / CAI** | — |

---

## 참고 문헌

- Brown, T. B. et al. (2020). Language Models are Few-Shot Learners. *NeurIPS 2020*. https://arxiv.org/abs/2005.14165
- Chowdhery, A. et al. (2022). PaLM: Scaling Language Modeling with Pathways. https://arxiv.org/abs/2204.02311
- Touvron, H. et al. (2023a). LLaMA. https://arxiv.org/abs/2302.13971
- Touvron, H. et al. (2023b). Llama 2. https://arxiv.org/abs/2307.09288
- Bai, Y. et al. (2022). Constitutional AI. https://arxiv.org/abs/2212.08073
- Gemini Team (2023). Gemini. https://arxiv.org/abs/2312.11805
