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

- $QK^\top$: 토큰 쌍의 유사도(attention score) → shape $(n\times n)$.
- $\sqrt{d_k}$로 나누는 이유: $d_k$가 크면 내적 분산이 커져 softmax가 포화(gradient 소실). 분산을 1로 맞춘다.
- softmax 행별 합 = 1 → 각 토큰의 출력은 $V$ 행들의 가중 평균.
- **temperature**: softmax에 $1/\tau$를 곱하면 $\tau$↓일수록 분포가 뾰족(결정적), $\tau$↑일수록 평탄.

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

- 이미지를 $16\times16$ 패치로 잘라 flatten → 선형사상으로 토큰 임베딩 → `[CLS]` 토큰 + positional embedding → Transformer encoder → `[CLS]`로 분류.
- CNN의 inductive bias(지역성·translation equivariance)가 **없다** → 데이터가 적으면 CNN보다 약하고, **대규모 사전학습**하면 능가.
- **Swin Transformer**: window 안에서만 attention + window를 shift → 계산량 $O(n)$, 계층적 feature(피라미드) → detection/segmentation에 적합.

## 4. CNN vs Transformer

| | CNN | Transformer |
|---|---|---|
| inductive bias | 강함(지역성, 가중치 공유) | 약함(거의 없음) |
| receptive field | 층수에 비례해 성장 | 1층에서 전역 |
| 데이터 요구량 | 상대적으로 적음 | 많음(또는 강한 사전학습) |
| 계산량 | $O(n)$ | self-attn $O(n^2)$ |

## 자주 틀리는 지점

- Q/K/V의 shape와 역할: score $QK^\top$는 $(n\times n)$, 출력은 $V$의 가중합이라 $(n\times d_v)$.
- $\sqrt{d_k}$ 스케일링을 빼면 큰 차원에서 softmax가 포화된다.
- positional encoding을 빼면 Transformer는 입력을 **집합**으로 취급(순서 무시).
- ViT가 "무조건 CNN보다 좋다"가 아니다 — 소규모 데이터에서는 inductive bias가 있는 CNN이 유리.
- decoder의 masked attention은 학습 시 teacher forcing과 짝을 이룬다.

## 복습 질문

- scaled dot-product attention 식을 쓰고 각 항의 shape와 $\sqrt{d_k}$의 이유를 설명하라.
- multi-head가 single-head보다 나은 이유는?
- ViT가 이미지를 어떻게 토큰화하며, CNN 대비 데이터 요구량이 큰 이유는?
- encoder self-attention과 decoder masked self-attention의 차이는?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. Transformers**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.
- 분류, 검출, 분할 모델은 architecture보다 입력 표현, loss, annotation 형식, metric의 차이가 중요하다.
- 3D vision에서는 좌표계, 카메라 모델, homogeneous coordinate, rank/scale ambiguity를 계속 추적해야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **07. Transformers**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Detection & Segmentation](06-detection-and-segmentation.md) · 다음: [08. Generative Models](08-generative-models.md)
