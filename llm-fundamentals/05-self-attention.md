# 05. Self-Attention Mechanism

[← Transformer Architecture](04-transformer-architecture.md) | [다음: Multi-Head Attention →](06-multi-head-attention.md)

---

## 핵심 접근 (Key Insight)

> **"시퀀스의 모든 위치가 다른 모든 위치를 직접 참조할 수 있게 하라."**
>
> RNN에서는 위치 1의 정보가 위치 100에 도달하려면 99번의 sequential step을 거쳐야 한다.
> CNN에서는 receptive field를 넓히기 위해 많은 레이어를 쌓아야 한다.
>
> Self-Attention은 **단 한 번의 연산으로 모든 위치 쌍 간의 관계를 계산**한다.
> 이것이 Vaswani et al. (2017)의 논문 제목 "Attention Is **All** You Need"의 의미이다.
>
> **Q(Query)-K(Key)-V(Value) 분리**는 정보 검색의 비유에서 왔다:
> - Query: "내가 찾고 싶은 것" (검색 쿼리)
> - Key: "각 항목의 인덱스" (검색 키)
> - Value: "실제 저장된 정보" (검색 결과)
>
> Q와 K의 dot product로 "관련성(relevance)"을 계산하고,
> 그 관련성에 비례하여 V를 가중 합산한다.

---

## 논문 인용

> Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). **Attention Is All You Need.** *Advances in Neural Information Processing Systems (NeurIPS)*, 30, pp. 5998-6008. Section 3.2.1 "Scaled Dot-Product Attention". https://arxiv.org/abs/1706.03762
>
> 선행 연구: Bahdanau, D., Cho, K., & Bengio, Y. (2015). **Neural Machine Translation by Jointly Learning to Align and Translate.** *ICLR 2015*. https://arxiv.org/abs/1409.0473 — Attention 메커니즘의 최초 제안 (additive attention)

---

## 5.1 Scaled Dot-Product Attention

### 수식

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
$$

각 구성 요소:

$$
Q = XW_Q, \quad K = XW_K, \quad V = XW_V
$$

$$
W_Q, W_K \in \mathbb{R}^{d_{model} \times d_k}, \quad W_V \in \mathbb{R}^{d_{model} \times d_v}
$$

| 기호 | 의미 | 직관적 설명 |
|------|------|------------|
| **Q** (Query) | 질문 벡터 | "이 토큰이 다른 토큰에게 묻는 것" |
| **K** (Key) | 키 벡터 | "이 토큰이 자신을 설명하는 방식" |
| **V** (Value) | 값 벡터 | "이 토큰이 실제로 전달하는 정보" |
| **QK^T** | 유사도 행렬 | "모든 토큰 쌍 간의 관련성 점수" |
| **√d_k** | 스케일링 | "dot product 크기를 정규화" |
| **softmax** | 정규화 | "점수를 확률 분포로 변환" |

---

## 5.2 왜 √d_k로 나누는가?

### 수학적 근거

Q와 K의 각 성분이 평균 0, 분산 1인 독립 확률변수일 때:

$$
q \cdot k = \sum_{i=1}^{d_k} q_i \cdot k_i
$$

$$
\mathbb{E}[q \cdot k] = 0, \quad \text{Var}(q \cdot k) = d_k
$$

`d_k`가 커질수록 dot product의 **분산이 커져서** softmax 입력이 극단적 값을 가지게 되고,
이는 **softmax가 거의 one-hot 분포**가 되어 gradient가 소실된다.

`√d_k`로 나누면:

$$
\text{Var}\left(\frac{q \cdot k}{\sqrt{d_k}}\right) = \frac{d_k}{d_k} = 1
$$

> "We suspect that for large values of d_k, the dot products grow large in magnitude,
> pushing the softmax function into regions where it has extremely small gradients."
> — Vaswani et al. (2017), Section 3.2.1

---

## 5.3 Causal Masking (Autoregressive)

LLM(Decoder-only)에서는 미래 토큰을 볼 수 없도록 **causal mask**를 적용:

```
Attention Score Matrix (4 tokens):

         t₁    t₂    t₃    t₄
t₁  [  0.8   -∞    -∞    -∞  ]   ← t₁은 자신만 참조
t₂  [  0.3   0.9   -∞    -∞  ]   ← t₂는 t₁, t₂만 참조
t₃  [  0.1   0.4   0.7   -∞  ]   ← t₃는 t₁, t₂, t₃만 참조
t₄  [  0.2   0.5   0.3   0.6 ]   ← t₄는 모든 토큰 참조
```

`-∞` 위치는 softmax 후 0이 되어 해당 토큰의 정보가 완전히 차단된다.

---

## 5.4 코드 구현

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
        mask: boolean causal mask, True = 차단

    Returns:
        output: (batch, heads, seq_len, d_v)
        attention_weights: (batch, heads, seq_len, seq_len)
    """
    d_k = Q.size(-1)

    # Step 1: QK^T — 모든 토큰 쌍 간의 유사도 점수
    # 행렬 곱: (B, H, T, d_k) × (B, H, d_k, T) → (B, H, T, T)
    scores = torch.matmul(Q, K.transpose(-2, -1))

    # Step 2: √d_k로 스케일링 — 분산 정규화
    scores = scores / math.sqrt(d_k)

    # Step 3: Causal Mask — 미래 토큰에 -inf 부여
    if mask is not None:
        scores = scores.masked_fill(mask, float('-inf'))

    # Step 4: Softmax — 각 행을 확률 분포로 변환
    # -inf → exp(-inf) = 0 → 미래 토큰의 기여가 0
    attention_weights = F.softmax(scores, dim=-1)

    # Step 5: 가중 합산 — V를 attention weights로 조합
    # (B, H, T, T) × (B, H, T, d_v) → (B, H, T, d_v)
    output = torch.matmul(attention_weights, V)

    return output, attention_weights
```

---

## 5.5 Attention의 직관적 이해

### 예시: "The cat sat on the mat"

"sat"(위치 3) 토큰이 다른 토큰들에 주는 attention weight:

```
            The    cat    sat    on    the    mat
sat   [    0.05   0.35   0.10  0.05  0.05   0.40  ]
             ↑      ↑                          ↑
           낮음   높음                        높음
                  (주어)                      (장소)
```

- **"cat"에 높은 attention** → "sat"의 주어가 누구인지 파악
- **"mat"에 높은 attention** → "sat"의 장소/목적어 파악
- 이것이 Transformer가 **문법적, 의미적 관계를 학습**하는 방식

### 시각적 비유

```
기존 RNN:   The → cat → sat → on → the → mat  (순차 전달, 정보 손실)

Attention:  sat ──→ The  (0.05)
            sat ──→ cat  (0.35)  ★
            sat ──→ on   (0.05)
            sat ──→ the  (0.05)
            sat ──→ mat  (0.40)  ★
            (모든 토큰에 직접 접근, 관련성에 따라 가중치)
```

---

## 5.6 Computational Complexity

| 연산 | Self-Attention | RNN | CNN |
|------|---------------|-----|-----|
| 레이어당 복잡도 | O(T² · d) | O(T · d²) | O(T · k · d²) |
| 순차 연산 수 | O(1) | **O(T)** | O(log_k T) |
| 최대 경로 길이 | **O(1)** | O(T) | O(log_k T) |

- **T**: 시퀀스 길이, **d**: 차원, **k**: 커널 크기
- Self-Attention의 O(T²)는 긴 시퀀스에서 병목 → **Flash Attention** 등의 최적화 연구로 이어짐

> Dao, T., Fu, D. Y., Ermon, S., Rudra, A., & Ré, C. (2022). **FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness.** *NeurIPS 2022*. https://arxiv.org/abs/2205.14135

---

## 참고 문헌

- Bahdanau, D. et al. (2015). Neural Machine Translation by Jointly Learning to Align and Translate. *ICLR 2015*. https://arxiv.org/abs/1409.0473
- Vaswani, A. et al. (2017). Attention Is All You Need. *NeurIPS 2017*. https://arxiv.org/abs/1706.03762
- Dao, T. et al. (2022). FlashAttention: Fast and Memory-Efficient Exact Attention. *NeurIPS 2022*. https://arxiv.org/abs/2205.14135
