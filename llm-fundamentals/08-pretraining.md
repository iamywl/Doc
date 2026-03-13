# 08. Pre-training (사전 학습)

[← FFN & Normalization](07-ffn-normalization.md) | [다음: Scaling Laws →](09-scaling-laws.md)

---

## 핵심 접근 (Key Insight)

> **"다음 단어를 예측하는 것만으로 언어의 모든 것을 학습할 수 있다."**
>
> GPT-1(Radford et al., 2018)의 핵심 가설은:
> 충분히 큰 모델이 충분히 많은 텍스트에서 next token prediction을 수행하면,
> 그 과정에서 **문법, 사실 지식, 추론 능력, 심지어 프로그래밍까지** 부수적으로(emergently) 학습한다는 것이다.
>
> GPT-3(Brown et al., 2020)는 이를 175B 파라미터로 실증하며,
> 별도의 fine-tuning 없이 **few-shot prompting만으로** 다양한 NLP 태스크를 수행할 수 있음을 보였다.
> 이것은 "사전 학습 → 태스크별 fine-tuning" 패러다임에서
> **"사전 학습 → in-context learning"** 패러다임으로의 전환이었다.

---

## 8.1 Next Token Prediction (Causal Language Modeling)

### 논문 인용

> Radford, A., Narasimhan, K., Salimans, T., & Sutskever, I. (2018). **Improving Language Understanding by Generative Pre-Training.** *OpenAI Technical Report*. — GPT-1
>
> Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., & Sutskever, I. (2019). **Language Models are Unsupervised Multitask Learners.** *OpenAI Technical Report*. — GPT-2
>
> Brown, T. B., Mann, B., Ryder, N., Subbiah, M., Kaplan, J., Dhariwal, P., ... & Amodei, D. (2020). **Language Models are Few-Shot Learners.** *Advances in Neural Information Processing Systems (NeurIPS)*, 33, pp. 1877-1901. https://arxiv.org/abs/2005.14165 — GPT-3

### 수식

주어진 토큰 시퀀스 $x_1, x_2, \dots, x_{T}$에 대해, 모든 위치에서 다음 토큰의 log-likelihood를 최대화:

$$
\mathcal{L}(\theta) = -\sum_{t=1}^{T} \log P_\theta(x_t | x_1, x_2, \dots, x_{t-1})
$$

$$
P_\theta(x_t | x_{<t}) = \text{softmax}(h_t W_E^T)_{x_t}
$$

- `h_t`: t번째 위치의 최종 hidden state
- `W_E`: token embedding 행렬 (weight tying — 입력 embedding과 출력 head가 동일)
- 이 단순한 objective 하나가 LLM 학습의 전부

### 코드 구현

```python
import torch
import torch.nn.functional as F

def compute_causal_lm_loss(model, input_ids):
    """
    Causal Language Modeling Loss (Next Token Prediction)
    (Radford et al., 2018)

    input_ids: (batch_size, seq_len) — 토큰 ID 시퀀스
    """
    # 입력: [x₁, x₂, ..., x_{T-1}]
    # 타겟: [x₂, x₃, ..., x_T]  (한 칸 shift)
    inputs = input_ids[:, :-1]
    targets = input_ids[:, 1:]

    logits = model(inputs)  # (B, T-1, vocab_size)

    # Cross-entropy loss: -log P(x_t | x_{<t})
    loss = F.cross_entropy(
        logits.reshape(-1, logits.size(-1)),  # (B*(T-1), vocab_size)
        targets.reshape(-1),                   # (B*(T-1),)
        ignore_index=-1  # padding 토큰 무시
    )
    return loss


# 학습 루프 (simplified)
def train_step(model, batch, optimizer, max_grad_norm=1.0):
    """
    단일 학습 스텝
    AdamW + gradient clipping이 표준
    """
    optimizer.zero_grad()
    loss = compute_causal_lm_loss(model, batch)
    loss.backward()

    # Gradient clipping — 학습 안정화
    torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=max_grad_norm)

    optimizer.step()
    return loss.item()
```

---

## 8.2 학습 설정

### Optimizer: AdamW

> Loshchilov, I., & Hutter, F. (2019). **Decoupled Weight Decay Regularization.** *ICLR 2019*. https://arxiv.org/abs/1711.05101

$$
m_t = \beta_1 m_{t-1} + (1-\beta_1) g_t \quad \text{(first moment)}
$$
$$
v_t = \beta_2 v_{t-1} + (1-\beta_2) g_t^2 \quad \text{(second moment)}
$$
$$
\theta_t = \theta_{t-1} - \eta \left(\frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon} + \lambda \theta_{t-1}\right) \quad \text{(weight decay 분리)}
$$

| 하이퍼파라미터 | GPT-3 값 | LLaMA-2 값 |
|---------------|----------|------------|
| β₁ | 0.9 | 0.9 |
| β₂ | 0.95 | 0.95 |
| ε | 1e-8 | 1e-5 |
| Weight decay (λ) | 0.1 | 0.1 |
| Max learning rate | 6e-5 | 3e-4 (7B) / 1.5e-4 (70B) |
| Warmup steps | ~375M tokens | 2000 steps |
| LR schedule | Cosine decay | Cosine decay |

### Learning Rate Schedule: Cosine Decay

```python
import math

def cosine_lr_schedule(step, max_steps, warmup_steps, max_lr, min_lr):
    """
    Cosine learning rate schedule with warmup
    GPT-3, LLaMA 등 대부분의 LLM이 사용
    """
    if step < warmup_steps:
        # Linear warmup
        return max_lr * step / warmup_steps
    elif step > max_steps:
        return min_lr
    else:
        # Cosine decay
        progress = (step - warmup_steps) / (max_steps - warmup_steps)
        return min_lr + 0.5 * (max_lr - min_lr) * (1 + math.cos(math.pi * progress))
```

---

## 8.3 학습 데이터 구성

### 데이터 소스와 혼합 비율

| 모델 | 총 토큰 | 데이터 소스 | 논문 |
|------|---------|------------|------|
| **GPT-3** | 300B | CommonCrawl (60%), WebText2 (22%), Books (8%), Wikipedia (3%) | Brown et al. (2020) |
| **PaLM** | 780B | Web (27%), Books (13%), Wikipedia (4%), Code (5%), Conversations (50%) | Chowdhery et al. (2022) |
| **LLaMA** | 1.4T | CommonCrawl (67%), C4 (15%), GitHub (4.5%), Wikipedia (4.5%), Others | Touvron et al. (2023a) |
| **LLaMA-2** | 2T | 공개 데이터 (구성 비공개) | Touvron et al. (2023b) |
| **Claude** | 비공개 | 비공개 | — |

### 데이터 품질의 중요성

> Penedo, G. et al. (2023). **The RefinedWeb Dataset for Falcon LLM.** *NeurIPS 2023 Datasets Track*. https://arxiv.org/abs/2306.01116
>
> 필터링된 고품질 웹 데이터만으로도 curated dataset(Wikipedia, Books 등)을
> 혼합한 것과 동등한 성능을 달성할 수 있음을 보임.

---

## 8.4 In-Context Learning (ICL)

### 논문 인용

> Brown, T. B. et al. (2020). **Language Models are Few-Shot Learners.** *NeurIPS 2020*. https://arxiv.org/abs/2005.14165

### 핵심 접근

> **"모델 파라미터를 수정하지 않고, 프롬프트에 예시를 넣는 것만으로 새 태스크를 수행한다."**

```
Zero-shot:  "Translate English to French: cheese =>"
            → "fromage"

One-shot:   "Translate English to French: sea otter => loutre de mer
             cheese =>"
            → "fromage"

Few-shot:   "Translate English to French: sea otter => loutre de mer
             peppermint => menthe poivrée
             plush giraffe => girafe en peluche
             cheese =>"
            → "fromage"
```

GPT-3의 핵심 발견: **모델이 충분히 크면 (175B), few-shot ICL이
fine-tuning한 소형 모델과 비슷하거나 더 좋은 성능**을 보인다.

---

## 8.5 Emergent Abilities

### 논문 인용

> Wei, J., Tay, Y., Bommasani, R., Raffel, C., Zoph, B., Borgeaud, S., ... & Fedus, W. (2022). **Emergent Abilities of Large Language Models.** *Transactions on Machine Learning Research (TMLR)*. https://arxiv.org/abs/2206.07682

### 핵심 접근

> 특정 모델 크기 이하에서는 **전혀 보이지 않던 능력**이,
> 임계 크기를 넘으면 **급격히 출현**하는 현상.

예시:
- **산술 연산**: 10B 이하에서는 랜덤 수준 → 100B에서 갑자기 높은 정확도
- **Chain-of-thought 추론**: 소형 모델에서는 효과 없음 → 대형 모델에서만 작동
- **코드 생성**: 일정 크기 이상에서만 의미 있는 코드 생성 가능

---

## 참고 문헌

- Radford, A. et al. (2018). Improving Language Understanding by Generative Pre-Training. *OpenAI*.
- Radford, A. et al. (2019). Language Models are Unsupervised Multitask Learners. *OpenAI*.
- Brown, T. B. et al. (2020). Language Models are Few-Shot Learners. *NeurIPS 2020*. https://arxiv.org/abs/2005.14165
- Chowdhery, A. et al. (2022). PaLM: Scaling Language Modeling with Pathways. https://arxiv.org/abs/2204.02311
- Touvron, H. et al. (2023a). LLaMA: Open and Efficient Foundation Language Models. https://arxiv.org/abs/2302.13971
- Touvron, H. et al. (2023b). Llama 2: Open Foundation and Fine-Tuned Chat Models. https://arxiv.org/abs/2307.09288
- Loshchilov, I. & Hutter, F. (2019). Decoupled Weight Decay Regularization. *ICLR 2019*. https://arxiv.org/abs/1711.05101
- Wei, J. et al. (2022). Emergent Abilities of Large Language Models. *TMLR*. https://arxiv.org/abs/2206.07682
- Penedo, G. et al. (2023). The RefinedWeb Dataset for Falcon LLM. *NeurIPS 2023*. https://arxiv.org/abs/2306.01116
