# LLM(Large Language Model) 동작원리 종합 분석

> 본 문서는 주요 논문들을 근거로 LLM의 핵심 동작 원리를 수식, 코드와 함께 체계적으로 설명합니다.

---

## 목차

1. [전체 아키텍처 개요](#1-전체-아키텍처-개요)
2. [Tokenization (토큰화)](#2-tokenization-토큰화)
3. [Embedding & Positional Encoding](#3-embedding--positional-encoding)
4. [Transformer Architecture](#4-transformer-architecture)
5. [Self-Attention Mechanism](#5-self-attention-mechanism)
6. [Multi-Head Attention](#6-multi-head-attention)
7. [Feed-Forward Network & Layer Normalization](#7-feed-forward-network--layer-normalization)
8. [Pre-training (사전 학습)](#8-pre-training-사전-학습)
9. [Scaling Laws](#9-scaling-laws)
10. [Fine-tuning & RLHF](#10-fine-tuning--rlhf)
11. [Constitutional AI (Anthropic)](#11-constitutional-ai-anthropic)
12. [Inference & Generation](#12-inference--generation)
13. [주요 모델별 아키텍처 비교](#13-주요-모델별-아키텍처-비교)
14. [참고 논문 목록](#14-참고-논문-목록)

---

## 1. 전체 아키텍처 개요

LLM의 동작은 크게 **4단계**로 구분된다:

```
[Raw Text] → [Tokenization] → [Embedding] → [Transformer Layers × N] → [Output Probabilities]
```

| 단계 | 설명 | 핵심 논문 |
|------|------|-----------|
| Tokenization | 텍스트를 subword 단위로 분할 | Sennrich et al. (2016), Kudo (2018) |
| Embedding | 토큰을 고차원 벡터로 변환 | Vaswani et al. (2017) |
| Transformer | Self-Attention + FFN 반복 | Vaswani et al. (2017) |
| Pre-training | 대규모 코퍼스에서 Next Token Prediction | Radford et al. (2018), Brown et al. (2020) |
| Alignment | RLHF / Constitutional AI로 정렬 | Ouyang et al. (2022), Bai et al. (2022) |

---

## 2. Tokenization (토큰화)

### 2.1 Byte Pair Encoding (BPE)

**논문**: *Neural Machine Translation of Rare Words with Subword Units* (Sennrich et al., 2016)

BPE는 가장 빈번한 인접 바이트(또는 문자) 쌍을 반복적으로 병합하는 알고리즘이다.

**알고리즘**:
1. 초기 vocabulary = 모든 개별 문자(또는 바이트)
2. 코퍼스에서 가장 빈번한 인접 토큰 쌍을 찾음
3. 해당 쌍을 새로운 토큰으로 병합
4. 원하는 vocabulary 크기에 도달할 때까지 2-3 반복

```python
# BPE 알고리즘 구현 (simplified)
# Reference: Karpathy's minbpe (https://github.com/karpathy/minbpe)

def get_stats(ids):
    """인접 토큰 쌍의 빈도수를 계산"""
    counts = {}
    for pair in zip(ids, ids[1:]):
        counts[pair] = counts.get(pair, 0) + 1
    return counts

def merge(ids, pair, idx):
    """가장 빈번한 쌍을 새로운 토큰으로 병합"""
    new_ids = []
    i = 0
    while i < len(ids):
        if i < len(ids) - 1 and ids[i] == pair[0] and ids[i+1] == pair[1]:
            new_ids.append(idx)
            i += 2
        else:
            new_ids.append(ids[i])
            i += 1
    return new_ids

def train_bpe(text, vocab_size):
    """BPE 학습: vocab_size까지 병합 반복"""
    tokens = list(text.encode("utf-8"))  # 바이트 단위로 시작
    num_merges = vocab_size - 256  # 256개 바이트 토큰 기본
    merges = {}

    for i in range(num_merges):
        stats = get_stats(tokens)
        if not stats:
            break
        best_pair = max(stats, key=stats.get)
        idx = 256 + i
        tokens = merge(tokens, best_pair, idx)
        merges[best_pair] = idx

    return merges

# 예시
text = "the cat sat on the mat"
merges = train_bpe(text, vocab_size=280)
print(f"Learned {len(merges)} merge rules")
```

### 2.2 SentencePiece

**논문**: *SentencePiece: A simple and language independent subword tokenizer and detokenizer* (Kudo & Richardson, 2018)

SentencePiece는 raw text를 직접 처리하며, 언어에 독립적이다. `▁` (U+2581)로 word boundary를 표시한다.

```python
import sentencepiece as spm

# 학습
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='tokenizer',
    vocab_size=32000,
    model_type='bpe'  # 'unigram' 도 지원
)

# 사용
sp = spm.SentencePieceProcessor(model_file='tokenizer.model')
tokens = sp.encode("Hello, how are you?", out_type=str)
# ['▁Hello', ',', '▁how', '▁are', '▁you', '?']

ids = sp.encode("Hello, how are you?", out_type=int)
# [8774, 6, 579, 418, 340, 60]
```

| 모델 | Tokenizer | Vocab Size |
|------|-----------|------------|
| GPT-2 | Byte-level BPE | 50,257 |
| GPT-4 | tiktoken (BPE) | ~100,000 |
| LLaMA | SentencePiece (BPE) | 32,000 |
| PaLM | SentencePiece | 256,000 |
| Claude | SentencePiece 계열 | 비공개 |

---

## 3. Embedding & Positional Encoding

### 3.1 Token Embedding

각 토큰 ID를 `d_model` 차원의 dense vector로 매핑한다.

$$
\mathbf{e}_i = \mathbf{W}_E[x_i] \quad \text{where} \quad \mathbf{W}_E \in \mathbb{R}^{|V| \times d_{model}}
$$

- `|V|`: vocabulary 크기
- `d_model`: embedding 차원 (GPT-3: 12288, PaLM: 18432)

```python
import torch
import torch.nn as nn

class TokenEmbedding(nn.Module):
    def __init__(self, vocab_size, d_model):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, d_model)

    def forward(self, x):
        # x: (batch_size, seq_len) → (batch_size, seq_len, d_model)
        return self.embedding(x)
```

### 3.2 Positional Encoding

**논문**: *Attention Is All You Need* (Vaswani et al., 2017)

Transformer는 순서 정보가 없으므로 위치 정보를 주입해야 한다.

**Sinusoidal Positional Encoding** (원본 Transformer):

$$
PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

$$
PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

- `pos`: 시퀀스 내 위치 (0, 1, 2, ...)
- `i`: embedding 차원 인덱스
- `d_model`: embedding 차원

```python
import numpy as np

def sinusoidal_positional_encoding(max_len, d_model):
    """
    원본 Transformer의 Sinusoidal Positional Encoding
    (Vaswani et al., 2017)
    """
    pe = np.zeros((max_len, d_model))
    position = np.arange(0, max_len)[:, np.newaxis]       # (max_len, 1)
    div_term = 10000 ** (np.arange(0, d_model, 2) / d_model)  # (d_model/2,)

    pe[:, 0::2] = np.sin(position / div_term)  # 짝수 인덱스: sin
    pe[:, 1::2] = np.cos(position / div_term)  # 홀수 인덱스: cos

    return pe  # (max_len, d_model)
```

**RoPE (Rotary Position Embedding)** — 최신 LLM에서 주로 사용:

**논문**: *RoFormer: Enhanced Transformer with Rotary Position Embedding* (Su et al., 2021)

$$
f(\mathbf{q}, m) = \mathbf{R}_m \mathbf{q}, \quad \text{where} \quad \mathbf{R}_m = \begin{pmatrix} \cos m\theta_1 & -\sin m\theta_1 \\ \sin m\theta_1 & \cos m\theta_1 \\ & & \cos m\theta_2 & -\sin m\theta_2 \\ & & \sin m\theta_2 & \cos m\theta_2 \\ & & & & \ddots \end{pmatrix}
$$

```python
def rotary_embedding(x, seq_len, d_model):
    """
    RoPE: 위치 정보를 회전 행렬로 인코딩
    LLaMA, PaLM-2, Gemini 등에서 사용
    """
    theta = 10000 ** (-2 * torch.arange(0, d_model, 2).float() / d_model)
    positions = torch.arange(seq_len).float()
    angles = positions[:, None] * theta[None, :]  # (seq_len, d_model/2)

    cos_angles = torch.cos(angles)
    sin_angles = torch.sin(angles)

    # x를 2차원 쌍으로 분리하여 회전 적용
    x1, x2 = x[..., 0::2], x[..., 1::2]
    rotated = torch.cat([
        x1 * cos_angles - x2 * sin_angles,
        x1 * sin_angles + x2 * cos_angles
    ], dim=-1)

    return rotated
```

---

## 4. Transformer Architecture

**논문**: *Attention Is All You Need* (Vaswani et al., 2017, Google Brain)

현대 LLM은 원본 Transformer의 **Decoder-only** 변형을 사용한다.

### 4.1 전체 구조

```
Input Tokens
    ↓
[Token Embedding + Positional Encoding]
    ↓
┌─────────────────────────────────┐
│  Transformer Block × N layers   │
│  ┌───────────────────────────┐  │
│  │ Layer Norm                │  │
│  │ Masked Multi-Head Attention│  │
│  │ + Residual Connection     │  │
│  ├───────────────────────────┤  │
│  │ Layer Norm                │  │
│  │ Feed-Forward Network      │  │
│  │ + Residual Connection     │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
    ↓
[Layer Norm]
    ↓
[Linear → Vocabulary Logits]
    ↓
[Softmax → Probability Distribution]
```

```python
class TransformerBlock(nn.Module):
    """
    Decoder-only Transformer Block (Pre-Norm 변형)
    GPT-2 이후 대부분의 LLM이 Pre-Norm 사용
    """
    def __init__(self, d_model, n_heads, d_ff, dropout=0.1):
        super().__init__()
        self.ln1 = nn.LayerNorm(d_model)
        self.attn = MultiHeadAttention(d_model, n_heads)
        self.ln2 = nn.LayerNorm(d_model)
        self.ffn = FeedForward(d_model, d_ff)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, mask=None):
        # Pre-Norm + Residual Connection
        x = x + self.dropout(self.attn(self.ln1(x), mask=mask))
        x = x + self.dropout(self.ffn(self.ln2(x)))
        return x


class DecoderOnlyTransformer(nn.Module):
    """GPT-style Decoder-only LLM"""
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
        tok_emb = self.token_emb(idx)                          # (B, T, d_model)
        pos_emb = self.pos_emb(torch.arange(T, device=idx.device))  # (T, d_model)
        x = tok_emb + pos_emb                                  # (B, T, d_model)

        # Causal mask: 미래 토큰을 볼 수 없도록 마스킹
        mask = torch.triu(torch.ones(T, T), diagonal=1).bool()

        for block in self.blocks:
            x = block(x, mask=mask)

        x = self.ln_f(x)
        logits = self.head(x)  # (B, T, vocab_size)
        return logits
```

---

## 5. Self-Attention Mechanism

### 5.1 Scaled Dot-Product Attention

**논문**: Vaswani et al. (2017)

핵심 수식:

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
$$

각 구성 요소:
- **Q (Query)**: "내가 무엇을 찾고 있는가" — 현재 토큰이 다른 토큰에 대해 묻는 질문
- **K (Key)**: "나는 어떤 정보를 가지고 있는가" — 다른 토큰과의 매칭에 사용
- **V (Value)**: "내가 실제로 전달할 정보" — attention weight에 따라 가중 합산
- **√d_k**: scaling factor — dot product 값이 커지면 softmax gradient가 소실되므로 정규화

$$
Q = XW_Q, \quad K = XW_K, \quad V = XW_V
$$

$$
W_Q, W_K \in \mathbb{R}^{d_{model} \times d_k}, \quad W_V \in \mathbb{R}^{d_{model} \times d_v}
$$

### 5.2 왜 √d_k로 나누는가?

dot product의 분산이 `d_k`에 비례하여 커지므로:

$$
\text{Var}(q \cdot k) = d_k \cdot \text{Var}(q_i) \cdot \text{Var}(k_i) = d_k
$$

`√d_k`로 나누면 분산이 1로 정규화되어 softmax가 극단적 값을 갖지 않는다.

### 5.3 코드 구현

```python
import torch
import torch.nn.functional as F
import math

def scaled_dot_product_attention(Q, K, V, mask=None):
    """
    Scaled Dot-Product Attention
    (Vaswani et al., 2017, Eq. 1)

    Args:
        Q: Query  (batch, heads, seq_len, d_k)
        K: Key    (batch, heads, seq_len, d_k)
        V: Value  (batch, heads, seq_len, d_v)
        mask: optional causal mask

    Returns:
        output: (batch, heads, seq_len, d_v)
        attention_weights: (batch, heads, seq_len, seq_len)
    """
    d_k = Q.size(-1)

    # Step 1: QK^T 계산 — 토큰 간 유사도 점수
    scores = torch.matmul(Q, K.transpose(-2, -1))  # (B, H, T, T)

    # Step 2: √d_k로 스케일링 — gradient vanishing 방지
    scores = scores / math.sqrt(d_k)

    # Step 3: Causal Mask 적용 — 미래 토큰 차단 (autoregressive)
    if mask is not None:
        scores = scores.masked_fill(mask, float('-inf'))

    # Step 4: Softmax — 확률 분포로 변환
    attention_weights = F.softmax(scores, dim=-1)  # (B, H, T, T)

    # Step 5: V와 가중 합산 — 최종 출력
    output = torch.matmul(attention_weights, V)  # (B, H, T, d_v)

    return output, attention_weights
```

### 5.4 Attention의 직관적 이해

예시: "The **cat** sat on the **mat**" 문장에서 "sat"의 attention:

```
         The    cat    sat    on    the    mat
sat  [  0.05   0.35   0.10  0.05  0.05   0.40  ]
          ↑      ↑                          ↑
        낮음   높음(주어)                 높음(장소)
```

"sat"은 주어 "cat"과 장소 "mat"에 높은 attention weight를 부여한다.
이것이 Self-Attention이 **문맥을 이해**하는 핵심 메커니즘이다.

---

## 6. Multi-Head Attention

### 6.1 수식

**논문**: Vaswani et al. (2017)

$$
\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h) W^O
$$

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)
$$

- 각 head가 **서로 다른 관계 패턴**을 학습
  - Head 1: 문법적 관계 (주어-동사)
  - Head 2: 의미적 관계 (동의어, 반의어)
  - Head 3: 위치적 관계 (인접 토큰)
  - ...

### 6.2 코드 구현

```python
class MultiHeadAttention(nn.Module):
    """
    Multi-Head Attention (Vaswani et al., 2017)
    각 head가 독립적인 attention pattern을 학습
    """
    def __init__(self, d_model, n_heads):
        super().__init__()
        assert d_model % n_heads == 0
        self.d_k = d_model // n_heads
        self.n_heads = n_heads

        # Q, K, V projection 행렬
        self.W_q = nn.Linear(d_model, d_model, bias=False)
        self.W_k = nn.Linear(d_model, d_model, bias=False)
        self.W_v = nn.Linear(d_model, d_model, bias=False)
        # Output projection
        self.W_o = nn.Linear(d_model, d_model, bias=False)

    def forward(self, x, mask=None):
        B, T, C = x.shape

        # Linear projection → (B, T, d_model) → (B, T, n_heads, d_k)
        Q = self.W_q(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        # 결과: (B, n_heads, T, d_k)

        # Scaled Dot-Product Attention (각 head 독립적으로 수행)
        out, attn_weights = scaled_dot_product_attention(Q, K, V, mask)

        # head들을 다시 합침: (B, n_heads, T, d_k) → (B, T, d_model)
        out = out.transpose(1, 2).contiguous().view(B, T, C)

        # Output projection
        return self.W_o(out)
```

### 6.3 Grouped-Query Attention (GQA) & Multi-Query Attention (MQA)

**논문**: *GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints* (Ainslie et al., 2023, Google)

Inference 효율을 위해 K, V를 head 간 공유:

| 방식 | K,V 그룹 수 | 사용 모델 |
|------|------------|-----------|
| Multi-Head Attention (MHA) | = n_heads | GPT-3, 원본 Transformer |
| Multi-Query Attention (MQA) | 1 | PaLM, Gemini |
| Grouped-Query Attention (GQA) | n_heads / G | LLaMA-2 70B, Gemini |

```python
class GroupedQueryAttention(nn.Module):
    """
    GQA: K, V를 그룹 단위로 공유하여 KV Cache 메모리 절감
    (Ainslie et al., 2023)
    """
    def __init__(self, d_model, n_heads, n_kv_heads):
        super().__init__()
        self.n_heads = n_heads
        self.n_kv_heads = n_kv_heads  # K,V 그룹 수
        self.n_rep = n_heads // n_kv_heads  # 각 KV가 담당하는 Q head 수
        self.d_k = d_model // n_heads

        self.W_q = nn.Linear(d_model, n_heads * self.d_k, bias=False)
        self.W_k = nn.Linear(d_model, n_kv_heads * self.d_k, bias=False)
        self.W_v = nn.Linear(d_model, n_kv_heads * self.d_k, bias=False)
        self.W_o = nn.Linear(d_model, d_model, bias=False)

    def forward(self, x, mask=None):
        B, T, _ = x.shape
        Q = self.W_q(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(B, T, self.n_kv_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(B, T, self.n_kv_heads, self.d_k).transpose(1, 2)

        # K, V를 Q head 수에 맞게 반복 확장
        K = K.repeat_interleave(self.n_rep, dim=1)  # (B, n_heads, T, d_k)
        V = V.repeat_interleave(self.n_rep, dim=1)

        out, _ = scaled_dot_product_attention(Q, K, V, mask)
        out = out.transpose(1, 2).contiguous().view(B, T, -1)
        return self.W_o(out)
```

---

## 7. Feed-Forward Network & Layer Normalization

### 7.1 Position-wise Feed-Forward Network

$$
\text{FFN}(x) = \text{Activation}(xW_1 + b_1)W_2 + b_2
$$

원본 Transformer: ReLU 사용, 현대 LLM: **SwiGLU** 또는 **GELU** 사용

**SwiGLU** (PaLM, LLaMA에서 사용):

**논문**: *GLU Variants Improve Transformer* (Shazeer, 2020, Google)

$$
\text{SwiGLU}(x) = (\text{Swish}(xW_1)) \otimes (xW_3)
$$

$$
\text{Swish}(x) = x \cdot \sigma(x) = x \cdot \frac{1}{1 + e^{-x}}
$$

```python
class SwiGLUFeedForward(nn.Module):
    """
    SwiGLU FFN (Shazeer, 2020)
    LLaMA, PaLM 등에서 사용 — ReLU 대비 성능 향상
    """
    def __init__(self, d_model, d_ff):
        super().__init__()
        self.w1 = nn.Linear(d_model, d_ff, bias=False)
        self.w2 = nn.Linear(d_ff, d_model, bias=False)
        self.w3 = nn.Linear(d_model, d_ff, bias=False)  # gate

    def forward(self, x):
        # SwiGLU: swish(x @ W1) * (x @ W3) → @ W2
        return self.w2(F.silu(self.w1(x)) * self.w3(x))
```

### 7.2 RMSNorm (Root Mean Square Layer Normalization)

**논문**: *Root Mean Square Layer Normalization* (Zhang & Sennrich, 2019)

$$
\text{RMSNorm}(x) = \frac{x}{\text{RMS}(x)} \cdot \gamma, \quad \text{RMS}(x) = \sqrt{\frac{1}{n}\sum_{i=1}^{n}x_i^2}
$$

```python
class RMSNorm(nn.Module):
    """
    RMSNorm — LayerNorm보다 간단하고 빠름
    LLaMA, PaLM-2, Gemini 등에서 사용
    """
    def __init__(self, d_model, eps=1e-6):
        super().__init__()
        self.weight = nn.Parameter(torch.ones(d_model))
        self.eps = eps

    def forward(self, x):
        rms = torch.sqrt(torch.mean(x ** 2, dim=-1, keepdim=True) + self.eps)
        return x / rms * self.weight
```

---

## 8. Pre-training (사전 학습)

### 8.1 Next Token Prediction (Causal Language Modeling)

**논문**: *Language Models are Few-Shot Learners* (Brown et al., 2020, OpenAI — GPT-3)

LLM의 사전 학습 objective는 **다음 토큰 예측**이다:

$$
\mathcal{L}(\theta) = -\sum_{t=1}^{T} \log P(x_t | x_1, x_2, \dots, x_{t-1}; \theta)
$$

- 주어진 이전 토큰들로부터 다음 토큰의 확률을 최대화
- 이것이 LLM이 "언어를 이해"하는 방법의 본질

```python
def compute_loss(model, input_ids, targets):
    """
    Causal Language Modeling Loss
    Cross-entropy loss over next-token predictions
    """
    logits = model(input_ids)  # (B, T, vocab_size)

    # logits → probability distribution over vocabulary
    # targets: 실제 다음 토큰의 ID
    loss = F.cross_entropy(
        logits.view(-1, logits.size(-1)),  # (B*T, vocab_size)
        targets.view(-1),                   # (B*T,)
        ignore_index=-1  # padding 무시
    )
    return loss

# 학습 루프 (simplified)
optimizer = torch.optim.AdamW(model.parameters(), lr=3e-4, weight_decay=0.1)

for batch in dataloader:
    input_ids = batch[:, :-1]   # [x1, x2, ..., x_{T-1}]
    targets = batch[:, 1:]      # [x2, x3, ..., x_T]

    loss = compute_loss(model, input_ids, targets)
    loss.backward()
    torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
    optimizer.step()
    optimizer.zero_grad()
```

### 8.2 학습 데이터 구성

| 모델 | 학습 데이터 크기 | 토큰 수 | 데이터 소스 |
|------|----------------|---------|------------|
| GPT-3 | ~570GB | 300B tokens | CommonCrawl, WebText, Books, Wikipedia |
| PaLM | ~780B tokens | 780B | Web, Books, Wikipedia, Code, Conversations |
| LLaMA-2 | ~2T tokens | 2T | 공개 데이터 |
| Claude | 비공개 | 비공개 | 비공개 |

---

## 9. Scaling Laws

### 9.1 Neural Scaling Laws

**논문**: *Scaling Laws for Neural Language Models* (Kaplan et al., 2020, OpenAI)

모델 성능(loss)은 **파라미터 수(N)**, **데이터 크기(D)**, **연산량(C)**과 **power-law** 관계를 따른다:

$$
L(N) \approx \left(\frac{N_c}{N}\right)^{\alpha_N}, \quad \alpha_N \approx 0.076
$$

$$
L(D) \approx \left(\frac{D_c}{D}\right)^{\alpha_D}, \quad \alpha_D \approx 0.095
$$

$$
L(C) \approx \left(\frac{C_c}{C}\right)^{\alpha_C}, \quad \alpha_C \approx 0.050
$$

핵심 발견:
- 모델 크기를 **10배** 늘리면 loss가 약 **0.076 power**로 감소
- 데이터와 모델 크기를 **동시에** 스케일링해야 효율적
- 큰 모델이 **sample-efficient** — 적은 데이터로 더 많이 학습

### 9.2 Chinchilla Scaling Laws

**논문**: *Training Compute-Optimal Large Language Models* (Hoffmann et al., 2022, DeepMind)

Kaplan의 법칙을 수정 — 데이터를 더 많이 써야 최적:

$$
N_{opt} \propto C^{0.50}, \quad D_{opt} \propto C^{0.50}
$$

**Chinchilla 규칙**: 최적 학습을 위해 파라미터 수와 토큰 수의 비율이 약 **1:20**이어야 함

| 모델 | 파라미터 | 학습 토큰 | 비율 (토큰/파라미터) | Chinchilla 최적? |
|------|---------|----------|---------------------|-----------------|
| GPT-3 | 175B | 300B | 1.7x | Under-trained |
| Chinchilla | 70B | 1.4T | 20x | Optimal |
| LLaMA | 65B | 1.4T | 21.5x | Optimal |
| LLaMA-2 | 70B | 2T | 28.6x | Over-trained (의도적) |

```python
def estimate_chinchilla_optimal(compute_budget_flops):
    """
    Chinchilla 최적 모델 크기와 데이터 크기 추정
    (Hoffmann et al., 2022)

    Args:
        compute_budget_flops: 총 연산 예산 (FLOPs)

    Returns:
        optimal_params: 최적 파라미터 수
        optimal_tokens: 최적 학습 토큰 수
    """
    # C ≈ 6 * N * D (근사)
    # N_opt ∝ C^0.5, D_opt ∝ C^0.5
    # Chinchilla ratio: D ≈ 20 * N

    optimal_params = (compute_budget_flops / 120) ** 0.5
    optimal_tokens = 20 * optimal_params

    return optimal_params, optimal_tokens

# 예시: 10^24 FLOPs 예산
N_opt, D_opt = estimate_chinchilla_optimal(1e24)
print(f"최적 파라미터: {N_opt/1e9:.1f}B, 최적 토큰: {D_opt/1e12:.1f}T")
```

---

## 10. Fine-tuning & RLHF

### 10.1 Supervised Fine-Tuning (SFT)

**논문**: *Training language models to follow instructions with human feedback* (Ouyang et al., 2022, OpenAI — InstructGPT)

SFT는 사람이 작성한 고품질 (prompt, response) 쌍으로 모델을 추가 학습한다:

```python
# SFT 데이터 형식
sft_data = [
    {
        "prompt": "Explain quantum computing in simple terms.",
        "response": "Quantum computing uses quantum bits (qubits) that can be..."
    },
    # ... 수만 개의 예시
]

# SFT는 기본적으로 동일한 next-token prediction loss 사용
# 단, prompt 부분의 loss는 무시하고 response 부분만 학습
def sft_loss(model, prompt_ids, response_ids):
    full_ids = torch.cat([prompt_ids, response_ids], dim=1)
    logits = model(full_ids[:, :-1])

    # prompt 부분은 -1로 마스킹 (loss 계산에서 제외)
    labels = full_ids[:, 1:].clone()
    labels[:, :prompt_ids.size(1) - 1] = -1

    return F.cross_entropy(
        logits.view(-1, logits.size(-1)),
        labels.view(-1),
        ignore_index=-1
    )
```

### 10.2 Reward Model 학습

사람의 선호도 데이터로 Reward Model을 학습:

$$
\mathcal{L}_{RM}(\theta) = -\mathbb{E}_{(x, y_w, y_l) \sim D}\left[\log \sigma\left(r_\theta(x, y_w) - r_\theta(x, y_l)\right)\right]
$$

- `y_w`: 사람이 선호한 응답 (winner)
- `y_l`: 사람이 비선호한 응답 (loser)
- `r_θ`: reward model이 출력하는 scalar reward 점수
- `σ`: sigmoid 함수

```python
class RewardModel(nn.Module):
    """
    Reward Model (Ouyang et al., 2022)
    LLM 위에 scalar reward head를 추가
    """
    def __init__(self, base_model, d_model):
        super().__init__()
        self.base = base_model
        self.reward_head = nn.Linear(d_model, 1)  # scalar 출력

    def forward(self, input_ids):
        hidden = self.base(input_ids)          # (B, T, d_model)
        last_hidden = hidden[:, -1, :]         # 마지막 토큰의 hidden state
        reward = self.reward_head(last_hidden) # (B, 1)
        return reward.squeeze(-1)

def reward_model_loss(rm, prompt_ids, chosen_ids, rejected_ids):
    """
    Bradley-Terry model 기반 pairwise ranking loss
    """
    r_chosen = rm(torch.cat([prompt_ids, chosen_ids], dim=1))
    r_rejected = rm(torch.cat([prompt_ids, rejected_ids], dim=1))

    # 선호 응답의 reward가 비선호보다 높도록 학습
    loss = -torch.log(torch.sigmoid(r_chosen - r_rejected)).mean()
    return loss
```

### 10.3 PPO (Proximal Policy Optimization)를 이용한 RLHF

**논문**: *Proximal Policy Optimization Algorithms* (Schulman et al., 2017, OpenAI)

SFT 모델을 policy로, Reward Model의 점수를 보상으로 사용하여 강화학습:

$$
\mathcal{L}^{PPO}(\theta) = \mathbb{E}_t\left[\min\left(r_t(\theta)\hat{A}_t, \;\text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon)\hat{A}_t\right)\right]
$$

$$
r_t(\theta) = \frac{\pi_\theta(a_t | s_t)}{\pi_{\theta_{old}}(a_t | s_t)}
$$

- `r_t(θ)`: policy ratio — 새 정책과 이전 정책의 확률 비율
- `Â_t`: advantage estimate — reward baseline 대비 얼마나 좋은지
- `ε`: clipping parameter (보통 0.2) — policy 변화 폭 제한
- `clip`: policy가 급격히 변하지 않도록 보호

**InstructGPT의 RLHF objective** (KL penalty 포함):

$$
\text{objective}(\phi) = \mathbb{E}_{(x,y)\sim\pi_\phi}\left[r_\theta(x, y) - \beta \cdot \text{KL}\left(\pi_\phi(y|x) \| \pi_{SFT}(y|x)\right)\right]
$$

- `β`: KL penalty 계수 — SFT 모델에서 너무 멀어지지 않도록 제약

```python
def rlhf_training_step(policy, ref_policy, reward_model, prompt_ids, beta=0.01):
    """
    RLHF with PPO — simplified
    (Ouyang et al., 2022)
    """
    # 1. 현재 policy로 응답 생성
    with torch.no_grad():
        response_ids = policy.generate(prompt_ids)
        full_ids = torch.cat([prompt_ids, response_ids], dim=1)

    # 2. Reward Model로 점수 계산
    reward = reward_model(full_ids)

    # 3. KL divergence penalty 계산
    policy_logprobs = get_logprobs(policy, full_ids)
    ref_logprobs = get_logprobs(ref_policy, full_ids)
    kl_div = (policy_logprobs - ref_logprobs).sum(dim=-1)

    # 4. 최종 reward = RM reward - β * KL
    final_reward = reward - beta * kl_div

    # 5. PPO update
    advantages = compute_advantages(final_reward)
    ppo_loss = compute_ppo_loss(policy, full_ids, advantages, clip_epsilon=0.2)

    return ppo_loss


def compute_ppo_loss(policy, ids, advantages, clip_epsilon=0.2):
    """PPO Clipped Surrogate Objective"""
    new_logprobs = get_logprobs(policy, ids)
    old_logprobs = get_logprobs(policy, ids).detach()

    ratio = torch.exp(new_logprobs - old_logprobs)

    # Clipped objective — policy 변화 제한
    surr1 = ratio * advantages
    surr2 = torch.clamp(ratio, 1 - clip_epsilon, 1 + clip_epsilon) * advantages

    return -torch.min(surr1, surr2).mean()
```

### 10.4 DPO (Direct Preference Optimization)

**논문**: *Direct Preference Optimization: Your Language Model is Secretly a Reward Model* (Rafailov et al., 2023, Stanford)

Reward Model과 PPO 없이 직접 선호도 데이터로 학습:

$$
\mathcal{L}_{DPO}(\pi_\theta; \pi_{ref}) = -\mathbb{E}_{(x, y_w, y_l)}\left[\log \sigma\left(\beta \log\frac{\pi_\theta(y_w|x)}{\pi_{ref}(y_w|x)} - \beta \log\frac{\pi_\theta(y_l|x)}{\pi_{ref}(y_l|x)}\right)\right]
$$

```python
def dpo_loss(policy, ref_policy, prompt, chosen, rejected, beta=0.1):
    """
    DPO Loss (Rafailov et al., 2023)
    Reward Model 없이 직접 preference 학습
    """
    # 각 응답의 log probability 계산
    pi_chosen = get_logprobs(policy, torch.cat([prompt, chosen], 1)).sum(-1)
    pi_rejected = get_logprobs(policy, torch.cat([prompt, rejected], 1)).sum(-1)
    ref_chosen = get_logprobs(ref_policy, torch.cat([prompt, chosen], 1)).sum(-1)
    ref_rejected = get_logprobs(ref_policy, torch.cat([prompt, rejected], 1)).sum(-1)

    # Log-ratio 계산
    logits = beta * ((pi_chosen - ref_chosen) - (pi_rejected - ref_rejected))

    return -F.logsigmoid(logits).mean()
```

---

## 11. Constitutional AI (Anthropic)

**논문**: *Constitutional AI: Harmlessness from AI Feedback* (Bai et al., 2022, Anthropic)

Anthropic의 Claude는 RLHF를 넘어 **Constitutional AI (CAI)** 를 사용한다.

### 11.1 핵심 아이디어

사람 대신 **AI 자체가 원칙(Constitution)에 따라** 응답을 평가하고 개선 → **RLAIF (RL from AI Feedback)**

### 11.2 2단계 프로세스

**Phase 1: Supervised Self-Critique & Revision**

```
[초기 모델이 유해한 응답 생성]
        ↓
[AI가 헌법 원칙에 따라 자기 비판 (Critique)]
        ↓
[AI가 비판을 바탕으로 응답 수정 (Revision)]
        ↓
[수정된 응답으로 SFT]
```

```python
# Phase 1: Self-Critique & Revision (개념적 pseudocode)
constitution = [
    "Choose the response that is most helpful while being safe.",
    "Choose the response that is least likely to contain harmful content.",
    "Choose the response that most accurately follows the user's intent.",
]

def constitutional_critique_and_revise(model, prompt, initial_response):
    """CAI Phase 1: 자기 비판과 수정"""
    for principle in constitution:
        # AI가 원칙에 따라 비판
        critique_prompt = f"""
        Human: {prompt}
        Assistant: {initial_response}

        Critique Request: Identify ways in which the response
        violates the following principle: "{principle}"
        """
        critique = model.generate(critique_prompt)

        # AI가 비판을 바탕으로 수정
        revision_prompt = f"""
        {critique_prompt}
        Critique: {critique}

        Revision Request: Revise the response to address the critique
        while maintaining helpfulness.
        """
        initial_response = model.generate(revision_prompt)

    return initial_response  # 개선된 응답
```

**Phase 2: RLAIF (RL from AI Feedback)**

```python
def ai_preference_labeling(model, prompt, response_a, response_b, principles):
    """
    CAI Phase 2: AI가 원칙에 따라 선호도 레이블링
    사람 대신 AI가 어떤 응답이 더 좋은지 판단
    """
    eval_prompt = f"""
    Consider the following principle: "{principles[0]}"

    Human: {prompt}

    Response A: {response_a}
    Response B: {response_b}

    Which response better follows the principle? Answer A or B.
    """
    preference = model.generate(eval_prompt)

    # AI 선호도로 Reward Model 학습 → PPO로 최종 모델 학습
    return preference
```

### 11.3 CAI vs RLHF 비교

| 측면 | RLHF | Constitutional AI |
|------|------|-------------------|
| 피드백 소스 | 사람 annotator | AI + 원칙 |
| 확장성 | 사람 비용으로 제한 | AI로 대규모 생성 가능 |
| 투명성 | 암묵적 선호 | 명시적 원칙 |
| 편향 | 개별 annotator 편향 | 원칙 설계자 편향 |

---

## 12. Inference & Generation

### 12.1 Autoregressive Generation

LLM은 **한 번에 하나의 토큰**을 순차적으로 생성:

$$
P(y_1, y_2, \dots, y_T) = \prod_{t=1}^{T} P(y_t | y_1, \dots, y_{t-1})
$$

### 12.2 Sampling Strategies

#### Temperature Scaling

$$
P(x_i) = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}
$$

- `T < 1`: 더 확정적 (confident) — high probability 토큰에 집중
- `T = 1`: 원본 분포
- `T > 1`: 더 무작위적 — 분포가 균일해짐

#### Top-k Sampling

상위 k개 토큰만 후보로 유지:

$$
P'(x_i) = \begin{cases} \frac{P(x_i)}{\sum_{j \in \text{top-k}} P(x_j)} & \text{if } x_i \in \text{top-k} \\ 0 & \text{otherwise} \end{cases}
$$

#### Top-p (Nucleus) Sampling

**논문**: *The Curious Case of Neural Text Degeneration* (Holtzman et al., 2020)

누적 확률이 `p`를 넘을 때까지의 토큰만 후보:

$$
\text{Top-p}(P) = \min \left\{ V' \subseteq V : \sum_{x \in V'} P(x) \geq p \right\}
$$

```python
def generate(model, prompt_ids, max_tokens=100, temperature=0.7, top_p=0.9, top_k=50):
    """
    LLM Autoregressive Generation with sampling strategies
    """
    generated = prompt_ids.clone()

    for _ in range(max_tokens):
        # Forward pass — 마지막 토큰의 logits만 사용
        logits = model(generated)[:, -1, :]  # (B, vocab_size)

        # Temperature scaling
        logits = logits / temperature

        # Top-k filtering
        if top_k > 0:
            top_k_vals, _ = torch.topk(logits, top_k)
            threshold = top_k_vals[:, -1:]
            logits[logits < threshold] = float('-inf')

        # Top-p (nucleus) filtering
        if top_p < 1.0:
            sorted_logits, sorted_indices = torch.sort(logits, descending=True)
            cumulative_probs = torch.cumsum(F.softmax(sorted_logits, dim=-1), dim=-1)

            # p를 초과하는 토큰 제거
            sorted_mask = cumulative_probs - F.softmax(sorted_logits, dim=-1) >= top_p
            sorted_logits[sorted_mask] = float('-inf')

            # 원래 순서로 복원
            logits = sorted_logits.scatter(1, sorted_indices, sorted_logits)

        # 확률 분포에서 샘플링
        probs = F.softmax(logits, dim=-1)
        next_token = torch.multinomial(probs, num_samples=1)

        generated = torch.cat([generated, next_token], dim=1)

        # EOS 토큰이면 종료
        if next_token.item() == eos_token_id:
            break

    return generated


def greedy_decode(model, prompt_ids, max_tokens=100):
    """Greedy Decoding — 항상 가장 높은 확률의 토큰 선택 (temperature=0)"""
    generated = prompt_ids.clone()
    for _ in range(max_tokens):
        logits = model(generated)[:, -1, :]
        next_token = logits.argmax(dim=-1, keepdim=True)
        generated = torch.cat([generated, next_token], dim=1)
    return generated
```

### 12.3 KV Cache

Inference 최적화의 핵심 — 이전 토큰의 K, V를 캐싱하여 중복 계산 방지:

```python
def attention_with_kv_cache(Q, K_new, V_new, kv_cache=None):
    """
    KV Cache: 이전에 계산한 K, V를 재사용
    O(T²) → O(T) per token으로 개선
    """
    if kv_cache is not None:
        K_cached, V_cached = kv_cache
        K = torch.cat([K_cached, K_new], dim=2)  # 기존 + 새로운
        V = torch.cat([V_cached, V_new], dim=2)
    else:
        K, V = K_new, V_new

    # Attention 계산은 동일
    output, _ = scaled_dot_product_attention(Q, K, V)

    # 업데이트된 cache 반환
    return output, (K, V)
```

---

## 13. 주요 모델별 아키텍처 비교

| 특성 | GPT-3 | PaLM | LLaMA-2 | Claude | Gemini |
|------|-------|------|---------|--------|--------|
| **회사** | OpenAI | Google | Meta | Anthropic | Google DeepMind |
| **논문** | Brown et al. (2020) | Chowdhery et al. (2022) | Touvron et al. (2023) | 비공개 | Gemini Team (2023) |
| **파라미터** | 175B | 540B | 70B | 비공개 | 비공개 |
| **아키텍처** | Decoder-only | Decoder-only | Decoder-only | Decoder-only | Decoder-only |
| **Attention** | MHA | MQA | GQA | 비공개 | MQA |
| **Positional** | Learned | RoPE | RoPE | 비공개 | RoPE |
| **Normalization** | Pre-Norm LayerNorm | Pre-Norm LayerNorm | Pre-Norm RMSNorm | 비공개 | RMSNorm |
| **Activation** | GELU | SwiGLU | SwiGLU | 비공개 | GeGLU |
| **Context Length** | 2048 | 8192 | 4096 | 200K | 32K+ |
| **학습 토큰** | 300B | 780B | 2T | 비공개 | 비공개 |
| **Alignment** | RLHF (PPO) | RLHF | RLHF (PPO) | Constitutional AI | RLHF |

---

## 14. 참고 논문 목록

### 핵심 아키텍처
1. **Vaswani et al.** (2017). *Attention Is All You Need*. NeurIPS. — Transformer 아키텍처 제안
2. **Su et al.** (2021). *RoFormer: Enhanced Transformer with Rotary Position Embedding*. — RoPE

### OpenAI 계열
3. **Radford et al.** (2018). *Improving Language Understanding by Generative Pre-Training*. — GPT-1
4. **Radford et al.** (2019). *Language Models are Unsupervised Multitask Learners*. — GPT-2
5. **Brown et al.** (2020). *Language Models are Few-Shot Learners*. NeurIPS. — GPT-3
6. **Ouyang et al.** (2022). *Training language models to follow instructions with human feedback*. NeurIPS. — InstructGPT / RLHF

### Google / DeepMind 계열
7. **Chowdhery et al.** (2022). *PaLM: Scaling Language Modeling with Pathways*. — PaLM
8. **Anil et al.** (2023). *PaLM 2 Technical Report*. — PaLM-2
9. **Gemini Team** (2023). *Gemini: A Family of Highly Capable Multimodal Models*. — Gemini
10. **Hoffmann et al.** (2022). *Training Compute-Optimal Large Language Models*. — Chinchilla Scaling Laws
11. **Ainslie et al.** (2023). *GQA: Training Generalized Multi-Query Transformer Models*. — GQA

### Anthropic 계열
12. **Bai et al.** (2022). *Constitutional AI: Harmlessness from AI Feedback*. — Constitutional AI
13. **Bai et al.** (2022). *Training a Helpful and Harmless Assistant with RLHF*. — Anthropic RLHF

### Scaling Laws
14. **Kaplan et al.** (2020). *Scaling Laws for Neural Language Models*. — OpenAI Scaling Laws

### Alignment
15. **Schulman et al.** (2017). *Proximal Policy Optimization Algorithms*. — PPO
16. **Rafailov et al.** (2023). *Direct Preference Optimization*. NeurIPS. — DPO
17. **Holtzman et al.** (2020). *The Curious Case of Neural Text Degeneration*. ICLR. — Nucleus Sampling

### Tokenization
18. **Sennrich et al.** (2016). *Neural Machine Translation of Rare Words with Subword Units*. ACL. — BPE
19. **Kudo & Richardson** (2018). *SentencePiece: A simple and language independent subword tokenizer*. EMNLP. — SentencePiece

### 기타 핵심 구성요소
20. **Shazeer** (2020). *GLU Variants Improve Transformer*. — SwiGLU
21. **Zhang & Sennrich** (2019). *Root Mean Square Layer Normalization*. — RMSNorm

---

## 요약: LLM 동작 흐름도

```
┌──────────────────────────────────────────────────────────────────────┐
│                        LLM 전체 파이프라인                            │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. TOKENIZATION                                                     │
│     "Hello world" → [15496, 995]  (BPE / SentencePiece)             │
│                         ↓                                            │
│  2. EMBEDDING + POSITION                                             │
│     [15496, 995] → [[0.12, -0.34, ...], [0.56, 0.78, ...]]         │
│     + Positional Info (Sinusoidal / RoPE)                            │
│                         ↓                                            │
│  3. TRANSFORMER LAYERS × N                                           │
│     ┌─────────────────────────────────┐                              │
│     │  Multi-Head Self-Attention      │  ← Q, K, V로 문맥 파악      │
│     │  + Residual + LayerNorm         │                              │
│     ├─────────────────────────────────┤                              │
│     │  Feed-Forward Network (SwiGLU)  │  ← 비선형 변환              │
│     │  + Residual + LayerNorm         │                              │
│     └─────────────────────────────────┘                              │
│     (이 블록을 32~120회 반복)                                         │
│                         ↓                                            │
│  4. OUTPUT HEAD                                                      │
│     Hidden State → Linear → Logits → Softmax → P(next token)       │
│                         ↓                                            │
│  5. SAMPLING                                                         │
│     Temperature, Top-k, Top-p → 다음 토큰 선택                      │
│     → 1번으로 돌아가서 반복 (autoregressive)                          │
│                                                                      │
├──────────────────────────────────────────────────────────────────────┤
│  학습 과정:                                                          │
│  Pre-training (Next Token Prediction, 수조 토큰)                     │
│       ↓                                                              │
│  SFT (고품질 instruction-response 쌍)                                │
│       ↓                                                              │
│  RLHF / Constitutional AI / DPO (인간/AI 선호도 정렬)               │
└──────────────────────────────────────────────────────────────────────┘
```

---

> **Note**: 이 문서는 2024-2025년까지의 공개된 논문과 기술 보고서를 기반으로 작성되었습니다.
> Claude, GPT-4 등 최신 모델의 구체적 아키텍처는 비공개이며, 위 내용은 공개된 연구를 종합한 것입니다.
