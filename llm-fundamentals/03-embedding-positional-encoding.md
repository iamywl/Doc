# 03. Embedding & Positional Encoding

[← Tokenization](02-tokenization.md) | [다음: Transformer Architecture →](04-transformer-architecture.md)

---

## 핵심 접근 (Key Insight)

> **"이산적 토큰을 연속 벡터 공간에 매핑하고, 순서 정보를 주입한다."**
>
> Transformer는 RNN과 달리 입력을 순차적으로 처리하지 않으므로
> 토큰의 **위치 정보가 아키텍처 자체에 내재되어 있지 않다**.
> Vaswani et al. (2017)은 sinusoidal 함수로 위치를 인코딩했고,
> 이후 Su et al. (2021)의 **RoPE**는 위치 정보를 attention 연산 자체에 녹여넣어
> **상대적 위치 관계**를 자연스럽게 포착하면서도 context length 확장이 용이한 방법을 제안했다.
> 현재 LLaMA, PaLM-2, Gemini 등 대부분의 최신 LLM이 RoPE를 채택하고 있다.

---

## 3.1 Token Embedding

### 논문 인용

> Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). **Attention Is All You Need.** *Advances in Neural Information Processing Systems (NeurIPS)*, 30, pp. 5998-6008. https://arxiv.org/abs/1706.03762

### 수식

각 토큰 ID `x_i`를 `d_model` 차원의 dense vector로 매핑한다:

$$
\mathbf{e}_i = \mathbf{W}_E[x_i] \quad \text{where} \quad \mathbf{W}_E \in \mathbb{R}^{|V| \times d_{model}}
$$

- `|V|`: vocabulary 크기
- `d_model`: embedding 차원

| 모델 | d_model | |V| |
|------|---------|-----|
| GPT-2 | 1,600 | 50,257 |
| GPT-3 (175B) | 12,288 | 50,257 |
| PaLM (540B) | 18,432 | 256,000 |
| LLaMA-2 (70B) | 8,192 | 32,000 |

### 코드

```python
import torch
import torch.nn as nn

class TokenEmbedding(nn.Module):
    def __init__(self, vocab_size, d_model):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, d_model)
        # W_E는 (vocab_size, d_model) 크기의 학습 가능한 행렬
        # 각 행이 하나의 토큰 벡터

    def forward(self, x):
        # x: (batch_size, seq_len) → (batch_size, seq_len, d_model)
        return self.embedding(x)
```

---

## 3.2 Sinusoidal Positional Encoding (원본 Transformer)

### 논문 인용

> Vaswani et al. (2017), Section 3.5. "Positional Encoding"

### 핵심 접근

> 서로 다른 주파수의 sin/cos 함수를 조합하면, 각 위치마다 **고유한 벡터**가 생성되고,
> 인접 위치 간의 **상대적 거리도 일정한 선형 변환**으로 표현된다.
> 이는 학습 없이도 임의의 시퀀스 길이에 일반화할 수 있다는 장점이 있다.

### 수식

$$
PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

$$
PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

- `pos`: 시퀀스 내 위치 (0, 1, 2, ...)
- `i`: embedding 차원 인덱스 (0, 1, ..., d_model/2 - 1)
- `10000`: base frequency (논문에서 실험적으로 선택)

**왜 sin/cos인가?** — 저자들의 직관:

> "We hypothesized it would allow the model to easily learn to attend by relative positions,
> since for any fixed offset k, PE(pos+k) can be represented as a linear function of PE(pos)."
> — Vaswani et al. (2017), Section 3.5

### 코드

```python
import numpy as np

def sinusoidal_positional_encoding(max_len, d_model):
    """
    원본 Transformer의 Sinusoidal Positional Encoding
    (Vaswani et al., 2017, Section 3.5)
    """
    pe = np.zeros((max_len, d_model))
    position = np.arange(0, max_len)[:, np.newaxis]            # (max_len, 1)
    div_term = 10000 ** (np.arange(0, d_model, 2) / d_model)  # (d_model/2,)

    pe[:, 0::2] = np.sin(position / div_term)  # 짝수 인덱스: sin
    pe[:, 1::2] = np.cos(position / div_term)  # 홀수 인덱스: cos

    return pe  # (max_len, d_model)

# 최종 입력 = Token Embedding + Positional Encoding
# x = TokenEmbedding(tokens) + PE[:seq_len, :]
```

---

## 3.3 RoPE (Rotary Position Embedding)

### 논문 인용

> Su, J., Lu, Y., Pan, S., Murtadha, A., Wen, B., & Liu, Y. (2021). **RoFormer: Enhanced Transformer with Rotary Position Embedding.** *arXiv preprint*. https://arxiv.org/abs/2104.09864

### 핵심 접근

> **"위치 정보를 벡터에 더하는 것이 아니라, 벡터를 회전시킨다."**
>
> Sinusoidal PE는 embedding에 **덧셈**으로 위치를 주입하지만,
> RoPE는 Q, K 벡터를 position에 비례하는 각도로 **회전**시킨다.
> 이렇게 하면 두 토큰 간의 dot product가 자연스럽게 **상대적 위치의 함수**가 되어,
> 절대 위치와 상대 위치 정보를 동시에 인코딩할 수 있다.
>
> 핵심 성질: `⟨f(q, m), f(k, n)⟩ = g(q, k, m-n)` — dot product가 상대 거리 `m-n`에만 의존

### 수식

embedding 벡터를 2차원 쌍으로 묶어 각각 회전 행렬을 적용:

$$
f(\mathbf{q}, m) = \mathbf{R}_m \mathbf{q}
$$

$$
\mathbf{R}_m = \begin{pmatrix}
\cos m\theta_1 & -\sin m\theta_1 & & \\
\sin m\theta_1 & \cos m\theta_1 & & \\
& & \cos m\theta_2 & -\sin m\theta_2 \\
& & \sin m\theta_2 & \cos m\theta_2 \\
& & & & \ddots
\end{pmatrix}
$$

$$
\theta_i = 10000^{-2i/d_{model}}
$$

### 코드

```python
import torch

def precompute_rope_frequencies(d_model, max_seq_len, base=10000.0):
    """
    RoPE 주파수 사전 계산
    (Su et al., 2021)
    """
    # θ_i = 10000^(-2i/d) for i = 0, 1, ..., d/2 - 1
    theta = 1.0 / (base ** (torch.arange(0, d_model, 2).float() / d_model))

    # 각 위치에 대한 각도: m * θ_i
    positions = torch.arange(max_seq_len).float()
    angles = torch.outer(positions, theta)  # (max_seq_len, d_model/2)

    # cos, sin 사전 계산
    cos = torch.cos(angles)  # (max_seq_len, d_model/2)
    sin = torch.sin(angles)  # (max_seq_len, d_model/2)
    return cos, sin


def apply_rope(x, cos, sin):
    """
    RoPE 적용: Q 또는 K 벡터를 위치에 따라 회전
    x: (batch, heads, seq_len, d_k)
    """
    d_half = x.shape[-1] // 2
    x1 = x[..., :d_half]   # 앞 절반
    x2 = x[..., d_half:]   # 뒤 절반

    # 2D 회전: [x1, x2] → [x1·cos - x2·sin, x1·sin + x2·cos]
    rotated = torch.cat([
        x1 * cos - x2 * sin,
        x1 * sin + x2 * cos
    ], dim=-1)

    return rotated


# 사용 예시
cos, sin = precompute_rope_frequencies(d_model=128, max_seq_len=4096)
Q_rotated = apply_rope(Q, cos[:seq_len], sin[:seq_len])
K_rotated = apply_rope(K, cos[:seq_len], sin[:seq_len])
# 이후 Q_rotated @ K_rotated^T 로 attention score 계산
```

---

## 3.4 Positional Encoding 방법 비교

| 방법 | 제안 논문 | 학습 필요? | 상대 위치? | 길이 외삽 | 사용 모델 |
|------|----------|-----------|-----------|----------|-----------|
| **Sinusoidal** | Vaswani et al. (2017) | No | 간접적 | 가능(이론적) | 원본 Transformer |
| **Learned** | Radford et al. (2018) | Yes | No | 불가 | GPT-2, GPT-3 |
| **ALiBi** | Press et al. (2022) [^1] | No | Yes | 우수 | BLOOM, MPT |
| **RoPE** | Su et al. (2021) | No | Yes | 확장 가능 [^2] | LLaMA, PaLM-2, Gemini |

[^1]: Press, O., Smith, N. A., & Lewis, M. (2022). **Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation.** *ICLR 2022*. https://arxiv.org/abs/2108.12409

[^2]: Chen, S., Wong, S., Chen, L., & Tian, Y. (2023). **Extending Context Window of Large Language Models via Positional Interpolation.** *arXiv preprint*. https://arxiv.org/abs/2306.15595 — RoPE의 context length를 학습 없이 확장하는 기법

---

## 참고 문헌

- Vaswani, A. et al. (2017). Attention Is All You Need. *NeurIPS 2017*. https://arxiv.org/abs/1706.03762
- Su, J. et al. (2021). RoFormer: Enhanced Transformer with Rotary Position Embedding. https://arxiv.org/abs/2104.09864
- Press, O. et al. (2022). Train Short, Test Long: Attention with Linear Biases. *ICLR 2022*. https://arxiv.org/abs/2108.12409
- Chen, S. et al. (2023). Extending Context Window via Positional Interpolation. https://arxiv.org/abs/2306.15595
