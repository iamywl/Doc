# 07. Feed-Forward Network & Normalization

[← Multi-Head Attention](06-multi-head-attention.md) | [다음: Pre-training →](08-pretraining.md)

---

## 핵심 접근 (Key Insight)

> **"Attention은 토큰 간 관계를 포착하고, FFN은 각 토큰을 비선형 변환한다."**
>
> Self-Attention만으로는 **모든 연산이 선형 조합**이다 (softmax는 가중치를 만들 뿐,
> V의 선형 결합). FFN이 **비선형성(non-linearity)**을 도입하여
> 각 토큰의 representation을 더 풍부하게 변환한다.
>
> 최근 연구에서 FFN은 "key-value memory"처럼 작동한다는 해석이 있다:
> 첫 번째 레이어(W₁)가 패턴을 매칭하고, 두 번째 레이어(W₂)가 대응하는 정보를 출력한다
> (Geva et al., 2021).
>
> Normalization은 깊은 네트워크의 학습을 안정화하는 핵심이며,
> RMSNorm은 LayerNorm의 mean-centering을 제거하여 **15% 빠른** 속도를 달성했다.

---

## 7.1 Position-wise Feed-Forward Network

### 논문 인용

> Vaswani, A. et al. (2017). **Attention Is All You Need.** *NeurIPS 2017*, Section 3.3. https://arxiv.org/abs/1706.03762
>
> Geva, M., Schuster, R., Berant, J., & Levy, O. (2021). **Transformer Feed-Forward Layers Are Key-Value Memories.** *EMNLP 2021*. https://arxiv.org/abs/2012.14913

### 원본 수식 (ReLU)

$$
\text{FFN}(x) = \max(0, \; xW_1 + b_1)W_2 + b_2
$$

$$
W_1 \in \mathbb{R}^{d_{model} \times d_{ff}}, \quad W_2 \in \mathbb{R}^{d_{ff} \times d_{model}}
$$

- `d_ff`는 보통 `4 × d_model` (원본 Transformer: 512 → 2048)
- "position-wise": 각 토큰 위치에 **독립적으로** 동일한 FFN 적용

---

## 7.2 SwiGLU Activation

### 논문 인용

> Shazeer, N. (2020). **GLU Variants Improve Transformer.** *arXiv preprint*. https://arxiv.org/abs/2002.05202
>
> Dauphin, Y. N., Fan, A., Auli, M., & Grangier, D. (2017). **Language Modeling with Gated Convolutional Networks.** *ICML 2017*. https://arxiv.org/abs/1612.08083 — GLU(Gated Linear Unit)의 최초 제안

### 핵심 접근

> **"Gating mechanism으로 정보를 선택적으로 통과시킨다."**
>
> 일반 FFN: 입력을 확장(up-project)하고 activation을 거쳐 축소(down-project)
> GLU 변형: **두 경로로 분기** — 하나는 activation, 하나는 gate — 둘을 element-wise 곱
> SwiGLU는 이 중 Swish(SiLU) activation을 사용한 변형으로, Shazeer(2020)의 실험에서
> **ReLU, GELU 등 모든 변형 중 최고 성능**을 기록했다.

### 수식

$$
\text{SwiGLU}(x) = \text{Swish}(xW_1) \otimes (xW_3)
$$

$$
\text{FFN}_{SwiGLU}(x) = (\text{Swish}(xW_1) \otimes xW_3) W_2
$$

$$
\text{Swish}(x) = x \cdot \sigma(x) = \frac{x}{1 + e^{-x}}
$$

- `W₁`: activation 경로
- `W₃`: gate 경로 (추가 파라미터)
- `⊗`: element-wise multiplication
- 파라미터가 50% 증가하므로 `d_ff`를 `2/3 × 4d_model`로 조정하여 총 파라미터를 맞춤

### Activation 비교

| Activation | 수식 | 사용 모델 |
|-----------|------|-----------|
| ReLU | max(0, x) | 원본 Transformer, GPT-2 |
| GELU | x · Φ(x) | GPT-3, BERT |
| **SwiGLU** | Swish(xW₁) ⊗ xW₃ | **LLaMA, PaLM, Gemini** |

### 코드 구현

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class SwiGLUFeedForward(nn.Module):
    """
    SwiGLU FFN (Shazeer, 2020)
    LLaMA, PaLM 등에서 사용

    d_ff는 보통 (8/3) * d_model로 설정 (2/3 × 4d_model)
    이렇게 하면 gate(W3) 추가에도 총 파라미터가 표준 FFN과 비슷
    """
    def __init__(self, d_model, d_ff):
        super().__init__()
        self.w1 = nn.Linear(d_model, d_ff, bias=False)  # activation path
        self.w2 = nn.Linear(d_ff, d_model, bias=False)  # down-projection
        self.w3 = nn.Linear(d_model, d_ff, bias=False)  # gate path

    def forward(self, x):
        # Swish(x @ W1) * (x @ W3): 두 경로의 element-wise product
        # F.silu = Swish activation (x * sigmoid(x))
        return self.w2(F.silu(self.w1(x)) * self.w3(x))
```

---

## 7.3 Layer Normalization

### 논문 인용

> Ba, J. L., Kiros, J. R., & Hinton, G. E. (2016). **Layer Normalization.** *arXiv preprint*. https://arxiv.org/abs/1607.06450

### 수식

$$
\text{LayerNorm}(x) = \gamma \cdot \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} + \beta
$$

$$
\mu = \frac{1}{d}\sum_{i=1}^{d} x_i, \quad \sigma^2 = \frac{1}{d}\sum_{i=1}^{d}(x_i - \mu)^2
$$

---

## 7.4 RMSNorm

### 논문 인용

> Zhang, B., & Sennrich, R. (2019). **Root Mean Square Layer Normalization.** *NeurIPS 2019*. https://arxiv.org/abs/1910.07467

### 핵심 접근

> **"LayerNorm에서 mean-centering(평균 빼기)을 제거해도 성능이 유지된다."**
>
> LayerNorm의 두 단계 — (1) mean centering, (2) variance scaling — 중
> (2)만으로도 학습 안정화에 충분하다는 실험적 발견.
> 연산량이 줄어들어 **약 15% 속도 향상**.

### 수식

$$
\text{RMSNorm}(x) = \frac{x}{\text{RMS}(x)} \cdot \gamma
$$

$$
\text{RMS}(x) = \sqrt{\frac{1}{n}\sum_{i=1}^{n}x_i^2 + \epsilon}
$$

- `γ`: 학습 가능한 scale parameter
- `β`(bias) 없음 — LayerNorm과의 핵심 차이
- `ε`: numerical stability를 위한 작은 상수 (보통 1e-6)

### 코드 구현

```python
class RMSNorm(nn.Module):
    """
    RMSNorm (Zhang & Sennrich, 2019)
    LayerNorm보다 간단하고 ~15% 빠름
    LLaMA, PaLM-2, Gemini 등에서 채택
    """
    def __init__(self, d_model, eps=1e-6):
        super().__init__()
        self.weight = nn.Parameter(torch.ones(d_model))  # γ (scale)
        self.eps = eps
        # 주의: bias(β)가 없음!

    def forward(self, x):
        # RMS = sqrt(mean(x²))
        rms = torch.sqrt(torch.mean(x ** 2, dim=-1, keepdim=True) + self.eps)
        # x / RMS(x) * γ
        return (x / rms) * self.weight
```

---

## 7.5 Normalization 비교

| 방법 | 연산 | 파라미터 | 속도 | 사용 모델 |
|------|------|---------|------|-----------|
| **LayerNorm** | mean + variance | γ, β | baseline | GPT-2/3, BERT |
| **RMSNorm** | variance only | γ only | **~15% 빠름** | LLaMA, PaLM-2, Gemini |

---

## 참고 문헌

- Vaswani, A. et al. (2017). Attention Is All You Need. *NeurIPS 2017*. https://arxiv.org/abs/1706.03762
- Ba, J. L. et al. (2016). Layer Normalization. https://arxiv.org/abs/1607.06450
- Dauphin, Y. N. et al. (2017). Language Modeling with Gated Convolutional Networks. *ICML 2017*. https://arxiv.org/abs/1612.08083
- Shazeer, N. (2020). GLU Variants Improve Transformer. https://arxiv.org/abs/2002.05202
- Zhang, B. & Sennrich, R. (2019). Root Mean Square Layer Normalization. *NeurIPS 2019*. https://arxiv.org/abs/1910.07467
- Geva, M. et al. (2021). Transformer Feed-Forward Layers Are Key-Value Memories. *EMNLP 2021*. https://arxiv.org/abs/2012.14913
