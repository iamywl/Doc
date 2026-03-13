# 14. 참고 논문 전체 목록

[← Model Comparison](13-model-comparison.md) | [README →](README.md)

---

> 모든 논문은 정식 서지 형식(저자, 연도, 제목, 학회/저널, URL)으로 인용합니다.
> 각 논문의 핵심 기여를 한 줄로 요약합니다.

---

## A. Transformer 아키텍처 (Core Architecture)

### A1. 원본 Transformer
Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). **Attention Is All You Need.** *Advances in Neural Information Processing Systems (NeurIPS)*, 30, pp. 5998-6008.
https://arxiv.org/abs/1706.03762
> Self-Attention만으로 시퀀스를 모델링하는 Transformer 아키텍처 제안.

### A2. Attention의 기원
Bahdanau, D., Cho, K., & Bengio, Y. (2015). **Neural Machine Translation by Jointly Learning to Align and Translate.** *ICLR 2015*.
https://arxiv.org/abs/1409.0473
> Attention 메커니즘의 최초 제안 (additive attention for seq2seq).

### A3. Residual Connection
He, K., Zhang, X., Ren, S., & Sun, J. (2016). **Deep Residual Learning for Image Recognition.** *CVPR 2016*.
https://arxiv.org/abs/1512.03385
> Skip connection으로 매우 깊은 네트워크의 학습을 가능하게 함.

### A4. Pre-Norm Transformer
Xiong, R., Yang, Y., He, J., Zheng, K., Zheng, S., Xing, C., Zhang, H., Lan, Y., Wang, L., & Liu, T. (2020). **On Layer Normalization in the Transformer Architecture.** *ICML 2020*.
https://arxiv.org/abs/2002.04745
> Pre-Norm이 Post-Norm보다 학습이 안정적임을 이론적으로 증명.

---

## B. Positional Encoding

### B1. RoPE
Su, J., Lu, Y., Pan, S., Murtadha, A., Wen, B., & Liu, Y. (2021). **RoFormer: Enhanced Transformer with Rotary Position Embedding.** *arXiv preprint*.
https://arxiv.org/abs/2104.09864
> 회전 행렬로 상대적 위치 정보를 인코딩. LLaMA, PaLM-2, Gemini 등이 채택.

### B2. ALiBi
Press, O., Smith, N. A., & Lewis, M. (2022). **Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation.** *ICLR 2022*.
https://arxiv.org/abs/2108.12409
> Attention score에 linear bias를 더해 위치 인코딩. 길이 외삽 성능 우수.

### B3. Positional Interpolation
Chen, S., Wong, S., Chen, L., & Tian, Y. (2023). **Extending Context Window of Large Language Models via Positional Interpolation.** *arXiv preprint*.
https://arxiv.org/abs/2306.15595
> RoPE의 context length를 학습 없이 확장하는 기법.

---

## C. Attention 변형

### C1. Multi-Query Attention
Shazeer, N. (2019). **Fast Transformer Decoding: One Write-Head is All You Need.** *arXiv preprint*.
https://arxiv.org/abs/1911.02150
> K, V를 모든 head가 공유하여 KV Cache 메모리를 1/h로 절감.

### C2. Grouped-Query Attention
Ainslie, J., Lee-Thorp, J., de Jong, M., Zemlyanskiy, Y., Lebrón, F., & Vaswani, A. (2023). **GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints.** *EMNLP 2023*.
https://arxiv.org/abs/2305.13245
> MHA와 MQA의 중간. K, V를 G개 그룹으로 공유.

### C3. FlashAttention
Dao, T., Fu, D. Y., Ermon, S., Rudra, A., & Ré, C. (2022). **FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness.** *NeurIPS 2022*.
https://arxiv.org/abs/2205.14135
> IO-aware tiling으로 정확한 attention을 2-4배 빠르게 계산.

---

## D. FFN & Normalization

### D1. SwiGLU
Shazeer, N. (2020). **GLU Variants Improve Transformer.** *arXiv preprint*.
https://arxiv.org/abs/2002.05202
> Gated Linear Unit의 Swish 변형. ReLU/GELU 대비 성능 향상.

### D2. GLU (원본)
Dauphin, Y. N., Fan, A., Auli, M., & Grangier, D. (2017). **Language Modeling with Gated Convolutional Networks.** *ICML 2017*.
https://arxiv.org/abs/1612.08083
> Gated Linear Unit의 최초 제안.

### D3. Layer Normalization
Ba, J. L., Kiros, J. R., & Hinton, G. E. (2016). **Layer Normalization.** *arXiv preprint*.
https://arxiv.org/abs/1607.06450
> Batch Normalization의 시퀀스 모델 적용 한계를 해결.

### D4. RMSNorm
Zhang, B., & Sennrich, R. (2019). **Root Mean Square Layer Normalization.** *NeurIPS 2019*.
https://arxiv.org/abs/1910.07467
> Mean-centering 제거로 LayerNorm 대비 ~15% 속도 향상.

### D5. FFN as Key-Value Memory
Geva, M., Schuster, R., Berant, J., & Levy, O. (2021). **Transformer Feed-Forward Layers Are Key-Value Memories.** *EMNLP 2021*.
https://arxiv.org/abs/2012.14913
> FFN이 key-value memory처럼 작동한다는 해석적 연구.

---

## E. Tokenization

### E1. BPE
Sennrich, R., Haddow, B., & Birch, A. (2016). **Neural Machine Translation of Rare Words with Subword Units.** *Proceedings of the 54th Annual Meeting of the ACL*, pp. 1715-1725.
https://arxiv.org/abs/1508.07909
> BPE를 NLP에 최초 적용. Subword tokenization의 시작.

### E2. SentencePiece
Kudo, T., & Richardson, J. (2018). **SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing.** *EMNLP 2018: System Demonstrations*, pp. 66-71.
https://arxiv.org/abs/1808.06226
> 언어 무관한 raw text tokenizer. 완전 가역적 detokenization.

### E3. Subword Regularization
Kudo, T. (2018). **Subword Regularization: Improving Neural Network Translation Models with Multiple Subword Candidates.** *ACL 2018*, pp. 66-75.
https://arxiv.org/abs/1804.10959
> Unigram Language Model 기반 tokenization과 확률적 정규화.

---

## F. OpenAI 계열 모델

### F1. GPT-1
Radford, A., Narasimhan, K., Salimans, T., & Sutskever, I. (2018). **Improving Language Understanding by Generative Pre-Training.** *OpenAI Technical Report*.
> Decoder-only Transformer의 generative pre-training + discriminative fine-tuning.

### F2. GPT-2
Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., & Sutskever, I. (2019). **Language Models are Unsupervised Multitask Learners.** *OpenAI Technical Report*.
> 1.5B 파라미터. Zero-shot 태스크 수행 가능성 시연.

### F3. GPT-3
Brown, T. B., Mann, B., Ryder, N., Subbiah, M., Kaplan, J., Dhariwal, P., Neelakantan, A., Shyam, P., Sastry, G., Askell, A., ... & Amodei, D. (2020). **Language Models are Few-Shot Learners.** *Advances in Neural Information Processing Systems (NeurIPS)*, 33, pp. 1877-1901.
https://arxiv.org/abs/2005.14165
> 175B 파라미터. In-context learning의 발견. Few-shot prompting 패러다임 확립.

### F4. InstructGPT
Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C., Mishkin, P., Zhang, C., Agarwal, S., Slama, K., Ray, A., ... & Lowe, R. (2022). **Training language models to follow instructions with human feedback.** *Advances in Neural Information Processing Systems (NeurIPS)*, 35, pp. 27730-27744.
https://arxiv.org/abs/2203.02155
> SFT + Reward Model + PPO의 RLHF 파이프라인. 1.3B InstructGPT > 175B GPT-3.

---

## G. Google / DeepMind 계열 모델

### G1. PaLM
Chowdhery, A., Narang, S., Devlin, J., Bosma, M., Mishra, G., Roberts, A., ... & Fiedel, N. (2022). **PaLM: Scaling Language Modeling with Pathways.** *arXiv preprint*.
https://arxiv.org/abs/2204.02311
> 540B 파라미터. Pathways 시스템으로 6144 TPU v4에서 학습.

### G2. PaLM-2
Anil, R., Dai, A. M., Firat, O., Johnson, M., Lepikhin, D., Passos, A., ... & Wu, Y. (2023). **PaLM 2 Technical Report.** *arXiv preprint*.
https://arxiv.org/abs/2305.10403
> PaLM 후속. Compute-optimal training, 향상된 다국어 능력.

### G3. Gemini
Gemini Team, Anil, R., Borgeaud, S., Wu, Y., Alayrac, J.-B., Yu, J., ... & Vinyals, O. (2023). **Gemini: A Family of Highly Capable Multimodal Models.** *arXiv preprint*.
https://arxiv.org/abs/2312.11805
> Native multimodal LLM. 텍스트, 이미지, 오디오, 비디오 통합 처리.

---

## H. Anthropic 계열

### H1. Constitutional AI
Bai, Y., Kadavath, S., Kundu, S., Askell, A., Kernion, J., Jones, A., Chen, A., Goldie, A., Mirhoseini, A., McKinnon, C., Chen, C., Olsson, C., Olah, C., Hernandez, D., Drain, D., Ganguli, D., Li, D., ... & Kaplan, J. (2022). **Constitutional AI: Harmlessness from AI Feedback.** *arXiv preprint*.
https://arxiv.org/abs/2212.08073
> RLAIF. AI가 명시적 원칙에 따라 자기 개선. Claude의 alignment 기반.

### H2. Helpful & Harmless RLHF
Bai, Y., Jones, A., Ndousse, K., Askell, A., Chen, A., DasSarma, N., Drain, D., Fort, S., Ganguli, D., Henighan, T., ... & Kaplan, J. (2022). **Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback.** *arXiv preprint*.
https://arxiv.org/abs/2204.05862
> RLHF로 helpfulness와 harmlessness를 동시에 최적화하는 방법론.

---

## I. Meta 계열

### I1. LLaMA
Touvron, H., Lavril, T., Izacard, G., Martinet, X., Lachaux, M.-A., Lacroix, T., ... & Lample, G. (2023). **LLaMA: Open and Efficient Foundation Language Models.** *arXiv preprint*.
https://arxiv.org/abs/2302.13971
> 13B LLaMA ≈ 175B GPT-3. Chinchilla-optimal. 오픈소스 LLM 생태계 촉발.

### I2. LLaMA-2
Touvron, H., Martin, L., Stone, K., Albert, P., Almahairi, A., Babaei, Y., ... & Scialom, T. (2023). **Llama 2: Open Foundation and Fine-Tuned Chat Models.** *arXiv preprint*.
https://arxiv.org/abs/2307.09288
> 2T 토큰 학습, GQA 도입, RLHF 적용 chat 모델 공개.

---

## J. Scaling Laws

### J1. OpenAI Scaling Laws
Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., Gray, S., Radford, A., Wu, J., & Amodei, D. (2020). **Scaling Laws for Neural Language Models.** *arXiv preprint*.
https://arxiv.org/abs/2001.08361
> Loss가 N, D, C와 power-law 관계. 모델 크기 우선 전략.

### J2. Chinchilla
Hoffmann, J., Borgeaud, S., Mensch, A., Buchatskaya, E., Cai, T., Rutherford, E., Casas, D. de L., Hendricks, L. A., Welbl, J., Clark, A., ... & Sifre, L. (2022). **Training Compute-Optimal Large Language Models.** *NeurIPS 2022*.
https://arxiv.org/abs/2203.15556
> D ≈ 20N이 compute-optimal. Kaplan 법칙 수정.

### J3. Inference-Optimal Scaling
Sardana, N., & Frankle, J. (2023). **Beyond Chinchilla-Optimal: Accounting for Inference in Language Model Scaling Laws.** *arXiv preprint*.
https://arxiv.org/abs/2401.00448
> 추론 비용까지 고려하면 작은 모델을 더 오래 학습하는 것이 총비용 최적.

---

## K. Alignment

### K1. PPO
Schulman, J., Wolski, F., Dhariwal, P., Radford, A., & Klimov, O. (2017). **Proximal Policy Optimization Algorithms.** *arXiv preprint*.
https://arxiv.org/abs/1707.06347
> Clipped surrogate objective로 안정적 policy gradient. RLHF의 핵심 알고리즘.

### K2. DPO
Rafailov, R., Sharma, A., Mitchell, E., Ermon, S., Manning, C. D., & Finn, C. (2023). **Direct Preference Optimization: Your Language Model is Secretly a Reward Model.** *NeurIPS 2023*.
https://arxiv.org/abs/2305.18290
> RM+PPO 없이 직접 preference 학습. 구현 단순, 학습 안정적.

### K3. Nucleus Sampling
Holtzman, A., Buys, J., Du, L., Forbes, M., & Choi, Y. (2020). **The Curious Case of Neural Text Degeneration.** *ICLR 2020*.
https://arxiv.org/abs/1904.09751
> Top-p sampling 제안. 동적으로 후보 토큰 수를 조절.

---

## L. Emergent Abilities & Analysis

### L1. Emergent Abilities
Wei, J., Tay, Y., Bommasani, R., Raffel, C., Zoph, B., Borgeaud, S., Yogatama, D., Bosma, M., Zhou, D., Metzler, D., Chi, E. H., Hashimoto, T., Vinyals, O., Liang, P., Dean, J., & Fedus, W. (2022). **Emergent Abilities of Large Language Models.** *Transactions on Machine Learning Research (TMLR)*.
https://arxiv.org/abs/2206.07682
> 특정 모델 크기를 넘으면 이전에 없던 능력이 급격히 출현하는 현상.

### L2. Attention Analysis
Clark, K., Khandelwal, U., Levy, O., & Manning, C. D. (2019). **What Does BERT Look At? An Analysis of BERT's Attention.** *BlackboxNLP Workshop at ACL 2019*.
https://arxiv.org/abs/1906.04341
> 각 attention head가 서로 다른 언어적 관계를 학습한다는 분석.

---

## M. Inference 최적화

### M1. Speculative Decoding
Leviathan, Y., Kalman, M., & Matias, Y. (2023). **Fast Inference from Transformers via Speculative Decoding.** *ICML 2023*.
https://arxiv.org/abs/2211.17192
> 작은 draft 모델로 여러 토큰 예측 후 큰 모델로 검증. 2-3배 속도 향상.

### M2. PagedAttention
Kwon, W., Li, Z., Zhuang, S., Sheng, Y., Zheng, L., Yu, C. H., Gonzalez, J. E., Zhang, H., & Stoica, I. (2023). **Efficient Memory Management for Large Language Model Serving with PagedAttention.** *SOSP 2023*.
https://arxiv.org/abs/2309.06180
> OS의 virtual memory 기법을 KV Cache에 적용. vLLM의 핵심 기술.

---

> 총 **30편**의 논문을 인용하였으며, 모든 URL은 arXiv 또는 공식 출처를 기준으로 합니다.
