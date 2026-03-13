# 09. Scaling Laws

[← Pre-training](08-pretraining.md) | [다음: RLHF & Alignment →](10-rlhf-alignment.md)

---

## 핵심 접근 (Key Insight)

> **"LLM 성능은 모델 크기, 데이터, 연산량과 예측 가능한 power-law 관계를 따른다."**
>
> Kaplan et al. (2020)의 가장 중요한 발견은:
> 모델 성능(loss)이 **무작위가 아니라 수학적으로 예측 가능한 법칙**을 따른다는 것이다.
> 이는 "얼마나 큰 모델을, 얼마나 많은 데이터로, 얼마나 오래 학습해야 하는가?"라는
> 질문에 **정량적 답**을 제공했다.
>
> Hoffmann et al. (2022)의 Chinchilla 논문은 이를 수정하여:
> 기존 모델들(GPT-3 포함)이 **파라미터 대비 데이터가 부족**(under-trained)했음을 밝히고,
> **파라미터 수와 토큰 수를 1:20 비율로** 동시에 확장해야 최적이라는 법칙을 제시했다.
> 이는 LLaMA 등 후속 모델의 설계에 직접적 영향을 미쳤다.

---

## 9.1 Kaplan Scaling Laws (OpenAI)

### 논문 인용

> Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., Gray, S., Radford, A., Wu, J., & Amodei, D. (2020). **Scaling Laws for Neural Language Models.** *arXiv preprint*. https://arxiv.org/abs/2001.08361

### 수식

모델 성능(cross-entropy loss)은 세 변수와 **power-law** 관계:

**파라미터 수 N에 대한 법칙:**

$$
L(N) \approx \left(\frac{N_c}{N}\right)^{\alpha_N}, \quad \alpha_N \approx 0.076, \quad N_c \approx 8.8 \times 10^{13}
$$

**데이터 크기 D에 대한 법칙:**

$$
L(D) \approx \left(\frac{D_c}{D}\right)^{\alpha_D}, \quad \alpha_D \approx 0.095, \quad D_c \approx 5.4 \times 10^{13}
$$

**연산량 C에 대한 법칙:**

$$
L(C) \approx \left(\frac{C_c}{C}\right)^{\alpha_C}, \quad \alpha_C \approx 0.050, \quad C_c \approx 3.1 \times 10^8
$$

**결합 법칙 (N과 D 동시):**

$$
L(N, D) = \left[\left(\frac{N_c}{N}\right)^{\alpha_N / \alpha_D} + \frac{D_c}{D}\right]^{\alpha_D}
$$

### 핵심 발견

1. **Power-law**: loss가 N, D, C의 거듭제곱으로 감소 → **로그 스케일에서 직선**
2. **모델 크기 우선**: 같은 compute budget이면 **큰 모델을 적은 데이터로 학습**하는 것이 효율적
3. **Sample efficiency**: 큰 모델일수록 **동일 성능 달성에 필요한 데이터가 적음**
4. **아키텍처 독립**: depth, width, n_heads 등 세부 구조보다 **총 파라미터 수 N이 지배적**

### 시각적 이해

```
Loss (log scale)
  |
  |\
  | \
  |  \     ← 10x 파라미터 증가 시 loss가 일정 비율로 감소
  |   \
  |    \
  |     \---___
  |            ---___    ← 수렴하지 않고 계속 감소 (7 orders of magnitude에서 확인)
  |                  ---___
  +-------------------------→ Parameters N (log scale)
```

---

## 9.2 Chinchilla Scaling Laws (DeepMind)

### 논문 인용

> Hoffmann, J., Borgeaud, S., Mensch, A., Buchatskaya, E., Cai, T., Rutherford, E., ... & Sifre, L. (2022). **Training Compute-Optimal Large Language Models.** *NeurIPS 2022*. https://arxiv.org/abs/2203.15556

### 핵심 접근

> **"Kaplan의 법칙은 틀렸다 — 데이터를 훨씬 더 많이 써야 한다."**
>
> Kaplan et al.은 "모델을 크게 만들고 일찍 멈추는 것"이 최적이라고 했지만,
> Chinchilla 논문은 400개 이상의 모델을 학습시킨 대규모 실험을 통해:
> **파라미터 수 N과 토큰 수 D를 동일한 비율로 확장**해야 compute-optimal이라고 수정했다.

### 수식

주어진 compute budget $C$에 대한 최적 모델 크기와 데이터 크기:

$$
N_{opt} \propto C^{a}, \quad D_{opt} \propto C^{b}
$$

$$
a \approx 0.50, \quad b \approx 0.50
$$

근사적으로 **C ≈ 6ND** (forward + backward FLOPs)이므로:

$$
D_{opt} \approx 20 \times N_{opt}
$$

> "For every doubling of model size, the number of training tokens should also be doubled."
> — Hoffmann et al. (2022)

### Kaplan vs Chinchilla 비교

| | Kaplan (2020) | Chinchilla (2022) |
|--|---------------|-------------------|
| **최적 전략** | 큰 모델, 적은 데이터 | **N과 D를 동일 비율로 확장** |
| **토큰/파라미터 비율** | ~1.7x (GPT-3) | **~20x** |
| **실험 규모** | 수십 모델 | **400+ 모델** |
| **영향** | GPT-3 설계 근거 | LLaMA, Gemini 설계 근거 |

### 실제 모델별 비교

| 모델 | 파라미터 | 학습 토큰 | 비율 (D/N) | Chinchilla 최적? |
|------|---------|----------|------------|-----------------|
| GPT-3 | 175B | 300B | **1.7x** | Under-trained |
| Gopher | 280B | 300B | **1.1x** | Under-trained |
| **Chinchilla** | 70B | 1.4T | **20x** | **Optimal** |
| LLaMA | 65B | 1.4T | **21.5x** | Optimal |
| LLaMA-2 | 70B | 2T | **28.6x** | Over-trained (의도적) |
| Mistral 7B | 7B | ~2T | **~285x** | Over-trained (추론 효율 목적) |

### 코드: Chinchilla 최적값 추정

```python
def estimate_chinchilla_optimal(compute_budget_flops):
    """
    Chinchilla 최적 모델 크기와 데이터 크기 추정
    (Hoffmann et al., 2022)

    C ≈ 6 * N * D (forward + backward pass FLOPs 근사)
    D_opt ≈ 20 * N_opt

    Args:
        compute_budget_flops: 총 연산 예산 (FLOPs)

    Returns:
        (optimal_params, optimal_tokens)
    """
    # C = 6 * N * D, D = 20 * N → C = 120 * N²
    # N = sqrt(C / 120)
    optimal_params = (compute_budget_flops / 120) ** 0.5
    optimal_tokens = 20 * optimal_params

    return optimal_params, optimal_tokens


def compute_flops(n_params, n_tokens):
    """학습에 필요한 총 FLOPs 추정 (6ND 근사)"""
    return 6 * n_params * n_tokens


# 예시들
examples = [
    ("GPT-3",      175e9,  300e9),
    ("Chinchilla", 70e9,   1.4e12),
    ("LLaMA-2",    70e9,   2e12),
]

for name, N, D in examples:
    C = compute_flops(N, D)
    N_opt, D_opt = estimate_chinchilla_optimal(C)
    ratio = D / N
    print(f"{name:12s}: N={N/1e9:.0f}B, D={D/1e12:.1f}T, "
          f"D/N={ratio:.1f}x, "
          f"Chinchilla optimal: N={N_opt/1e9:.0f}B, D={D_opt/1e12:.1f}T")
```

---

## 9.3 Inference-Optimal Scaling (Over-training)

### 논문 인용

> Sardana, N., & Frankle, J. (2023). **Beyond Chinchilla-Optimal: Accounting for Inference in Language Model Scaling Laws.** *arXiv preprint*. https://arxiv.org/abs/2401.00448

### 핵심 접근

> **"학습 비용만이 아니라 추론 비용까지 고려하면, 작은 모델을 더 오래 학습하는 것이 최적이다."**
>
> Chinchilla는 **학습 compute만 최적화**했지만, 실제로 모델은 학습 후
> 수백만 번의 inference를 수행한다. 작은 모델의 inference 비용이 낮으므로,
> **총 비용(학습 + 추론) 관점에서는 Chinchilla 비율보다 더 많은 데이터로 학습**하는 것이 유리하다.
>
> LLaMA-2 (D/N = 28.6x), Mistral (D/N ≈ 285x)가 이 전략을 따른다.

---

## 참고 문헌

- Kaplan, J. et al. (2020). Scaling Laws for Neural Language Models. https://arxiv.org/abs/2001.08361
- Hoffmann, J. et al. (2022). Training Compute-Optimal Large Language Models. *NeurIPS 2022*. https://arxiv.org/abs/2203.15556
- Sardana, N. & Frankle, J. (2023). Beyond Chinchilla-Optimal: Accounting for Inference. https://arxiv.org/abs/2401.00448
- Touvron, H. et al. (2023). LLaMA: Open and Efficient Foundation Language Models. https://arxiv.org/abs/2302.13971
