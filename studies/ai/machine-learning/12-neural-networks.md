---
layout: page
title: "12. 신경망 (기초 · Backpropagation · 실전 학습)"
permalink: /studies/ai/machine-learning/12-neural-networks/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `17(Part 1)` + `18(Part 2)` + `19(Part 3)` 통합·보강
- 표준 자료(CS231n) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

Part 1 — linear classifier의 한계, perceptron/sigmoid neuron, hidden layer, XOR, activation.
Part 2 — computational graph에서 **chain rule**로 gradient 계산(backpropagation), gate별 규칙, matrix backward.
Part 3 — 실전 optimization(learning rate·momentum·Adam·mini-batch)과 regularization(augmentation·L2·dropout·early stopping).

---

# Part 1. 다층 신경망 기초

## 1. Linear classifier의 한계

$\hat y = \operatorname{sign}(w^\top x)$ — 선형 경계뿐. XOR·원형 경계는 표현 불가.

**Feature transform**: $\text{model}(x,w) = w_0 + w_1 x_1^2 + w_2 x_2^2$ 처럼 입력을 변환하면 linear model도 비선형 경계. 신경망 = 이 nonlinear feature를 **데이터에서 학습**.

## 2. Perceptron / Sigmoid neuron

$$
z = w_0 + \sum_j w_j x_j,\qquad \text{output} = g(z)
$$
$g$ = sigmoid $\sigma(z) = 1/(1+e^{-z})$ 등. sigmoid는 [0,1] 출력(확률 해석) + 미분 가능.

## 3. XOR과 Hidden Layer

perceptron은 AND/OR(linearly separable)은 되지만 **XOR은 단일 선형 경계로 불가**. hidden layer가 중간 feature를 만들어 표현 가능:
$$
h_k = g(w_k^\top x),\qquad \text{output} = g(v^\top h)
$$
각 hidden unit = feature detector.

## 4. Activation은 반드시 비선형

모든 layer가 선형이면 여러 선형변환의 곱 = 하나의 선형변환. 대표: step, sigmoid, tanh, ReLU.

---

# Part 2. Backpropagation

## 5. Computational Graph + Chain Rule

$$
\frac{\partial L}{\partial x} = \frac{\partial L}{\partial z}\cdot\frac{\partial z}{\partial x}
\qquad\Longleftrightarrow\qquad
\text{downstream} = \text{upstream} \times \text{local}
$$

### Gate별 backward
| gate | forward | backward |
|---|---|---|
| **add** $z = x+y$ | — | $\partial L/\partial x = \partial L/\partial y = \partial L/\partial z$ (그대로 분배) |
| **multiply** $z = xy$ | — | $\partial L/\partial x = (\partial L/\partial z)\,y$ (상대 입력값 곱) |
| **max** $z = \max(x,y)$ | — | gradient가 argmax 입력으로만 |
| **sigmoid** $s = \sigma(x)$ | $s$ 저장 | $\partial s/\partial x = s(1-s)$ |
| **ReLU** $\max(0,x)$ | — | $x>0$이면 통과, else 0 |

## 6. Vector / Matrix Backward

**원칙**: 어떤 변수에 대한 loss gradient는 그 변수와 **같은 shape**. $W$가 $N\times M$이면 $\partial L/\partial W$도 $N\times M$.

$$
Y = XW \;\Longrightarrow\;
\frac{\partial L}{\partial X} = \frac{\partial L}{\partial Y} W^\top,\qquad
\frac{\partial L}{\partial W} = X^\top \frac{\partial L}{\partial Y}
$$
fully connected layer backward의 핵심. batch $X$로 vectorize하면 모든 sample gradient를 행렬 연산으로 한 번에.

## 7. Modular forward/backward API
```
forward(input)  -> output, cache
backward(dupstream, cache) -> dinput, dparams
```
cache에 backward에 필요한 forward 중간값 저장(메모리 ↔ 재계산 trade-off).

---

# Part 3. 실전 학습과 정규화

## 8. Optimization

다층 신경망 loss surface는 **non-convex**(local minima, saddle point, plateau).

- **Learning rate**: 너무 작음 → 느림/plateau, 너무 큼 → 발산. 여러 값 시도 후 validation으로.
- **Momentum**: $v \leftarrow \rho v - \eta\nabla,\ w \leftarrow w + v$ — 일관된 방향엔 가속, 지그재그 진동 감소.
- **Adaptive**(Adagrad/RMSProp/Adam): parameter별 gradient 통계로 유효 learning rate 조절(기본 lr은 여전히 tuning).
- **LR decay**: 초반 크게 → 후반 작게(optimum 근처 진동↓).
- **Mini-batch SGD**: full batch와 SGD의 중간 — gradient noise ↔ 계산 효율 균형, 신경망 학습의 표준.

## 9. Activation 비교

| | 범위 | 문제 |
|---|---|---|
| sigmoid | [0,1] | 큰 입력에서 saturation(gradient≈0), not zero-centered |
| tanh | [-1,1] | zero-centered(개선), 여전히 saturation |
| **ReLU** $\max(0,x)$ | [0,∞) | gradient 소실 완화·빠름, 음수 영역 "dead ReLU" |

## 10. Regularization

- **Data augmentation**: translation/rotation/crop/color jitter — label 유지 변형.
- **L2**: $\text{loss} + \lambda\lVert W\rVert_2^2$ — weight 크기 억제.
- **Dropout**: training forward마다 확률 $p$로 neuron 출력을 0. 특정 neuron 조합 의존↓, subnetwork 앙상블 효과, redundant representation. **test time엔 전부 사용 → activation scale 보정**(inverted dropout).
- **Early stopping**: validation 성능이 정체/악화되면 중단.

## 복습 질문

- linear classifier가 XOR를 못 하는 이유와, hidden layer가 해결하는 방식은?
- add/multiply/max gate의 backward 규칙, 그리고 $Y=XW$의 $\partial L/\partial X$, $\partial L/\partial W$는?
- forward/backward API에서 cache가 필요한 이유는?
- momentum·Adam의 직관, dropout·early stopping이 overfitting을 줄이는 원리는?
{% endraw %}

---

이전: [11. 차원 축소 (PCA · LDA)](11-dimension-reduction-pca-lda.md) · 다음: [13. CNN (합성곱 신경망)](13-convolutional-neural-networks.md)
