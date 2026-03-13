# 01. 전체 아키텍처 개요

[← README](README.md) | [다음: Tokenization →](02-tokenization.md)

---

## 핵심 접근 (Key Insight)

> **LLM의 본질은 "확률적 다음 단어 예측기"이다.**
> 거대한 텍스트 코퍼스에서 "주어진 문맥 다음에 올 가장 적절한 토큰"을 예측하도록 학습하면,
> 그 과정에서 문법, 사실 관계, 추론 능력까지 자연스럽게 습득한다는 것이
> GPT 계열 연구의 핵심 발견이다 (Radford et al., 2018; Brown et al., 2020).

---

## 동작 흐름

```
[Raw Text] → [Tokenization] → [Embedding] → [Transformer Layers × N] → [Output Probabilities]
```

LLM의 동작은 크게 **5단계**로 구분된다:

| # | 단계 | 설명 | 핵심 논문 |
|---|------|------|-----------|
| 1 | **Tokenization** | 텍스트를 subword 단위로 분할 | Sennrich et al., 2016 [^1]; Kudo & Richardson, 2018 [^2] |
| 2 | **Embedding** | 토큰을 고차원 벡터로 변환 + 위치 정보 주입 | Vaswani et al., 2017 [^3] |
| 3 | **Transformer** | Self-Attention + FFN을 N번 반복 | Vaswani et al., 2017 [^3] |
| 4 | **Pre-training** | 대규모 코퍼스에서 Next Token Prediction | Brown et al., 2020 [^4] |
| 5 | **Alignment** | RLHF / Constitutional AI로 인간 선호에 정렬 | Ouyang et al., 2022 [^5]; Bai et al., 2022 [^6] |

---

## 왜 이 구조가 작동하는가?

1. **Self-Attention**: 문장 내 모든 토큰 쌍의 관계를 병렬로 계산 → 장거리 의존성 포착 (Vaswani et al., 2017 [^3])
2. **Scaling**: 모델·데이터·연산량을 키우면 예측 가능한 비율로 성능이 향상 (Kaplan et al., 2020 [^7])
3. **Alignment**: Pre-training만으로는 유용하지 않음 → RLHF/CAI로 "도움되고 안전한" 응답 생성 (Ouyang et al., 2022 [^5])

---

## 참고 문헌

[^1]: Sennrich, R., Haddow, B., & Birch, A. (2016). Neural Machine Translation of Rare Words with Subword Units. *Proceedings of the 54th Annual Meeting of the Association for Computational Linguistics (ACL)*, pp. 1715-1725. https://arxiv.org/abs/1508.07909

[^2]: Kudo, T., & Richardson, J. (2018). SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing. *Proceedings of the 2018 Conference on Empirical Methods in Natural Language Processing (EMNLP): System Demonstrations*, pp. 66-71. https://arxiv.org/abs/1808.06226

[^3]: Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). Attention Is All You Need. *Advances in Neural Information Processing Systems (NeurIPS)*, 30, pp. 5998-6008. https://arxiv.org/abs/1706.03762

[^4]: Brown, T. B., Mann, B., Ryder, N., Subbiah, M., Kaplan, J., Dhariwal, P., ... & Amodei, D. (2020). Language Models are Few-Shot Learners. *Advances in Neural Information Processing Systems (NeurIPS)*, 33, pp. 1877-1901. https://arxiv.org/abs/2005.14165

[^5]: Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C., Mishkin, P., ... & Lowe, R. (2022). Training language models to follow instructions with human feedback. *Advances in Neural Information Processing Systems (NeurIPS)*, 35, pp. 27730-27744. https://arxiv.org/abs/2203.02155

[^6]: Bai, Y., Kadavath, S., Kundu, S., Askell, A., Kernion, J., Jones, A., ... & Kaplan, J. (2022). Constitutional AI: Harmlessness from AI Feedback. *arXiv preprint*. https://arxiv.org/abs/2212.08073

[^7]: Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., ... & Amodei, D. (2020). Scaling Laws for Neural Language Models. *arXiv preprint*. https://arxiv.org/abs/2001.08361
