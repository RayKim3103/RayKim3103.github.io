---
layout: page
title: "05. 로지스틱 회귀 (분류 · 정규화 · SGD)"
permalink: /studies/ai/machine-learning/05-logistic-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `08(Part 1)` + `09(Part 2)` + `10(Part 3)` 통합·보강

{% raw %}
## 개요

Part 1 — 선형 분류기, **odds/logit/sigmoid**, likelihood(=cross-entropy) 최적화, softmax(다중 클래스).
Part 2 — 고차 feature → 결정경계 overfitting → **L2 정규화**.
Part 3 — 대규모 데이터 확장: **SGD**, online learning.

---

# Part 1. 선형 분류와 로지스틱 회귀

## 1. 선형 분류기

$$
\text{Score}(x) = w^\top h(x),\qquad \hat y = \operatorname{sign}(\text{Score}(x))
$$
결정경계 $$\{x : w^\top h(x) = 0\}$$ 는 feature 공간의 hyperplane. 단어 목록 기반 threshold 분류의 문제: 단어 중요도를 사람이 정해야 함 / 문맥·조합 반영 어려움 / confidence 없음 → **weight를 데이터에서 학습 + score를 확률로 해석**.

## 2. Odds → Logit → Sigmoid

$$
\text{odds} = \frac{p}{1-p},\qquad
\text{logit}(p) = \log\frac{p}{1-p}
$$
로지스틱 회귀의 가정: **log-odds가 feature의 선형결합**
$$
\text{logit}\big(P(y{=}{+}1\mid x)\big) = w^\top h(x)
\;\Longleftrightarrow\;
P(y{=}{+}1\mid x, w) = \sigma(w^\top h(x)),\quad \sigma(z) = \frac{1}{1+e^{-z}}
$$
score 0 → 확률 0.5, score↑ → positive 확률↑. **$$w_j$$ 해석**: feature $$j$$가 1 증가하면 log-odds가 $$w_j$$ 증가 (odds가 $$e^{w_j}$$ 배).

sigmoid 성질: $$\sigma(-z) = 1 - \sigma(z)$$, $$\;\sigma'(z) = \sigma(z)(1 - \sigma(z))$$.

## 3. 학습 — Likelihood = Cross-Entropy, gradient 유도

$$y_i \in \{0, 1\}$$ 표기로, $$p_i = \sigma(w^\top h(x_i))$$:
$$
P(D\mid w) = \prod_i p_i^{\,y_i}(1 - p_i)^{1 - y_i}
$$
negative log-likelihood(= cross-entropy loss):
$$
J(w) = -\sum_i \big[\, y_i \log p_i + (1 - y_i)\log(1 - p_i)\,\big]
$$

**gradient** (핵심 결과): $$\dfrac{\partial}{\partial w}\big[y\log\sigma(z) + (1-y)\log(1-\sigma(z))\big] = (y - \sigma(z))\,h(x)$$ 이므로
$$
\boxed{\;\nabla J(w) = -\sum_i \big(y_i - p_i\big)\, h(x_i) = H^\top(p - y)\;}
$$
"오차(잔차) × feature" — 선형회귀 gradient와 같은 형태. **Hessian** $$H^\top S H$$ ($$S = \mathrm{diag}(p_i(1-p_i)) \succeq 0$$) → **$$J$$는 볼록**, 단 **closed-form 없음** → gradient descent / **Newton(IRLS)**.

| 개념 | 의미 |
|---|---|
| Entropy $$H(p) = -\sum p\log p$$ | 분포 자체의 불확실성 |
| Cross-entropy $$H(p, q) = -\sum p\log q$$ | 참 분포 $$p$$를 $$q$$로 부호화할 때 평균 비용 |
| KL $$D_{KL}(p\Vert q) = H(p,q) - H(p)$$ | 두 분포의 차이 (extra cost, $$\ge 0$$) |

분류 학습 = model 분포를 참 label 분포에 가깝게 → cross-entropy ↓ ⟺ $$D_{KL}$$ ↓ (참 분포 entropy는 상수).

## 4. 다중 클래스 — Softmax

$$
P(y{=}c\mid x, w) = \frac{e^{\,s_c}}{\sum_k e^{\,s_k}},\qquad s_c = w_c^\top h(x)
$$
cross-entropy $$J = -\sum_i \log P(y_i \mid x_i)$$. gradient: $$\nabla_{w_c} J = \sum_i (P(c\mid x_i) - \mathbb{1}[y_i = c])\,h(x_i)$$ — 이진과 동일한 "예측확률 − 정답" 구조. (softmax는 $$w_c$$에 상수 벡터를 더해도 불변 → 한 클래스 weight를 0으로 고정 가능; 이진 softmax = 로지스틱 회귀.)

---

# Part 2. Overfitting과 L2 정규화

고차 feature(quadratic, degree 6/20 …) → 결정경계가 매우 유연 → training엔 잘 맞고 test는 악화. 이때 coefficient가 커진다(선형분리 가능 데이터면 MLE가 $$\lVert w\rVert \to \infty$$로 발산).

$$
\max_w\; \ell\ell(w) - \lambda \lVert w \rVert_2^2
\quad\Longleftrightarrow\quad
\min_w\; J(w) + \lambda \lVert w \rVert_2^2
$$

| $$\lambda$$ | 결정경계 | 위험 |
|---|---|---|
| 작음 | 복잡, training에 민감 | overfitting |
| 큼 | 부드럽고 단순 (덜 confident) | underfitting |

### Gradient update (L2)
$$
w \leftarrow w + \eta\big(\nabla \ell\ell(w) - 2\lambda w\big)
= w + \eta\Big(\sum_i (y_i - p_i)h(x_i) - 2\lambda w\Big)
$$
regularization 항 $$-2\lambda w$$가 weight를 **0 쪽으로 당긴다**(weight decay). intercept는 penalize 안 하는 설정이 흔함. $$\lambda$$는 validation/CV로.

---

# Part 3. SGD와 Online Learning

## Batch vs Stochastic

$$
\nabla \ell\ell(w) = \sum_i \nabla \ell\ell_i(w)
$$
전체 데이터로 매 update → 정확하지만 느림(update 횟수 적음). $$N$$이 크면 1 epoch = 1 update.

**SGD**: 한 sample(또는 mini-batch $$B$$)의 gradient로 자주 갱신
$$
w \leftarrow w + \eta\, \nabla \ell\ell_i(w)
\qquad(\text{L2: } +\eta(\nabla\ell\ell_i(w) - 2\lambda w))
$$
개별 gradient는 **unbiased 추정** ($$\mathbb{E}_i[\nabla\ell\ell_i] = \frac{1}{N}\nabla\ell\ell$$) 이지만 분산이 큼 → path는 흔들려도 자주 갱신해 큰 데이터에서 빠르게 수렴. **수렴엔 $$\sum \eta_t = \infty$$, $$\sum \eta_t^2 < \infty$$** (예: $$\eta_t = \eta_0/(1 + t/\tau)$$) 같은 감소 스케줄 필요.

| | Batch | SGD |
|---|---|---|
| update 비용 | 큼(전체) | 작음(일부) |
| 방향 정확도 | 높음 | noisy (unbiased) |
| 대규모 데이터 | 느림 | 효율적 |
| 수렴 경로 | 매끄러움 | 흔들림, LR decay로 진정 |

- **Online learning**: 데이터가 시간에 따라 도착(광고 클릭 예측, 추천, ranking) → sample마다 갱신 → 분포가 변하는(concept drift) 문제에 적합. regret으로 분석.
- sample 순서는 매 epoch **shuffle** (편향된 순서면 gradient가 편향).

## 복습 질문

- odds, logit, sigmoid의 관계와, "log-odds가 선형" 가정에서 $$w_j$$의 해석(odds 배수)은?
- $$\nabla J(w) = H^\top(p - y)$$를 유도하고, $$J$$가 볼록임을 Hessian으로 보여라. 왜 closed-form이 없는가?
- cross-entropy = NLL임을 보이고, $$D_{KL}$$과의 관계(왜 cross-entropy 최소화가 KL 최소화인가)를 설명하라.
- 선형분리 가능한 데이터에서 정규화 없는 로지스틱 회귀가 발산하는 이유, L2가 이를 막는 방식은?
- SGD gradient가 unbiased라는 것과, 수렴을 위한 learning-rate 조건($$\sum\eta_t = \infty$$, $$\sum\eta_t^2 < \infty$$)의 의미는?
{% endraw %}

---

이전: [04. 정규화 회귀](04-regularized-regression.md) · 다음: [06. 결정 트리](06-decision-tree.md)
