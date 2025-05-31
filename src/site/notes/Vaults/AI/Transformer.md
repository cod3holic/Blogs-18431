---
{"dg-publish":true,"dg-path":"AI/Transformer.md","permalink":"/ai/transformer/","created":"2025-05-31","updated":"2025-05-31"}
---

Transformer는 2017년 Google Research의 "[[Vaults/Papers/Attention is All You Need\|Attention is All You Need]]" 논문에서 제안된 모델로, 기존 [[Vaults/AI/Recurrent Neural Network\|RNN]] 기반 시퀀스 모델의 한계를 극복하기 위해 등장했다. 핵심은 **[[Vaults/AI/Attention\|Attention]] 메커니즘**을 활용해 입력 시퀀스 전체를 한 번에 처리하고, 병렬 연산이 가능하다는 점이다.

## 1. 전체 구조

Transformer는 크게 [[Vaults/AI/Transformer Encoder\|Encoder]]와 [[Vaults/AI/Transformer Decoder\|Decoder]] 두 부분으로 나뉜다.

- **Encoder**: 입력 시퀀스를 받아 정보를 추상화한다.
- **Decoder**: Encoder의 정보를 바탕으로 출력 시퀀스를 생성한다.

각각 여러 개의 동일한 레이어(블록)로 구성되어 있다.

---

## 2. 입력 처리

- **Token [[Vaults/AI/Embedding\|Embedding]]**: 각 입력 토큰(단어 등)을 고차원 벡터로 변환한다.
- **Positional Encoding**: 시퀀스 내에서 각 토큰의 위치 정보를 더해준다. (사인/코사인 함수 기반)
- (선택) **Segment Embedding**: 두 개 이상의 시퀀스를 구분할 때 사용한다.

---

## 3. Encoder 구조

Encoder는 N개의 동일한 블록으로 구성된다(논문에서는 N=6).

각 블록은 다음과 같은 순서로 동작한다:

1. **Multi-Head Self-Attention**
   - 입력 시퀀스의 각 토큰이 다른 모든 토큰과의 관계(유사도)를 계산한다.
   - 여러 개의 Attention Head를 사용해 다양한 관점에서 정보를 추출한다.
2. **Add & Layer Normalization**
   - 입력과 Attention 출력을 더하고, Layer Normalization을 적용한다.
3. **Position-wise Feed-Forward Network (FFN)**
   - 각 토큰별로 동일한 2층의 완전연결 신경망을 적용한다.
4. **Add & Layer Normalization**
   - FFN의 출력과 이전 출력을 더하고, 다시 Layer Normalization을 적용한다.

---

## 4. Decoder 구조

Decoder 역시 N개의 동일한 블록으로 구성된다.

각 블록은 다음과 같은 순서로 동작한다:

1. **Masked Multi-Head Self-Attention**
   - 현재 시점까지의 출력만을 참고하도록 미래 토큰을 마스킹한다.
2. **Add & Layer Normalization**
3. **Multi-Head Attention (Encoder-Decoder Attention)**
   - Encoder의 출력과 Decoder의 출력을 결합해, 입력 시퀀스의 특정 부분을 참고할 수 있게 한다.
4. **Add & Layer Normalization**
5. **Position-wise Feed-Forward Network (FFN)**
6. **Add & Layer Normalization**

---

## 5. Attention 메커니즘

- **Self-Attention**: 입력 시퀀스 내에서 각 토큰이 다른 토큰과 얼마나 관련 있는지 가중치를 계산한다.
- **Multi-Head Attention**: 여러 개의 Self-Attention을 병렬로 수행해 다양한 관계를 학습한다.

---

## 6. 출력

Decoder의 마지막 출력은 **Linear Layer**와 **Softmax**를 거쳐, 각 토큰이 어휘 집합에서 어떤 단어일지 확률로 변환된다.

---

## 7. 추가 정보

- **Residual Connection**: 각 서브레이어(Attention, FFN) 뒤에 입력을 더해준다. (학습 안정화)
- **Layer Normalization**: 각 서브레이어 뒤에 적용해 학습을 돕는다.
- **Position-wise FFN**: 각 토큰별로 동일한 FFN을 적용한다.

---

## 8. 장점 및 한계

- **장점**: 병렬 연산 가능, 장기 의존성 문제 해결, 다양한 시퀀스 작업에 적용 가능
- **한계**: 입력 길이가 길어질수록 연산량이 급격히 증가(메모리, 속도 문제)

---

## 9. Transformer의 학습 방식

### Teacher Forcing
- 디코더가 다음 토큰을 예측할 때, 실제 정답(ground truth) 토큰을 이전 출력으로 사용하여 학습한다.
- 예를 들어 번역 문제라면, 영어 문장(입력)과 한국어 번역문(정답)을 함께 넣어 디코더가 올바른 다음 단어를 예측하도록 한다.

### Loss Function
- 일반적으로 Cross-Entropy Loss를 사용한다.
- 각 시점별로 예측한 단어와 실제 정답 단어의 확률 분포 차이를 계산해 전체 시퀀스에 대해 손실을 구한다.

---

## 10. Transformer의 장점과 한계

### 장점
- **병렬 연산**: RNN과 달리 모든 토큰을 동시에 처리할 수 있어 학습 속도가 빠르다.
- **장기 의존성 해결**: Self-Attention을 통해 먼 거리의 토큰 관계도 잘 학습한다.
- **확장성**: 다양한 자연어 처리(NLP) 및 시퀀스 작업에 적용 가능하다.

### 한계
- **메모리 사용량**: 입력 시퀀스 길이가 길어질수록 연산량과 메모리 사용량이 급격히 증가한다($O(n^2)$).
- **순서 정보의 한계**: Positional Encoding만으로는 완벽한 순서 정보를 학습하기 어렵다는 지적도 있다.

---

## 11. Transformer의 발전 및 응용

- **BERT, GPT, T5 등**: Transformer 구조를 기반으로 다양한 사전학습(pretraining) 모델이 등장했다.
- **비전, 음성 등**: 자연어 처리뿐 아니라 이미지, 음성 등 다양한 도메인에도 적용되고 있다.
- **효율화 연구**: Longformer, Performer 등 입력 길이에 따른 연산량을 줄이기 위한 다양한 변형 모델이 개발되고 있다.

---

## 12. 전체 구조 요약 다이어그램

![Pasted image 20250531191720.png](/img/user/Attachments/Pasted%20image%2020250531191720.png)

---

[^1]: Vaswani et al., "Attention is All You Need", 2017
[^2]: [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)
[^3]: [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/abs/1810.04805)
[^4]: [GPT: Improving Language Understanding by Generative Pre-Training](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf)
