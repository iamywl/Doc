# 04. Transformer Architecture

[← Embedding](03-embedding-positional-encoding.md) | [다음: Self-Attention →](05-self-attention.md)

---

## 핵심 접근 (Key Insight)

> **"Recurrence도 Convolution도 없이, Attention만으로 시퀀스를 모델링한다."**
>
> RNN은 시퀀스를 순차적으로 처리하므로 병렬화가 불가능하고,
> 긴 시퀀스에서 gradient vanishing/exploding 문제가 발생한다.
> CNN은 병렬화는 가능하지만, 장거리 의존성을 포착하려면 많은 레이어가 필요하다.
>
> Vaswani et al. (2017)의 핵심 통찰은:
> **Self-Attention 하나만으로 시퀀스 내 모든 위치 간의 관계를 O(1) 레이어에서 직접 계산**할 수 있다는 것이다.
> 이로써 학습 속도(병렬화)와 장거리 의존성을 동시에 해결했다.
>
> 원본 Transformer는 Encoder-Decoder 구조였으나, GPT(Radford et al., 2018) 이후
> LLM은 **Decoder-only** 구조가 표준이 되었다.

---

## 논문 인용

> Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). **Attention Is All You Need.** *Advances in Neural Information Processing Systems (NeurIPS)*, 30, pp. 5998-6008. https://arxiv.org/abs/1706.03762
>
> Radford, A., Narasimhan, K., Salimans, T., & Sutskever, I. (2018). **Improving Language Understanding by Generative Pre-Training.** *OpenAI Technical Report*. https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf

---

## 4.1 원본 Transformer vs Decoder-only

| 구조 | 원본 Transformer | Decoder-only (GPT, LLaMA, PaLM) |
|------|-----------------|----------------------------------|
| 구성 | Encoder + Decoder | Decoder만 사용 |
| Cross-Attention | 있음 | 없음 |
| Masking | Encoder: 양방향 / Decoder: causal | **항상 causal (미래 토큰 차단)** |
| 용도 | 번역 (seq2seq) | **언어 생성 (autoregressive)** |
| 사용 모델 | T5, BART | GPT, LLaMA, PaLM, Claude, Gemini |

---

## 4.2 Decoder-only 전체 구조

```
Input Token IDs: [x₁, x₂, ..., xₜ]
    ↓
┌─────────────────────────────────────┐
│  Token Embedding + Positional Info  │
│  (d_model 차원 벡터로 변환)          │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│  Transformer Block × N layers       │
│                                     │
│  ┌─────────────────────────────┐    │
│  │ 1. Layer Norm (Pre-Norm)    │    │
│  │ 2. Masked Multi-Head Attn   │    │
│  │ 3. + Residual Connection    │    │
│  ├─────────────────────────────┤    │
│  │ 4. Layer Norm               │    │
│  │ 5. Feed-Forward Network     │    │
│  │ 6. + Residual Connection    │    │
│  └─────────────────────────────┘    │
│                                     │
│  N = 32 (LLaMA-7B)                 │
│  N = 96 (GPT-3 175B)               │
│  N = 118 (PaLM 540B)               │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│  Final Layer Norm                   │
│  Linear Head → (vocab_size) logits  │
│  Softmax → P(next token)           │
└─────────────────────────────────────┘
```

### Pre-Norm vs Post-Norm

원본 Transformer는 **Post-Norm** (Attention → Add → LayerNorm) 이었으나,
GPT-2 이후 대부분의 LLM은 **Pre-Norm** (LayerNorm → Attention → Add)을 사용한다.

> Xiong, R., Yang, Y., He, J., Zheng, K., Zheng, S., Xing, C., ... & Liu, T. (2020). **On Layer Normalization in the Transformer Architecture.** *ICML 2020*. https://arxiv.org/abs/2002.04745
>
> Pre-Norm이 학습을 더 안정적으로 만들어 warmup 없이도 수렴 가능하다는 것을 이론적으로 증명.

---

## 4.3 코드 구현

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math


class TransformerBlock(nn.Module):
    """
    Decoder-only Transformer Block (Pre-Norm variant)
    - Pre-Norm: GPT-2 (Radford et al., 2019) 이후 표준
    - Residual Connection: He et al. (2016)의 ResNet에서 차용
    """
    def __init__(self, d_model, n_heads, d_ff, dropout=0.1):
        super().__init__()
        self.ln1 = nn.LayerNorm(d_model)
        self.attn = MultiHeadAttention(d_model, n_heads)
        self.ln2 = nn.LayerNorm(d_model)
        self.ffn = FeedForward(d_model, d_ff)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, mask=None):
        # Pre-Norm + Masked Self-Attention + Residual
        x = x + self.dropout(self.attn(self.ln1(x), mask=mask))
        # Pre-Norm + FFN + Residual
        x = x + self.dropout(self.ffn(self.ln2(x)))
        return x


class DecoderOnlyTransformer(nn.Module):
    """
    GPT-style Decoder-only LLM
    (Radford et al., 2018, 2019; Brown et al., 2020)
    """
    def __init__(self, vocab_size, d_model, n_layers, n_heads, d_ff, max_seq_len):
        super().__init__()
        self.token_emb = nn.Embedding(vocab_size, d_model)
        self.pos_emb = nn.Embedding(max_seq_len, d_model)  # learned positional
        self.blocks = nn.ModuleList([
            TransformerBlock(d_model, n_heads, d_ff)
            for _ in range(n_layers)
        ])
        self.ln_f = nn.LayerNorm(d_model)
        self.head = nn.Linear(d_model, vocab_size, bias=False)

    def forward(self, idx):
        B, T = idx.shape
        tok_emb = self.token_emb(idx)                               # (B, T, d_model)
        pos_emb = self.pos_emb(torch.arange(T, device=idx.device))  # (T, d_model)
        x = tok_emb + pos_emb                                       # (B, T, d_model)

        # Causal Mask: 미래 토큰을 볼 수 없도록 upper-triangular masking
        causal_mask = torch.triu(torch.ones(T, T, device=idx.device), diagonal=1).bool()

        for block in self.blocks:
            x = block(x, mask=causal_mask)

        x = self.ln_f(x)               # Final layer norm
        logits = self.head(x)           # (B, T, vocab_size)
        return logits
```

---

## 4.4 Residual Connection의 중요성

$$
\text{output} = x + \text{SubLayer}(x)
$$

> He, K., Zhang, X., Ren, S., & Sun, J. (2016). **Deep Residual Learning for Image Recognition.** *CVPR 2016*. https://arxiv.org/abs/1512.03385

Residual Connection이 없으면 96개 레이어를 쌓는 것이 불가능하다:
- **Gradient flow**: 역전파 시 gradient가 직접 전달되는 "highway" 역할
- **Identity mapping**: 레이어가 아무것도 학습하지 않으면 입력을 그대로 통과 → 최소한 성능이 나빠지지 않음

---

## 4.5 주요 모델별 하이퍼파라미터

| 모델 | n_layers | d_model | n_heads | d_ff | Parameters |
|------|----------|---------|---------|------|------------|
| GPT-2 | 48 | 1,600 | 25 | 6,400 | 1.5B |
| GPT-3 | 96 | 12,288 | 96 | 49,152 | 175B |
| PaLM | 118 | 18,432 | 48 | 73,728 | 540B |
| LLaMA-2 7B | 32 | 4,096 | 32 | 11,008 | 7B |
| LLaMA-2 70B | 80 | 8,192 | 64 | 28,672 | 70B |

**파라미터 수 근사 공식** (Decoder-only):

$$
P \approx 12 \cdot n_{layers} \cdot d_{model}^2
$$

---

## 참고 문헌

- Vaswani, A. et al. (2017). Attention Is All You Need. *NeurIPS 2017*. https://arxiv.org/abs/1706.03762
- Radford, A. et al. (2018). Improving Language Understanding by Generative Pre-Training. *OpenAI*.
- Radford, A. et al. (2019). Language Models are Unsupervised Multitask Learners. *OpenAI*.
- Brown, T. B. et al. (2020). Language Models are Few-Shot Learners. *NeurIPS 2020*. https://arxiv.org/abs/2005.14165
- He, K. et al. (2016). Deep Residual Learning for Image Recognition. *CVPR 2016*. https://arxiv.org/abs/1512.03385
- Xiong, R. et al. (2020). On Layer Normalization in the Transformer Architecture. *ICML 2020*. https://arxiv.org/abs/2002.04745
