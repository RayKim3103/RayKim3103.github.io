---
layout: page
title: "12. 신경망 (기초 · Backpropagation · 실전 학습)"
permalink: /studies/ai/machine-learning/12-neural-networks/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `17(Part 1)` + `18(Part 2)` + `19(Part 3)` 통합·보강

{% raw %}
## 개요

Part 1 — linear classifier의 한계, perceptron/sigmoid neuron, hidden layer, XOR, activation.
Part 2 — computational graph에서 **chain rule**로 gradient 계산(backpropagation), gate별 규칙, matrix backward.
Part 3 — 실전 optimization(learning rate·momentum·Adam·mini-batch)과 regularization(augmentation·L2·dropout·early stopping).

---

# Part 1. 다층 신경망 기초

## 1. Linear classifier의 한계

$$\hat y = \operatorname{sign}(w^\top x)$$ — 선형 경계뿐. XOR·원형 경계는 표현 불가.

**Feature transform**: $$\text{model}(x,w) = w_0 + w_1 x_1^2 + w_2 x_2^2$$ 처럼 입력을 변환하면 linear model도 비선형 경계. 신경망 = 이 nonlinear feature를 **데이터에서 학습**.

## 2. Perceptron / Sigmoid neuron

$$
z = w_0 + \sum_j w_j x_j = w^\top x\ (x_0 = 1),\qquad a = g(z)
$$
$$g$$ = sigmoid $$\sigma(z) = 1/(1+e^{-z})$$ 등. sigmoid는 [0,1] 출력(확률 해석) + 미분 가능.

## 3. XOR과 Hidden Layer

perceptron은 AND/OR(linearly separable)은 되지만 **XOR은 단일 선형 경계로 불가**. hidden unit 2개로 명시적 해:
$$h_1 = \sigma(x_1 + x_2 - 0.5)$$ (OR), $$h_2 = \sigma(x_1 + x_2 - 1.5)$$ (AND) → $$\hat y = \sigma(h_1 - h_2 - 0.5)$$ = OR AND NOT AND = XOR.

$$
h_k = g(w_k^\top x),\qquad \hat y = g(v^\top h)
$$
각 hidden unit = feature detector. **Universal approximation**: hidden unit이 충분하면 1개 hidden layer로도 임의의 연속함수를 원하는 정확도로 근사 (단, 필요한 폭이 지수적일 수 있어 실무는 **깊게**).

## 4. Activation은 반드시 비선형

모든 layer가 선형이면 $$W_L\cdots W_1 x$$ = 하나의 선형변환. 대표: step, sigmoid, tanh, ReLU, LeakyReLU, GELU.

---

# Part 2. Backpropagation

## 5. Computational Graph + Chain Rule

$$
\frac{\partial L}{\partial x} = \frac{\partial L}{\partial z}\cdot\frac{\partial z}{\partial x}
\qquad\Longleftrightarrow\qquad
\text{downstream} = \text{upstream} \times \text{local}
$$
forward: 각 노드 출력 + backward에 필요한 값을 cache. backward: 출력에서 입력 방향으로 upstream gradient에 local Jacobian을 곱해 전파. 비용은 forward의 상수배 (**reverse-mode autodiff**).

### Gate별 backward
| gate | forward | backward |
|---|---|---|
| **add** $$z = x+y$$ | — | $$\partial L/\partial x = \partial L/\partial y = \partial L/\partial z$$ (그대로 분배) |
| **multiply** $$z = xy$$ | $$x, y$$ 저장 | $$\partial L/\partial x = (\partial L/\partial z)\,y$$, $$\partial L/\partial y = (\partial L/\partial z)\,x$$ |
| **max** $$z = \max(x,y)$$ | argmax 저장 | gradient가 argmax 입력으로만, 나머지 0 |
| **copy → 두 갈래** | — | 두 upstream gradient를 **합** |
| **sigmoid** $$a = \sigma(z)$$ | $$a$$ 저장 | $$\partial L/\partial z = \partial L/\partial a \cdot a(1-a)$$ |
| **tanh** $$a = \tanh(z)$$ | $$a$$ 저장 | $$\cdot(1 - a^2)$$ |
| **ReLU** $$a = \max(0,z)$$ | mask 저장 | $$z>0$$이면 통과, else 0 |

**sigmoid + cross-entropy 융합**: $$L = -y\log a - (1-y)\log(1-a)$$, $$a = \sigma(z)$$ → $$\partial L/\partial z = a - y$$ (깔끔, 수치 안정). softmax + CE도 $$\partial L/\partial z_c = p_c - \mathbb{1}[y = c]$$.

## 6. Vector / Matrix Backward

**원칙**: 어떤 변수에 대한 loss gradient는 그 변수와 **같은 shape**. $$W$$가 $$D_{in}\times D_{out}$$이면 $$\partial L/\partial W$$도 $$D_{in}\times D_{out}$$.

$$
Y = XW + b \;\Longrightarrow\;
\frac{\partial L}{\partial X} = \frac{\partial L}{\partial Y}\, W^\top,\qquad
\frac{\partial L}{\partial W} = X^\top \frac{\partial L}{\partial Y},\qquad
\frac{\partial L}{\partial b} = \sum_{\text{batch}} \frac{\partial L}{\partial Y}
$$
shape로 검산: $$X\ (B\times D_{in})$$, $$W\ (D_{in}\times D_{out})$$, $$\partial L/\partial Y\ (B\times D_{out})$$ → $$\partial L/\partial W = X^\top(\partial L/\partial Y)$$ 는 $$D_{in}\times D_{out}$$. batch $$X$$로 vectorize하면 모든 sample gradient를 한 번의 행렬곱으로.

## 7. Modular forward/backward API
```
forward(input)  -> output, cache
backward(dupstream, cache) -> dinput, dparams
```
cache에 backward에 필요한 forward 중간값 저장(메모리 ↔ 재계산 trade-off; gradient checkpointing).

### Vanishing / exploding gradient
sigmoid/tanh는 포화 영역에서 도함수 $$\approx 0$$ → 깊은 망에서 gradient가 층을 지나며 소실. 대응: **ReLU**, 잔차 연결(residual), 적절한 **weight init**(Xavier: $$\mathrm{Var}(W) = 1/D_{in}$$ for tanh; He: $$2/D_{in}$$ for ReLU), **BatchNorm/LayerNorm**, gradient clipping(exploding).

---

# Part 3. 실전 학습과 정규화

## 8. Optimization

다층 신경망 loss surface는 **non-convex**(local minima, saddle point, plateau — 고차원에선 saddle이 지배적).

- **Learning rate**: 너무 작음 → 느림/plateau, 너무 큼 → 발산/진동. log-scale로 탐색 후 validation.
- **Momentum**: $$v \leftarrow \rho v - \eta\nabla,\ w \leftarrow w + v$$ ($$\rho \approx 0.9$$) — 일관된 방향엔 가속, 지그재그 진동 감소. Nesterov는 look-ahead.
- **Adaptive**(Adagrad → RMSProp → **Adam**): $$m$$(1차)·$$v$$(2차) gradient 모멘트로 parameter별 유효 lr 조정. $$\hat m, \hat v$$ bias 보정. 기본 lr($$\approx 10^{-3}$$)은 여전히 tuning.
- **LR decay / warmup**: 초반 warmup 후 step/cosine decay → optimum 근처 진동↓.
- **Mini-batch SGD**: full batch와 SGD의 중간(batch 32~512) — gradient noise ↔ 계산 효율(GPU 활용) 균형, 신경망 학습의 표준.

## 9. Activation 비교

| | 범위 | 도함수 | 문제 |
|---|---|---|---|
| sigmoid | (0,1) | $$a(1-a) \le 0.25$$ | saturation, not zero-centered |
| tanh | (−1,1) | $$1 - a^2$$ | zero-centered(개선), 여전히 saturation |
| **ReLU** $$\max(0,z)$$ | [0,∞) | 1 or 0 | gradient 소실 완화·빠름, 음수에서 "dead ReLU" |
| LeakyReLU $$\max(\alpha z, z)$$ | ℝ | 1 or $$\alpha$$ | dead ReLU 완화 |

## 10. Regularization

- **Data augmentation**: translation/rotation/crop/flip/color jitter/mixup — label 유지(또는 혼합) 변형. 가장 효과 큰 정규화 중 하나.
- **L2 (weight decay)**: $$\text{loss} + \lambda\lVert W\rVert_2^2$$ — weight 크기 억제 → variance↓.
- **Dropout**: training forward마다 확률 $$p$$로 neuron 출력을 0. 특정 neuron 조합 의존↓, subnetwork 앙상블 효과. **test time엔 전부 사용** → activation scale 보정(**inverted dropout**: train 시 $$1/(1-p)$$로 나눔).
- **Early stopping**: validation 성능이 정체/악화되면 중단 → 유효 복잡도 제한.
- **BatchNorm**: 정규화 효과 부수적으로 동반(mini-batch 통계의 noise).

## 복습 질문

- linear classifier가 XOR를 못 하는 이유와, hidden unit 2개로 XOR를 만드는 명시적 구성은? universal approximation이 "깊게"를 권하는 이유는?
- add/multiply/max/copy gate와 sigmoid·ReLU의 backward 규칙, sigmoid+CE 융합 gradient가 $$a - y$$인 것을 유도하라.
- $$Y = XW + b$$의 $$\partial L/\partial X$$, $$\partial L/\partial W$$, $$\partial L/\partial b$$를 shape 검산과 함께 쓰라.
- vanishing gradient의 원인과, ReLU·residual·Xavier/He init·BatchNorm이 각각 어떻게 돕는가?
- momentum·Adam의 직관, mini-batch가 표준인 이유, dropout(inverted)·early stopping이 overfitting을 줄이는 원리는?
{% endraw %}

---

이전: [11. 차원 축소 (PCA · LDA)](11-dimension-reduction-pca-lda.md) · 다음: [13. CNN (합성곱 신경망)](13-convolutional-neural-networks.md)
