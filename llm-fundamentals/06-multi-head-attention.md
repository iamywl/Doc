# 06. Multi-Head Attention

[← Self-Attention](05-self-attention.md) | [다음: FFN & Normalization →](07-ffn-normalization.md)

---

## 핵심 접근 (Key Insight)

> **"하나의 Attention으로는 부족하다 — 여러 관점에서 동시에 관계를 파악하라."**
>
> 단일 Attention head는 하나의 관계 패턴만 학습할 수 있다 (예: 주어-동사 관계).
> 하지만 자연어에는 **문법적, 의미적, 위치적 관계가 동시에** 존재한다.
> Multi-Head Attention은 입력을 h개의 독립적인 subspace로 투영하여
> **각 head가 서로 다른 유형의 관계**를 병렬로 학습하게 한다 (Vaswani et al., 2017).
>
> 이후 MQA(Shazeer, 2019)와 GQA(Ainslie et al., 2023)는
> **K, V를 head 간 공유**하여 inference 시 KV Cache 메모리를 대폭 절감하면서도
> 품질 저하를 최소화하는 실용적 개선을 제안했다.

---

## 6.1 Multi-Head Attention (MHA)

### 논문 인용

> Vaswani, A. et al. (2017). **Attention Is All You Need.** *NeurIPS 2017*, Section 3.2.2. https://arxiv.org/abs/1706.03762
>
> "Multi-head attention allows the model to jointly attend to information from
> different representation subspaces at different positions."

### 수식

$$
\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h) W^O
$$

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)
$$

$$
W_i^Q, W_i^K \in \mathbb{R}^{d_{model} \times d_k}, \quad W_i^V \in \mathbb{R}^{d_{model} \times d_v}, \quad W^O \in \mathbb{R}^{hd_v \times d_{model}}
$$

- 보통 `d_k = d_v = d_model / h`로 설정
- 각 head의 연산량이 줄어들어 **총 비용은 single-head full attention과 동일**

### 각 Head가 학습하는 패턴 (경험적 관찰)

| Head | 학습하는 관계 | 예시 |
|------|-------------|------|
| Head 1 | 문법적 관계 | 주어-동사, 수식어-피수식어 |
| Head 2 | 의미적 관계 | 동의어, 반의어, 상위어 |
| Head 3 | 인접 위치 | 바로 옆 토큰 |
| Head 4 | 장거리 참조 | 대명사-선행사 |
| ... | ... | ... |

> Clark, K., Khandelwal, U., Levy, O., & Manning, C. D. (2019). **What Does BERT Look At? An Analysis of BERT's Attention.** *BlackboxNLP Workshop at ACL 2019*. https://arxiv.org/abs/1906.04341

### 코드 구현

```python
import torch
import torch.nn as nn
import math

class MultiHeadAttention(nn.Module):
    """
    Multi-Head Attention (Vaswani et al., 2017)
    각 head가 독립적인 attention pattern을 학습
    """
    def __init__(self, d_model, n_heads):
        super().__init__()
        assert d_model % n_heads == 0, "d_model must be divisible by n_heads"
        self.d_k = d_model // n_heads
        self.n_heads = n_heads

        # Q, K, V projection — 하나의 큰 행렬로 모든 head를 한번에 계산
        self.W_q = nn.Linear(d_model, d_model, bias=False)
        self.W_k = nn.Linear(d_model, d_model, bias=False)
        self.W_v = nn.Linear(d_model, d_model, bias=False)
        # Output projection W^O
        self.W_o = nn.Linear(d_model, d_model, bias=False)

    def forward(self, x, mask=None):
        B, T, C = x.shape

        # (B, T, d_model) → (B, T, n_heads, d_k) → (B, n_heads, T, d_k)
        Q = self.W_q(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)

        # 각 head에서 독립적으로 Scaled Dot-Product Attention
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)
        if mask is not None:
            scores = scores.masked_fill(mask, float('-inf'))
        attn = torch.softmax(scores, dim=-1)
        out = torch.matmul(attn, V)  # (B, n_heads, T, d_k)

        # head들을 concat: (B, n_heads, T, d_k) → (B, T, d_model)
        out = out.transpose(1, 2).contiguous().view(B, T, C)

        # Output projection
        return self.W_o(out)
```

---

## 6.2 Multi-Query Attention (MQA)

### 논문 인용

> Shazeer, N. (2019). **Fast Transformer Decoding: One Write-Head is All You Need.** *arXiv preprint*. https://arxiv.org/abs/1911.02150

### 핵심 접근

> **"K와 V는 모든 head가 공유하고, Q만 head별로 다르게 하면
> KV Cache 메모리를 1/h로 줄일 수 있다."**
>
> Inference 시 KV Cache가 메모리 병목인데, MQA는 K, V를 단 1개의 head로 통일하여
> 메모리 사용량을 대폭 감소시킨다. 품질 저하는 미미하다.

| | Q heads | K heads | V heads | KV Cache 크기 |
|-|---------|---------|---------|--------------|
| **MHA** | h | h | h | 100% |
| **MQA** | h | **1** | **1** | **100/h %** |

---

## 6.3 Grouped-Query Attention (GQA)

### 논문 인용

> Ainslie, J., Lee-Thorp, J., de Jong, M., Zemlyanskiy, Y., Lebrón, F., & Vaswani, A. (2023). **GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints.** *EMNLP 2023*. https://arxiv.org/abs/2305.13245

### 핵심 접근

> **"MHA와 MQA의 중간 — K, V를 G개 그룹으로 공유한다."**
>
> MQA는 너무 극단적이어서 복잡한 추론 태스크에서 품질이 떨어질 수 있다.
> GQA는 K, V를 G개의 그룹으로 나누어 **MHA 수준의 품질을 유지하면서
> MQA에 가까운 메모리 효율**을 달성한다.

```
MHA (h=8):    Q₁K₁V₁  Q₂K₂V₂  Q₃K₃V₃  Q₄K₄V₄  Q₅K₅V₅  Q₆K₆V₆  Q₇K₇V₇  Q₈K₈V₈
              (8 KV pairs)

GQA (G=2):   Q₁Q₂Q₃Q₄ → K₁V₁    Q₅Q₆Q₇Q₈ → K₂V₂
              (2 KV pairs — 4x 절감)

MQA (G=1):   Q₁Q₂Q₃Q₄Q₅Q₆Q₇Q₈ → K₁V₁
              (1 KV pair — 8x 절감)
```

### 코드 구현

```python
class GroupedQueryAttention(nn.Module):
    """
    GQA: K, V를 그룹 단위로 공유
    (Ainslie et al., 2023)

    n_heads=32, n_kv_heads=8 → 각 KV가 4개의 Q head를 담당
    """
    def __init__(self, d_model, n_heads, n_kv_heads):
        super().__init__()
        self.n_heads = n_heads
        self.n_kv_heads = n_kv_heads
        self.n_rep = n_heads // n_kv_heads  # 각 KV가 담당하는 Q head 수
        self.d_k = d_model // n_heads

        self.W_q = nn.Linear(d_model, n_heads * self.d_k, bias=False)
        self.W_k = nn.Linear(d_model, n_kv_heads * self.d_k, bias=False)  # 축소
        self.W_v = nn.Linear(d_model, n_kv_heads * self.d_k, bias=False)  # 축소
        self.W_o = nn.Linear(d_model, d_model, bias=False)

    def forward(self, x, mask=None):
        B, T, _ = x.shape

        Q = self.W_q(x).view(B, T, self.n_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(B, T, self.n_kv_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(B, T, self.n_kv_heads, self.d_k).transpose(1, 2)

        # K, V를 Q head 수에 맞게 반복 확장
        # (B, n_kv_heads, T, d_k) → (B, n_heads, T, d_k)
        K = K.repeat_interleave(self.n_rep, dim=1)
        V = V.repeat_interleave(self.n_rep, dim=1)

        # 이후 동일한 attention 연산
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)
        if mask is not None:
            scores = scores.masked_fill(mask, float('-inf'))
        attn = torch.softmax(scores, dim=-1)
        out = torch.matmul(attn, V)

        out = out.transpose(1, 2).contiguous().view(B, T, -1)
        return self.W_o(out)
```

---

## 6.4 Attention 변형 비교 요약

| 방식 | K,V 그룹 수 | KV Cache | 품질 | 사용 모델 |
|------|------------|----------|------|-----------|
| **MHA** | = n_heads | 100% | 최고 | GPT-3, 원본 Transformer |
| **GQA** | n_heads / G | ~G/n_heads | MHA에 근접 | **LLaMA-2 70B**, Mistral |
| **MQA** | 1 | 1/n_heads | 약간 저하 | **PaLM**, Gemini, Falcon |

---

## 참고 문헌

- Vaswani, A. et al. (2017). Attention Is All You Need. *NeurIPS 2017*. https://arxiv.org/abs/1706.03762
- Shazeer, N. (2019). Fast Transformer Decoding: One Write-Head is All You Need. https://arxiv.org/abs/1911.02150
- Ainslie, J. et al. (2023). GQA: Training Generalized Multi-Query Transformer Models. *EMNLP 2023*. https://arxiv.org/abs/2305.13245
- Clark, K. et al. (2019). What Does BERT Look At? An Analysis of BERT's Attention. *BlackboxNLP at ACL 2019*. https://arxiv.org/abs/1906.04341
