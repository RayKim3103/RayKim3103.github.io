---
layout: page
title: "과제 3 — Neural Networks (Flat · Modular · Dropout · Momentum)"
permalink: /studies/ai/machine-learning/hw3-neural-networks/
sitemap: false
---

- **원본 코드**: [GitHub — Artificial Intelligence / NeuralNetwork](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence/NeuralNetwork) (`2024_P03_neural_network.ipynb`, `project3.py`)
- EEE3314 Introduction to Artificial Intelligence · Assignment III (2024-12-22 마감)
- 관련 강의 노트: [12. 신경망](12-neural-networks.md) · [05. 로지스틱 회귀](05-logistic-regression.md) · [13. CNN](13-convolutional-neural-networks.md)

{% raw %}
## 개요

**MLP를 직접 구현** — 먼저 flat 방식(forward/backward를 한 함수에), 다음 modular 방식(PyTorch처럼 layer별 `forward`/`backward` + cache). `make_moons` 데이터는 비선형이라 `LogisticRegressionCV`로는 분리 불가 → 은닉층의 비선형 feature가 필요함을 보인다.

### 데이터셋
`sklearn.datasets.make_moons(300, noise=0.25)`, `np.random.seed(0)` → $$X \in \mathbb{R}^{300\times2}$$, $$y \in \{0, 1\}$$

### 네트워크 구조 (P1·P2 공통)

$$
\begin{aligned}
H_1 &= X W_1 + b_1, & z_1 &= \text{ReLU}(H_1) \\
H_2 &= z_1 W_2 + b_2, & z_2 &= \text{LeakyReLU}(H_2) \quad (\max(0.01x, x)) \\
H_3 &= z_2 W_3 + b_3, & z_3 &= \tanh(H_3 + H_1) \quad \leftarrow \textbf{skip connection} \\
H_4 &= z_3 W_4 + b_4, & \hat y &= \sigma(H_4)
\end{aligned}
$$

| 하이퍼파라미터 | 값 |
|---|---|
| 입력 / 은닉1 / 은닉2 / 은닉3 / 출력 | 2 / 10 / 10 / 10 / 1 |
| weight init | $$\mathcal{N}(0,1)$$ (`np.random.randn`) · bias init 0 |
| loss | BCE(log loss) $$L = -y\log\hat y - (1-y)\log(1-\hat y)$$ |
| total loss | $$L_{total} = \sum_i L^{(i)} + \lambda\lVert W\rVert^2$$, $$\;\lambda = 0.001$$ |
| optimizer | Gradient Descent, lr $$= 10^{-4}$$, epochs $$= 50{,}000$$ |

$$z_3 = \tanh(H_3 + H_1)$$의 skip connection 때문에 backprop에서 $$H_1$$의 gradient는 $$z_1$$ 경로와 $$\tanh$$ 경로 **양쪽에서** 흘러 들어온다.

---

## P1. Flat 구현 (40점)

`class NeuralNetwork`: `forward_propagation` / `back_propagation` / `compute_loss` / `train` / `predict`. 전체 순전파·역전파를 손으로 전개.

### 측정 결과 (loss history)

| epoch | total loss |
|---:|---:|
| 1,000 | 51.56 |
| 10,000 | 24.68 |
| 25,000 | 16.47 |
| 38,000 | **10.86** |

이후 소폭 진동 (lr 고정 GD). moon 데이터의 두 초승달을 가르는 곡선 경계 형성.

## P2. Modular 구현 (40점)

`class Linear / ReLU / LeakyReLU / Tanh / Sigmoid / Dropout / GradientDescent / Momentum / NeuralNetwork_module`. 각 layer는 `staticmethod forward(x, ...) -> (out, cache)`와 `backward(cache, dout) -> dx` 구조. `SigmoidWithBCEloss`는 sigmoid+BCE를 융합해 gradient를 단순화.

추가 요소:
- 각 은닉 활성화 뒤 **Dropout** (rate 0.1, train 시에만)
- optimizer: **GradientDescent** 와 **Momentum** ($$\rho = 0.9$$): $$v \leftarrow \rho v - \eta g$$, $$\theta \leftarrow \theta + v$$

### 측정 결과 (dropout 0.01, momentum 0.9)

| epoch | total loss |
|---:|---:|
| 1,000 | 31.92 |
| 5,000 | 17.06 |
| 22,000 | **10.83** |

→ momentum 덕에 P1보다 초반 수렴이 빠르지만, dropout의 확률적 마스킹으로 loss curve가 더 noisy.

---

## P3. 분석 (20점)

### P3.1 Capacity (5점)
은닉 노드 수 $$(1,1), (2,2), (3,3), (4,4), (5,5), (20,20), (50,50)$$로 바꿔 decision boundary 관찰 → 용량이 작으면 underfit(거의 직선), 커지면 곡선이 정교해지지만 과하면 noise까지 학습.

### P3.2 Regularization (5점)
$$\lambda \in \{0,\ 10^{-5},\ 10^{-3},\ 10^{-1}\}$$ → $$\lambda$$가 커질수록 경계가 매끄러워지고, 과하면 underfit.

### P3.3 Dropout (5점)
rate $$\in \{0,\ 0.01,\ 0.1,\ 0.5\}$$ → 적당한 dropout은 일반화 향상, 0.5는 정보 손실로 성능 저하.

### P3.4 Optimizer (5점)
Gradient Descent vs GD + Momentum($$\rho = 0.9$$) → momentum이 같은 epoch에서 더 낮은 loss, 수렴 속도 향상.

---

## 핵심 정리

- MLP: `Linear → ReLU → LeakyReLU → tanh(·+skip) → sigmoid`, BCE loss + L2, GD (lr 1e-4, 50k epoch).
- **Flat 구현**은 backprop을 한 함수에 손으로, **modular 구현**은 layer별 forward/backward + cache (PyTorch 방식). skip connection은 gradient가 두 경로로 합쳐짐.
- Flat: loss 51.6(1k) → 10.9(38k). Modular(+dropout+momentum): 31.9(1k) → 10.8(22k), 더 빠르지만 noisy.
- 분석: capacity↑ → 경계 복잡도↑ (과하면 overfit); L2·dropout은 일반화↑ (과하면 underfit); momentum은 수렴 가속.

## 복습 질문

- `make_moons`가 로지스틱 회귀로 분리 안 되는 이유, 은닉층이 어떻게 이를 해결하나?
- $$z_3 = \tanh(H_3 + H_1)$$의 skip connection이 backprop에서 $$H_1$$의 gradient에 미치는 영향은?
- flat 구현과 modular 구현의 차이, cache가 backward에 필요한 이유는?
- capacity / L2 / dropout / momentum 각각을 바꿨을 때 decision boundary와 loss curve가 어떻게 변하나?
{% endraw %}

---

이전 과제: [과제 2 — Ensemble](hw2-ensemble.md)
