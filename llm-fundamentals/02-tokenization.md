# 02. Tokenization (토큰화)

[← 개요](01-overview.md) | [다음: Embedding →](03-embedding-positional-encoding.md)

---

## 핵심 접근 (Key Insight)

> **"단어 단위도, 문자 단위도 아닌 그 중간 — Subword가 최적이다."**
>
> 단어 단위 tokenization은 어휘 크기가 폭발하고 미등록어(OOV) 문제가 발생한다.
> 문자 단위는 시퀀스가 너무 길어진다.
> BPE(Sennrich et al., 2016)는 **빈도 기반 병합**으로 이 딜레마를 해결했다:
> 자주 등장하는 문자 조합은 하나의 토큰으로 병합하고, 드문 단어는 subword로 분해하여
> 고정된 vocabulary로 모든 텍스트를 표현할 수 있게 했다.
> 이 아이디어는 사실 1994년 Philip Gage의 데이터 압축 알고리즘에서 유래했으며,
> Sennrich et al.이 이를 NLP에 최초로 적용했다.

---

## 2.1 Byte Pair Encoding (BPE)

### 논문 인용

> Sennrich, R., Haddow, B., & Birch, A. (2016). **Neural Machine Translation of Rare Words with Subword Units.** *Proceedings of the 54th Annual Meeting of the Association for Computational Linguistics (ACL)*, pp. 1715-1725. https://arxiv.org/abs/1508.07909
>
> 원본: Gage, P. (1994). **A New Algorithm for Data Compression.** *The C Users Journal*, 12(2), pp. 23-38.

### 알고리즘

1. 초기 vocabulary = 모든 개별 문자(또는 바이트, 256개)
2. 코퍼스에서 가장 빈번한 인접 토큰 쌍을 찾음
3. 해당 쌍을 새로운 토큰으로 병합
4. 원하는 vocabulary 크기에 도달할 때까지 2-3 반복

### 코드 구현

```python
# BPE 알고리즘 구현 (simplified)
# Reference: Karpathy's minbpe (https://github.com/karpathy/minbpe)

def get_stats(ids):
    """인접 토큰 쌍의 빈도수를 계산"""
    counts = {}
    for pair in zip(ids, ids[1:]):
        counts[pair] = counts.get(pair, 0) + 1
    return counts

def merge(ids, pair, idx):
    """가장 빈번한 쌍을 새로운 토큰으로 병합"""
    new_ids = []
    i = 0
    while i < len(ids):
        if i < len(ids) - 1 and ids[i] == pair[0] and ids[i+1] == pair[1]:
            new_ids.append(idx)
            i += 2
        else:
            new_ids.append(ids[i])
            i += 1
    return new_ids

def train_bpe(text, vocab_size):
    """BPE 학습: vocab_size까지 병합 반복"""
    tokens = list(text.encode("utf-8"))  # 바이트 단위로 시작
    num_merges = vocab_size - 256        # 256개 바이트 토큰 기본
    merges = {}

    for i in range(num_merges):
        stats = get_stats(tokens)
        if not stats:
            break
        best_pair = max(stats, key=stats.get)
        idx = 256 + i
        tokens = merge(tokens, best_pair, idx)
        merges[best_pair] = idx

    return merges

# 예시
text = "the cat sat on the mat the cat ate the rat"
merges = train_bpe(text, vocab_size=280)
print(f"Learned {len(merges)} merge rules")
```

### 동작 예시

```
초기:     t h e _ c a t _ s a t _ o n _ t h e _ m a t
Step 1:   th e _ c a t _ s a t _ o n _ th e _ m a t     (t+h → th, 빈도 3)
Step 2:   the _ c a t _ s a t _ o n _ the _ m a t       (th+e → the, 빈도 3)
Step 3:   the _ c at _ s at _ o n _ the _ m at           (a+t → at, 빈도 3)
...
```

---

## 2.2 SentencePiece

### 논문 인용

> Kudo, T., & Richardson, J. (2018). **SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing.** *Proceedings of the 2018 Conference on Empirical Methods in Natural Language Processing (EMNLP): System Demonstrations*, pp. 66-71. https://arxiv.org/abs/1808.06226
>
> Kudo, T. (2018). **Subword Regularization: Improving Neural Network Translation Models with Multiple Subword Candidates.** *Proceedings of the 56th Annual Meeting of the ACL*, pp. 66-75. https://arxiv.org/abs/1804.10959

### 핵심 접근 — BPE와의 차이

| 특성 | BPE (Sennrich) | SentencePiece (Kudo) |
|------|----------------|----------------------|
| 전처리 | whitespace tokenization 필요 | **raw text 직접 처리** |
| 언어 의존성 | 영어 중심 (공백 기반) | **언어 무관** (일본어, 한국어 등) |
| Word boundary | 공백으로 구분 | `▁` (U+2581) 특수 문자 사용 |
| 알고리즘 | BPE only | **BPE + Unigram LM** 선택 가능 |
| 가역성 | 복원 어려움 | **완전 가역적** detokenization |

### 코드 구현

```python
import sentencepiece as spm

# 학습
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='tokenizer',
    vocab_size=32000,
    model_type='bpe'  # 또는 'unigram'
)

# 사용
sp = spm.SentencePieceProcessor(model_file='tokenizer.model')

tokens = sp.encode("Hello, how are you?", out_type=str)
# ['▁Hello', ',', '▁how', '▁are', '▁you', '?']
# ▁는 단어 시작(공백 위치)을 표시

ids = sp.encode("Hello, how are you?", out_type=int)
# [8774, 6, 579, 418, 340, 60]

# 완전한 역변환 가능
text = sp.decode(ids)
# "Hello, how are you?"
```

---

## 2.3 모델별 Tokenizer 비교

| 모델 | Tokenizer | Vocab Size | 특징 |
|------|-----------|------------|------|
| GPT-2 | Byte-level BPE | 50,257 | 바이트 단위 시작, OOV 없음 |
| GPT-4 | tiktoken (BPE) | ~100,000 | 다국어 효율 개선 |
| LLaMA / LLaMA-2 | SentencePiece (BPE) | 32,000 | 경량 vocab |
| PaLM | SentencePiece | 256,000 | 대형 vocab으로 다국어 커버 |
| Claude | SentencePiece 계열 | 비공개 | — |
| Gemini | SentencePiece | 256,000 | PaLM 계승 |

---

## 참고 문헌

- Gage, P. (1994). A New Algorithm for Data Compression. *The C Users Journal*, 12(2), pp. 23-38.
- Sennrich, R., Haddow, B., & Birch, A. (2016). Neural Machine Translation of Rare Words with Subword Units. *ACL 2016*. https://arxiv.org/abs/1508.07909
- Kudo, T. (2018). Subword Regularization: Improving Neural Network Translation Models with Multiple Subword Candidates. *ACL 2018*. https://arxiv.org/abs/1804.10959
- Kudo, T., & Richardson, J. (2018). SentencePiece: A simple and language independent subword tokenizer. *EMNLP 2018*. https://arxiv.org/abs/1808.06226
