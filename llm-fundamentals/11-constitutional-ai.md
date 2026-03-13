# 11. Constitutional AI (Anthropic)

[← RLHF & Alignment](10-rlhf-alignment.md) | [다음: Inference & Generation →](12-inference-generation.md)

---

## 핵심 접근 (Key Insight)

> **"사람이 아닌 AI가, 명시적 원칙(Constitution)에 따라 스스로를 개선한다."**
>
> RLHF는 강력하지만 두 가지 한계가 있다:
> (1) 사람 annotator가 비싸고 확장이 어렵다
> (2) 사람의 선호가 **암묵적**이어서 무엇이 좋은 응답인지 불투명하다
>
> Bai et al. (2022)의 Constitutional AI(CAI)는 이 두 문제를 동시에 해결한다:
> **문서화된 원칙(Constitution)**을 AI에게 제공하고,
> AI 스스로가 자신의 응답을 비판(critique)하고 수정(revise)하도록 한다.
> 이후 AI가 원칙에 따라 생성한 선호도 데이터로 RL을 수행한다.
>
> 이것이 **RLAIF (RL from AI Feedback)** — RLHF에서 H(Human)을 AI로 대체한 것이다.
> CAI는 helpfulness를 유지하면서 harmlessness를 크게 개선했고,
> 무엇보다 **왜 특정 응답이 좋은지가 원칙으로 명시**되어 투명하다.

---

## 논문 인용

> Bai, Y., Kadavath, S., Kundu, S., Askell, A., Kernion, J., Jones, A., Chen, A., Goldie, A., Mirhoseini, A., McKinnon, C., ... & Kaplan, J. (2022). **Constitutional AI: Harmlessness from AI Feedback.** *arXiv preprint*. https://arxiv.org/abs/2212.08073
>
> Bai, Y., Jones, A., Ndousse, K., Askell, A., Chen, A., DasSarma, N., ... & Kaplan, J. (2022). **Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback.** *arXiv preprint*. https://arxiv.org/abs/2204.05862

---

## 11.1 2단계 프로세스

### Phase 1: Supervised Self-Critique & Revision

```
┌─────────────────────────────────────────────┐
│  Step 1: 초기 모델이 (잠재적으로 유해한)      │
│          응답을 생성                          │
│                                              │
│  Step 2: AI가 Constitution에 따라 자기 비판   │
│          "이 응답은 원칙 X를 위반한다..."      │
│                                              │
│  Step 3: AI가 비판을 바탕으로 응답 수정       │
│          "더 안전하고 도움이 되는 버전은..."    │
│                                              │
│  Step 4: (수정된 응답, 원본 prompt) 쌍으로    │
│          SFT 수행                             │
└─────────────────────────────────────────────┘
```

### Phase 2: RLAIF (RL from AI Feedback)

```
┌─────────────────────────────────────────────┐
│  Step 1: 같은 prompt에 대해 여러 응답 생성   │
│                                              │
│  Step 2: AI가 Constitution에 따라             │
│          어떤 응답이 더 좋은지 판단            │
│          "원칙 X에 따르면 응답 A가 더..."      │
│                                              │
│  Step 3: AI 선호도로 Reward Model 학습        │
│                                              │
│  Step 4: PPO로 최종 모델 학습                 │
└─────────────────────────────────────────────┘
```

---

## 11.2 Constitution 예시

논문에서 사용된 실제 원칙들 (16개 중 일부):

| # | 원칙 | 목적 |
|---|------|------|
| 1 | "Choose the response that is the most helpful, while being safe and avoiding harmful content." | Helpfulness + Safety 균형 |
| 2 | "Choose the assistant response that is as harmless and ethical as possible." | Harmlessness |
| 3 | "Choose the response that sounds most similar to what a peaceful, ethical, and wise person would say." | 윤리적 판단 |
| 4 | "Choose the response that is least likely to be used for illegal or harmful activities." | 악용 방지 |
| 5 | "Which response from the assistant is most helpful while being honest and not harmful?" | 정직성 |

---

## 11.3 코드 (Pseudocode)

### Phase 1: Self-Critique & Revision

```python
CONSTITUTION = [
    "Choose the response that is most helpful while being safe.",
    "Choose the response that is least likely to contain harmful content.",
    "Choose the response that most accurately follows the user's intent.",
    "Choose the response that does the least harm to vulnerable groups.",
]

def critique_and_revise(model, prompt, initial_response, principles=CONSTITUTION):
    """
    CAI Phase 1: AI가 자신의 응답을 원칙에 따라 반복적으로 개선
    (Bai et al., 2022, Section 3.1)
    """
    response = initial_response

    for principle in principles:
        # Step 1: Critique — 원칙 위반 식별
        critique_prompt = (
            f"Human: {prompt}\n"
            f"Assistant: {response}\n\n"
            f"Critique Request: Identify specific ways in which the "
            f"assistant's response violates: \"{principle}\"\n"
            f"Critique:"
        )
        critique = model.generate(critique_prompt)

        # Step 2: Revision — 비판 반영하여 수정
        revision_prompt = (
            f"{critique_prompt} {critique}\n\n"
            f"Revision Request: Please rewrite the response to address "
            f"the critique while maintaining helpfulness.\n"
            f"Revision:"
        )
        response = model.generate(revision_prompt)

    return response  # Constitution을 준수하는 개선된 응답
```

### Phase 2: AI Preference Labeling

```python
def ai_preference_labeling(model, prompt, response_a, response_b, principle):
    """
    CAI Phase 2: AI가 Constitution에 따라 선호도 레이블 생성
    (Bai et al., 2022, Section 3.2)
    사람 annotator 대신 AI가 판단
    """
    eval_prompt = (
        f"Consider the following principle: \"{principle}\"\n\n"
        f"Human: {prompt}\n\n"
        f"Response A: {response_a}\n\n"
        f"Response B: {response_b}\n\n"
        f"Which response better follows the principle? Answer A or B."
    )
    preference = model.generate(eval_prompt)

    # "A" 또는 "B"를 파싱하여 선호도 레이블 반환
    if "A" in preference:
        return response_a, response_b  # chosen, rejected
    else:
        return response_b, response_a


def generate_ai_preference_data(model, prompts, principles):
    """Phase 2에서 AI 선호도 데이터 대규모 생성"""
    preference_data = []
    for prompt in prompts:
        # 여러 응답 생성
        responses = [model.generate(prompt) for _ in range(4)]

        # 모든 쌍에 대해 AI가 선호도 판단
        for i, resp_a in enumerate(responses):
            for resp_b in responses[i+1:]:
                principle = random.choice(principles)
                chosen, rejected = ai_preference_labeling(
                    model, prompt, resp_a, resp_b, principle
                )
                preference_data.append({
                    "prompt": prompt,
                    "chosen": chosen,
                    "rejected": rejected,
                })

    return preference_data
    # → 이 데이터로 Reward Model 학습 → PPO
```

---

## 11.4 CAI vs RLHF 비교

| 측면 | RLHF | Constitutional AI |
|------|------|-------------------|
| **피드백 소스** | 사람 annotator | AI + 명시적 원칙 |
| **확장성** | 사람 비용으로 제한 | **AI로 대규모 생성 가능** |
| **투명성** | 암묵적 선호 (왜 좋은지 불명확) | **원칙이 문서화되어 투명** |
| **편향** | 개별 annotator의 주관적 편향 | 원칙 설계자의 편향 |
| **비용** | annotator 고용 비용 높음 | **AI 추론 비용만 필요** |
| **일관성** | annotator 간 불일치 가능 | 원칙 기반으로 **더 일관적** |
| **최초 적용** | InstructGPT (Ouyang et al., 2022) | **Claude (Bai et al., 2022)** |

---

## 11.5 핵심 실험 결과

논문의 주요 발견:

1. **Harmlessness 개선**: CAI 모델이 RLHF-only 모델보다 유해 응답 비율이 크게 감소
2. **Helpfulness 유지**: harmlessness 개선이 helpfulness를 거의 떨어뜨리지 않음
3. **Scaling**: 더 큰 모델일수록 self-critique & revision 품질이 향상
4. **Chain-of-thought**: 원칙에 대한 reasoning을 포함하면 선호도 레이블 품질이 향상

---

## 참고 문헌

- Bai, Y. et al. (2022a). Constitutional AI: Harmlessness from AI Feedback. https://arxiv.org/abs/2212.08073
- Bai, Y. et al. (2022b). Training a Helpful and Harmless Assistant with RLHF. https://arxiv.org/abs/2204.05862
- Ouyang, L. et al. (2022). Training language models to follow instructions with human feedback. *NeurIPS 2022*. https://arxiv.org/abs/2203.02155
