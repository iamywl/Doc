# 10. RLHF & Alignment

[← Scaling Laws](09-scaling-laws.md) | [다음: Constitutional AI →](11-constitutional-ai.md)

---

## 핵심 접근 (Key Insight)

> **"다음 단어를 잘 예측하는 모델 ≠ 사람에게 도움이 되는 모델"**
>
> Pre-training만으로는 모델이 유해한 콘텐츠를 생성하거나,
> 지시를 따르지 않거나, 사실이 아닌 것을 자신있게 말할 수 있다.
>
> InstructGPT(Ouyang et al., 2022)의 핵심 발견:
> **1.3B InstructGPT가 175B GPT-3보다 사람이 선호하는 응답을 생성**했다.
> 즉, 모델 크기보다 **alignment이 사용자 만족도에 더 큰 영향**을 미친다.
>
> 이 alignment은 3단계로 이루어진다:
> (1) SFT — 고품질 데모로 행동 양식 학습
> (2) Reward Model — 인간 선호도를 점수화
> (3) PPO — RM 점수를 최대화하도록 강화학습

---

## 10.1 Supervised Fine-Tuning (SFT)

### 논문 인용

> Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C., Mishkin, P., ... & Lowe, R. (2022). **Training language models to follow instructions with human feedback.** *Advances in Neural Information Processing Systems (NeurIPS)*, 35, pp. 27730-27744. https://arxiv.org/abs/2203.02155

### 핵심 접근

> 사람이 직접 작성한 고품질 (prompt, response) 쌍으로 모델을 fine-tuning.
> InstructGPT에서는 40명의 labeler가 ~13,000개의 데모를 작성.
> Loss는 pre-training과 동일한 next-token prediction이지만,
> **prompt 부분의 loss는 무시하고 response 부분만 학습**한다.

### 코드

```python
import torch
import torch.nn.functional as F

def sft_loss(model, prompt_ids, response_ids):
    """
    SFT Loss: response 부분만 next-token prediction
    (Ouyang et al., 2022, Section 3.1)

    prompt:   "Explain quantum computing simply."
    response: "Quantum computing uses qubits that can be..."
    """
    full_ids = torch.cat([prompt_ids, response_ids], dim=1)
    logits = model(full_ids[:, :-1])  # 마지막 토큰 제외

    # 라벨 생성: prompt 부분은 -1로 마스킹 (loss에서 제외)
    labels = full_ids[:, 1:].clone()
    prompt_len = prompt_ids.size(1)
    labels[:, :prompt_len - 1] = -1  # prompt 부분 마스킹

    return F.cross_entropy(
        logits.reshape(-1, logits.size(-1)),
        labels.reshape(-1),
        ignore_index=-1
    )
```

---

## 10.2 Reward Model (RM)

### 논문 인용

> Ouyang et al. (2022), Section 3.2. "Reward model training"
>
> Bradley, R. A., & Terry, M. E. (1952). **Rank Analysis of Incomplete Block Designs: I. The Method of Paired Comparisons.** *Biometrika*, 39(3/4), pp. 324-345. — 선호도 모델의 수학적 기반

### 핵심 접근

> **사람의 "좋다/나쁘다" 판단을 scalar 점수로 변환하는 모델.**
>
> 같은 prompt에 대해 여러 응답을 생성하고, 사람이 순위를 매긴 데이터로 학습.
> Bradley-Terry model을 사용하여 **pairwise comparison**을 학습:
> "응답 A가 응답 B보다 좋다"는 판단을 "r(A) > r(B)"로 모델링.
>
> InstructGPT에서는 ~33,000개의 비교 데이터를 수집했다.

### 수식

$$
\mathcal{L}_{RM}(\theta) = -\mathbb{E}_{(x, y_w, y_l) \sim D}\left[\log \sigma\left(r_\theta(x, y_w) - r_\theta(x, y_l)\right)\right]
$$

- $x$: prompt
- $y_w$: 사람이 선호한 응답 (winner)
- $y_l$: 사람이 비선호한 응답 (loser)
- $r_\theta$: reward model이 출력하는 scalar 점수
- $\sigma$: sigmoid 함수

K개 응답의 순위가 있을 때, $\binom{K}{2}$개의 비교 쌍을 생성:

$$
\mathcal{L}_{RM}(\theta) = -\frac{1}{\binom{K}{2}} \mathbb{E}_{(x, y_w, y_l)}\left[\log \sigma\left(r_\theta(x, y_w) - r_\theta(x, y_l)\right)\right]
$$

### 코드

```python
import torch
import torch.nn as nn

class RewardModel(nn.Module):
    """
    Reward Model (Ouyang et al., 2022)
    SFT 모델을 기반으로 마지막에 scalar head 추가
    """
    def __init__(self, base_model, d_model):
        super().__init__()
        self.base = base_model  # SFT 모델 (또는 pre-trained 모델)
        self.reward_head = nn.Linear(d_model, 1, bias=False)

    def forward(self, input_ids):
        hidden = self.base(input_ids)         # (B, T, d_model)
        last_hidden = hidden[:, -1, :]        # 마지막 토큰의 hidden state
        reward = self.reward_head(last_hidden) # (B, 1) → scalar
        return reward.squeeze(-1)


def reward_model_loss(rm, prompt_ids, chosen_ids, rejected_ids):
    """
    Bradley-Terry pairwise ranking loss
    선호 응답의 reward > 비선호 응답의 reward
    """
    full_chosen = torch.cat([prompt_ids, chosen_ids], dim=1)
    full_rejected = torch.cat([prompt_ids, rejected_ids], dim=1)

    r_chosen = rm(full_chosen)      # scalar reward for chosen
    r_rejected = rm(full_rejected)  # scalar reward for rejected

    # -log σ(r_chosen - r_rejected): chosen의 reward가 높을수록 loss 감소
    loss = -torch.log(torch.sigmoid(r_chosen - r_rejected)).mean()
    return loss
```

---

## 10.3 PPO (Proximal Policy Optimization)

### 논문 인용

> Schulman, J., Wolski, F., Dhariwal, P., Radford, A., & Klimov, O. (2017). **Proximal Policy Optimization Algorithms.** *arXiv preprint*. https://arxiv.org/abs/1707.06347
>
> Ouyang et al. (2022), Section 3.3. "RL fine-tuning"

### 핵심 접근

> **"SFT 모델을 RL agent로, RM 점수를 reward로 사용하여 강화학습."**
>
> PPO의 핵심은 **clipped surrogate objective**: policy 업데이트 폭을 제한하여
> 학습이 불안정해지는 것을 방지한다.
>
> 추가로 **KL penalty**를 도입하여 SFT 모델에서 너무 멀어지지 않도록 제약한다.
> 이것이 없으면 모델이 RM의 허점을 찾아 "reward hacking"하게 된다
> (높은 reward를 받지만 실제로는 무의미한 응답 생성).

### PPO Clipped Surrogate Objective

$$
\mathcal{L}^{PPO}(\theta) = \mathbb{E}_t\left[\min\left(r_t(\theta)\hat{A}_t, \;\text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon)\hat{A}_t\right)\right]
$$

$$
r_t(\theta) = \frac{\pi_\theta(a_t | s_t)}{\pi_{\theta_{old}}(a_t | s_t)}
$$

| 기호 | 의미 |
|------|------|
| $r_t(\theta)$ | policy ratio — 새 정책과 이전 정책의 확률 비율 |
| $\hat{A}_t$ | advantage estimate — 기대 보상 대비 얼마나 좋은지 |
| $\epsilon$ | clipping parameter (보통 0.2) |
| $\pi_\theta$ | 현재 policy (학습 중인 LLM) |
| $\pi_{\theta_{old}}$ | 이전 policy (업데이트 전 LLM) |

### InstructGPT의 전체 RLHF Objective

$$
\text{objective}(\phi) = \mathbb{E}_{(x,y)\sim\pi_\phi}\left[r_\theta(x, y)\right] - \beta \cdot \mathbb{E}_{(x,y)\sim\pi_\phi}\left[\text{KL}\left(\pi_\phi(y|x) \| \pi_{SFT}(y|x)\right)\right] + \gamma \cdot \mathbb{E}_{x \sim D_{pretrain}}\left[\log \pi_\phi(x)\right]
$$

- 제1항: **RM reward 최대화**
- 제2항: **KL penalty** — SFT 모델에서 너무 벗어나지 않도록
- 제3항: **PPO-ptx** — pre-training 데이터도 일부 섞어 성능 유지

### 코드

```python
def rlhf_training_step(policy, ref_policy, reward_model, prompt_ids, beta=0.01):
    """
    RLHF with PPO — simplified
    (Ouyang et al., 2022, Section 3.3)
    """
    # 1. Policy로 응답 생성
    with torch.no_grad():
        response_ids = policy.generate(prompt_ids, max_new_tokens=256)
        full_ids = torch.cat([prompt_ids, response_ids], dim=1)

    # 2. Reward Model로 점수 계산
    reward = reward_model(full_ids)

    # 3. KL divergence penalty
    policy_logprobs = get_logprobs(policy, full_ids)
    ref_logprobs = get_logprobs(ref_policy, full_ids)
    kl_div = (policy_logprobs - ref_logprobs).sum(dim=-1)

    # 4. 최종 reward = RM reward - β * KL
    final_reward = reward - beta * kl_div

    # 5. PPO update with clipping
    advantages = compute_gae(final_reward)  # Generalized Advantage Estimation
    ppo_loss = compute_ppo_clipped_loss(policy, full_ids, advantages, clip_eps=0.2)

    return ppo_loss


def compute_ppo_clipped_loss(policy, ids, advantages, clip_eps=0.2):
    """PPO Clipped Surrogate Objective"""
    new_logprobs = get_logprobs(policy, ids)
    old_logprobs = get_logprobs(policy, ids).detach()

    ratio = torch.exp(new_logprobs - old_logprobs)

    surr1 = ratio * advantages
    surr2 = torch.clamp(ratio, 1 - clip_eps, 1 + clip_eps) * advantages

    # min을 취해 policy가 급격히 변하는 것을 양방향으로 제한
    return -torch.min(surr1, surr2).mean()
```

---

## 10.4 DPO (Direct Preference Optimization)

### 논문 인용

> Rafailov, R., Sharma, A., Mitchell, E., Ermon, S., Manning, C. D., & Finn, C. (2023). **Direct Preference Optimization: Your Language Model is Secretly a Reward Model.** *Advances in Neural Information Processing Systems (NeurIPS)*, 36. https://arxiv.org/abs/2305.18290

### 핵심 접근

> **"Reward Model과 PPO 없이, 선호도 데이터로 직접 policy를 학습할 수 있다."**
>
> DPO의 핵심 통찰: RLHF의 최적 policy는 reward function의 closed-form으로 표현 가능.
> 이를 역으로 이용하면, policy 자체가 implicit reward model이 되어
> **RM 학습 → PPO라는 복잡한 2단계를 1단계로 축소**할 수 있다.
>
> 장점: 구현이 간단 (SFT와 비슷한 loss), 학습이 안정적, 하이퍼파라미터가 적음
> 단점: on-policy 데이터 생성이 없어 성능 상한이 RLHF보다 낮을 수 있음

### 수식

$$
\mathcal{L}_{DPO}(\pi_\theta; \pi_{ref}) = -\mathbb{E}_{(x, y_w, y_l) \sim D}\left[\log \sigma\left(\beta \log\frac{\pi_\theta(y_w|x)}{\pi_{ref}(y_w|x)} - \beta \log\frac{\pi_\theta(y_l|x)}{\pi_{ref}(y_l|x)}\right)\right]
$$

- $\pi_\theta$: 학습 중인 policy
- $\pi_{ref}$: reference policy (보통 SFT 모델, frozen)
- $\beta$: temperature (KL constraint 강도)
- $y_w, y_l$: 선호/비선호 응답

### 코드

```python
def dpo_loss(policy, ref_policy, prompt, chosen, rejected, beta=0.1):
    """
    DPO Loss (Rafailov et al., 2023)
    RM + PPO 없이 직접 preference 학습
    """
    # 각 응답의 log P(response | prompt)
    pi_logp_chosen = get_sequence_logprobs(policy, prompt, chosen)
    pi_logp_rejected = get_sequence_logprobs(policy, prompt, rejected)
    ref_logp_chosen = get_sequence_logprobs(ref_policy, prompt, chosen)
    ref_logp_rejected = get_sequence_logprobs(ref_policy, prompt, rejected)

    # Log-ratio: policy vs reference의 likelihood 비율
    chosen_logratios = pi_logp_chosen - ref_logp_chosen
    rejected_logratios = pi_logp_rejected - ref_logp_rejected

    # DPO objective
    logits = beta * (chosen_logratios - rejected_logratios)
    loss = -F.logsigmoid(logits).mean()

    # 부가 지표
    chosen_rewards = beta * chosen_logratios.detach()
    rejected_rewards = beta * rejected_logratios.detach()
    accuracy = (chosen_rewards > rejected_rewards).float().mean()

    return loss, accuracy
```

---

## 10.5 RLHF vs DPO 비교

| 측면 | RLHF (PPO) | DPO |
|------|-----------|-----|
| **파이프라인** | SFT → RM → PPO (3단계) | SFT → DPO (2단계) |
| **Reward Model** | 별도 학습 필요 | 불필요 (implicit) |
| **강화학습** | PPO 필요 | 불필요 |
| **구현 복잡도** | 높음 | **낮음 (SFT와 유사)** |
| **학습 안정성** | PPO 하이퍼파라미터에 민감 | **안정적** |
| **성능 상한** | on-policy 데이터로 높은 성능 가능 | off-policy 한계 |
| **사용 모델** | InstructGPT, Claude, GPT-4 | Zephyr, Tulu |

---

## 참고 문헌

- Ouyang, L. et al. (2022). Training language models to follow instructions with human feedback. *NeurIPS 2022*. https://arxiv.org/abs/2203.02155
- Schulman, J. et al. (2017). Proximal Policy Optimization Algorithms. https://arxiv.org/abs/1707.06347
- Rafailov, R. et al. (2023). Direct Preference Optimization. *NeurIPS 2023*. https://arxiv.org/abs/2305.18290
- Bradley, R. A. & Terry, M. E. (1952). Rank Analysis of Incomplete Block Designs. *Biometrika*, 39(3/4).
- Bai, Y. et al. (2022). Training a Helpful and Harmless Assistant with RLHF. https://arxiv.org/abs/2204.05862
