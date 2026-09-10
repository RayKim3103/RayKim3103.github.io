---
layout: page
title: "07. Transformers"
permalink: /studies/ai/computer-vision/07-transformers/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `07` 보강
- 표준 자료(Attention Is All You Need · CS231n · ViT 논문) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

RNN은 시퀀스를 순차 처리해 (1) 장거리 의존성이 약해지고 (2) 병렬화가 어렵다. Transformer는 이를 **self-attention**으로 대체한다: 모든 위치가 모든 위치를 한 번에 참조한다. NLP에서 시작해 vision(ViT, Swin)으로 확장됐다.

## 1. Self-Attention

각 토큰 임베딩에서 Query·Key·Value를 선형사상으로 만든다.

$$
Q = XW_Q,\quad K = XW_K,\quad V = XW_V
$$

**Scaled dot-product attention**:

$$
\text{Attn}(Q,K,V) = \operatorname{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right) V
$$

- $$QK^\top$$: 토큰 쌍의 유사도(attention score) → shape $$(n\times n)$$.
- $$\sqrt{d_k}$$로 나누는 이유: $$d_k$$가 크면 내적 분산이 커져 softmax가 포화(gradient 소실). 분산을 1로 맞춘다.
- softmax 행별 합 = 1 → 각 토큰의 출력은 $$V$$ 행들의 가중 평균.
- **temperature**: softmax에 $$1/\tau$$를 곱하면 $$\tau$$↓일수록 분포가 뾰족(결정적), $$\tau$$↑일수록 평탄.

### Multi-head attention
$$
\text{MHA}(X) = [\,\text{head}_1;\dots;\text{head}_h\,]\,W_O,\qquad
\text{head}_i = \text{Attn}(XW_Q^i, XW_K^i, XW_V^i)
$$
여러 부분공간에서 서로 다른 관계(문법/의미/위치 등)를 병렬로 학습.

## 2. 블록 구성

$$
\begin{aligned}
z &= x + \text{MHA}(\text{LN}(x)) \\
y &= z + \text{MLP}(\text{LN}(z))
\end{aligned}
$$

- **residual + LayerNorm**: 깊은 스택 학습 안정화(BN이 아니라 LN — 시퀀스 길이·배치에 무관).
- **positional encoding**: attention은 순서를 모른다 → 위치 정보를 더해줌(사인함수 고정식 또는 학습형).
- **encoder–decoder**: 번역 등. decoder는 (1) masked self-attention(미래 토큰 차단, autoregressive) + (2) cross-attention(encoder 출력을 K,V로).

## 3. Vision Transformer (ViT)

- 이미지를 $$16\times16$$ 패치로 잘라 flatten → 선형사상으로 토큰 임베딩 → `[CLS]` 토큰 + positional embedding → Transformer encoder → `[CLS]`로 분류.
- CNN의 inductive bias(지역성·translation equivariance)가 **없다** → 데이터가 적으면 CNN보다 약하고, **대규모 사전학습**하면 능가.
- **Swin Transformer**: window 안에서만 attention + window를 shift → 계산량 $$O(n)$$, 계층적 feature(피라미드) → detection/segmentation에 적합.

## 4. CNN vs Transformer

| | CNN | Transformer |
|---|---|---|
| inductive bias | 강함(지역성, 가중치 공유) | 약함(거의 없음) |
| receptive field | 층수에 비례해 성장 | 1층에서 전역 |
| 데이터 요구량 | 상대적으로 적음 | 많음(또는 강한 사전학습) |
| 계산량 | $$O(n)$$ | self-attn $$O(n^2)$$ |

## 자주 틀리는 지점

- Q/K/V의 shape와 역할: score $$QK^\top$$는 $$(n\times n)$$, 출력은 $$V$$의 가중합이라 $$(n\times d_v)$$.
- $$\sqrt{d_k}$$ 스케일링을 빼면 큰 차원에서 softmax가 포화된다.
- positional encoding을 빼면 Transformer는 입력을 **집합**으로 취급(순서 무시).
- ViT가 "무조건 CNN보다 좋다"가 아니다 — 소규모 데이터에서는 inductive bias가 있는 CNN이 유리.
- decoder의 masked attention은 학습 시 teacher forcing과 짝을 이룬다.

## 복습 질문

- scaled dot-product attention 식을 쓰고 각 항의 shape와 $$\sqrt{d_k}$$의 이유를 설명하라.
- multi-head가 single-head보다 나은 이유는?
- ViT가 이미지를 어떻게 토큰화하며, CNN 대비 데이터 요구량이 큰 이유는?
- encoder self-attention과 decoder masked self-attention의 차이는?

{% endraw %}

---

이전: [06. Detection & Segmentation](06-detection-and-segmentation.md) · 다음: [08. Generative Models](08-generative-models.md)
