# 12. Inference & Generation

[← Constitutional AI](11-constitutional-ai.md) | [다음: Model Comparison →](13-model-comparison.md)

---

## 핵심 접근 (Key Insight)

> **"LLM은 한 번에 하나의 토큰을 생성하며, '어떤 토큰을 선택할 것인가'가 출력 품질을 결정한다."**
>
> 학습된 LLM은 각 위치에서 vocabulary 전체에 대한 확률 분포를 출력한다.
> 가장 높은 확률의 토큰을 항상 선택하면(greedy) 반복적이고 지루한 텍스트가 생성되고,
> 너무 무작위로 선택하면 비문이 된다.
>
> Holtzman et al. (2020)은 "인간의 텍스트는 높은 확률 토큰의 집합에서 나오지만,
> 그 중 하나를 확률적으로 선택한다"는 관찰에서 **Nucleus Sampling (Top-p)**을 제안했다.
>
> Inference 속도 최적화에서는 **KV Cache**가 핵심이다:
> 이전에 계산한 K, V를 캐싱하여 새 토큰마다 전체 시퀀스를 재계산하지 않음으로써
> O(T^2)를 O(T)로 줄인다.

---

## 12.1 Autoregressive Generation

### 수식

$$
P(y_1, y_2, \dots, y_T) = \prod_{t=1}^{T} P(y_t | y_1, \dots, y_{t-1})
$$

각 step에서:
1. 전체 시퀀스를 모델에 입력
2. 마지막 위치의 logits으로 다음 토큰의 확률 분포 계산
3. Sampling strategy에 따라 토큰 선택
4. 선택된 토큰을 시퀀스에 추가하고 1번으로 돌아감

---

## 12.2 Sampling Strategies

### Temperature Scaling

$$
P(x_i) = \frac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}
$$

| Temperature T | 효과 | 용도 |
|--------------|------|------|
| T → 0 (greedy) | 가장 확률 높은 토큰만 선택 | 코드 생성, 사실 기반 Q&A |
| T = 1.0 | 원본 분포 그대로 | 기본값 |
| T > 1.0 | 분포가 균일해짐 (더 무작위) | 창의적 글쓰기 |

### Top-k Sampling

> Fan, A., Lewis, M., & Dauphin, Y. (2018). **Hierarchical Neural Story Generation.** *ACL 2018*. https://arxiv.org/abs/1805.04833

상위 k개 토큰만 후보로 유지:

$$
P'(x_i) = \begin{cases} \frac{P(x_i)}{\sum_{j \in \text{top-k}} P(x_j)} & \text{if } x_i \in \text{top-k} \\ 0 & \text{otherwise} \end{cases}
$$

### Top-p Sampling (Nucleus Sampling)

> Holtzman, A., Buys, J., Du, L., Forbes, M., & Choi, Y. (2020). **The Curious Case of Neural Text Degeneration.** *ICLR 2020*. https://arxiv.org/abs/1904.09751

누적 확률이 p를 넘을 때까지의 토큰만 후보:

$$
V_p = \arg\min_{V' \subseteq V} \left\{ |V'| : \sum_{x \in V'} P(x) \geq p \right\}
$$

**Top-k vs Top-p**:
- Top-k: 항상 고정된 k개 후보 → 분포가 뾰족하면 불필요한 토큰 포함
- Top-p: 분포 모양에 따라 후보 수가 **동적으로 조절** → 더 유연

---

## 12.3 코드 구현

```python
import torch
import torch.nn.functional as F

def generate(model, prompt_ids, max_tokens=256, temperature=0.7,
             top_p=0.9, top_k=50, eos_token_id=2):
    """
    LLM Autoregressive Generation
    Temperature + Top-k + Top-p 조합
    """
    generated = prompt_ids.clone()

    for _ in range(max_tokens):
        # Forward pass: 마지막 토큰의 logits만 사용
        logits = model(generated)[:, -1, :]  # (B, vocab_size)

        # 1. Temperature scaling
        if temperature > 0:
            logits = logits / temperature
        else:
            # temperature=0 → greedy decoding
            next_token = logits.argmax(dim=-1, keepdim=True)
            generated = torch.cat([generated, next_token], dim=1)
            if next_token.item() == eos_token_id:
                break
            continue

        # 2. Top-k filtering
        if top_k > 0:
            top_k_values, _ = torch.topk(logits, min(top_k, logits.size(-1)))
            min_top_k = top_k_values[:, -1:]
            logits = logits.masked_fill(logits < min_top_k, float('-inf'))

        # 3. Top-p (nucleus) filtering
        if top_p < 1.0:
            sorted_logits, sorted_indices = torch.sort(logits, descending=True)
            cumulative_probs = torch.cumsum(
                F.softmax(sorted_logits, dim=-1), dim=-1
            )
            # 누적 확률이 p를 초과하는 토큰 제거
            # (첫 번째 토큰은 항상 유지)
            sorted_mask = cumulative_probs - F.softmax(sorted_logits, dim=-1) >= top_p
            sorted_logits[sorted_mask] = float('-inf')
            # 원래 인덱스 순서로 복원
            logits = torch.zeros_like(logits).scatter_(
                1, sorted_indices, sorted_logits
            )

        # 4. 확률 분포에서 샘플링
        probs = F.softmax(logits, dim=-1)
        next_token = torch.multinomial(probs, num_samples=1)

        generated = torch.cat([generated, next_token], dim=1)

        if next_token.item() == eos_token_id:
            break

    return generated
```

---

## 12.4 KV Cache

### 핵심 접근

> 이전 토큰의 K, V를 캐싱하여 **매 step마다 전체 시퀀스를 재계산하지 않는다.**

KV Cache 없이:
```
Step 1: [A]           → compute K,V for [A]          → predict B
Step 2: [A, B]        → compute K,V for [A, B]       → predict C  (A 중복!)
Step 3: [A, B, C]     → compute K,V for [A, B, C]    → predict D  (A,B 중복!)
```

KV Cache 사용:
```
Step 1: [A]           → compute & cache K,V for [A]   → predict B
Step 2: [B] + cache   → compute K,V for [B] only      → predict C
Step 3: [C] + cache   → compute K,V for [C] only      → predict D
```

### 코드

```python
def attention_with_kv_cache(Q_new, K_new, V_new, kv_cache=None):
    """
    KV Cache를 활용한 Attention
    Inference 시 O(T^2) → O(T) per step
    """
    if kv_cache is not None:
        K_cached, V_cached = kv_cache
        # 기존 cache에 새로운 K, V를 append
        K = torch.cat([K_cached, K_new], dim=2)  # (B, H, T_prev+1, d_k)
        V = torch.cat([V_cached, V_new], dim=2)
    else:
        K, V = K_new, V_new

    # Q_new는 새 토큰 하나에 대해서만 → (B, H, 1, d_k)
    # K, V는 전체 시퀀스 → (B, H, T_total, d_k)
    scores = torch.matmul(Q_new, K.transpose(-2, -1)) / math.sqrt(K.size(-1))
    attn = F.softmax(scores, dim=-1)
    output = torch.matmul(attn, V)

    # 업데이트된 cache 반환
    return output, (K, V)
```

### KV Cache 메모리 사용량

$$
\text{KV Cache (bytes)} = 2 \times n_{layers} \times n_{kv\_heads} \times d_k \times T \times \text{dtype\_size} \times B
$$

| 모델 | Batch=1, T=2048 | Batch=1, T=32768 |
|------|----------------|-----------------|
| LLaMA-2 7B (GQA, 8 KV heads) | ~0.5 GB | ~8 GB |
| LLaMA-2 70B (GQA, 8 KV heads) | ~2.5 GB | ~40 GB |
| GPT-3 175B (MHA, 96 KV heads) | ~12 GB | ~192 GB |

> 이것이 GQA/MQA가 중요한 이유 — KV Cache 메모리가 n_kv_heads에 비례

---

## 12.5 기타 최적화 기법

| 기법 | 논문 | 핵심 접근 |
|------|------|----------|
| **FlashAttention** | Dao et al. (2022) | IO-aware tiling으로 attention의 메모리 접근 최적화 |
| **Speculative Decoding** | Leviathan et al. (2023) | 작은 draft 모델로 여러 토큰 예측 후, 큰 모델로 검증 |
| **Continuous Batching** | Yu et al. (2022) | 요청별 동적 batching으로 GPU 활용도 극대화 |
| **PagedAttention** | Kwon et al. (2023) | OS의 virtual memory 기법을 KV Cache에 적용 |

---

## 참고 문헌

- Holtzman, A. et al. (2020). The Curious Case of Neural Text Degeneration. *ICLR 2020*. https://arxiv.org/abs/1904.09751
- Fan, A. et al. (2018). Hierarchical Neural Story Generation. *ACL 2018*. https://arxiv.org/abs/1805.04833
- Dao, T. et al. (2022). FlashAttention: Fast and Memory-Efficient Exact Attention. *NeurIPS 2022*. https://arxiv.org/abs/2205.14135
- Leviathan, Y. et al. (2023). Fast Inference from Transformers via Speculative Decoding. *ICML 2023*. https://arxiv.org/abs/2211.17192
- Kwon, W. et al. (2023). Efficient Memory Management for LLM Serving with PagedAttention. *SOSP 2023*. https://arxiv.org/abs/2309.06180
