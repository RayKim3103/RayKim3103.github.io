---
layout: page
title: "05. 로지스틱 회귀 (분류 · 정규화 · SGD)"
permalink: /studies/ai/machine-learning/05-logistic-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `08(Part 1)` + `09(Part 2)` + `10(Part 3)` 통합·보강
- 표준 ML 교재 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

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
결정경계 $\{x : \text{Score}(x) = 0\}$ 는 feature 공간의 hyperplane.
단어 목록 기반 threshold 분류의 문제: 단어 중요도를 사람이 정해야 함 / 문맥·조합 반영 어려움 / confidence 없음 → **weight를 데이터에서 학습 + score를 확률로 해석**.

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
score 0 → 확률 0.5, score↑ → positive 확률↑.

## 3. 학습 — Likelihood = Cross-Entropy

$$
\hat w = \arg\max_w \log P(D\mid w)
= \arg\min_w \sum_i \Big[ -\mathbb{1}[y_i{=}{+}1]\log p_i - \mathbb{1}[y_i{=}{-}1]\log(1-p_i) \Big]
$$
closed-form 없음 → gradient ascent/descent.

| 개념 | 의미 |
|---|---|
| Entropy | 분포 자체의 불확실성 |
| Cross-entropy | 참 분포를 다른 분포로 부호화할 때 평균 비용 |
| KL divergence | 두 분포의 차이(extra cost) |

분류 학습 = model 분포를 참 label 분포에 가깝게 (cross-entropy ↓ = KL ↓).

## 4. 다중 클래스 — Softmax

$$
P(y{=}c\mid x, w) = \frac{e^{\,s_c}}{\sum_k e^{\,s_k}},\qquad s_c = w_c^\top h(x)
$$

---

# Part 2. Overfitting과 L2 정규화

고차 feature(quadratic, degree 6/20 …) → 결정경계가 매우 유연 → training엔 잘 맞고 test는 악화. 이때 coefficient가 커진다.

$$
\max_w\; \ell\ell(w) - \lambda \lVert w \rVert_2^2
\quad\Longleftrightarrow\quad
\min_w\; \text{CE}(w) + \lambda \lVert w \rVert_2^2
$$

| $\lambda$ | 결정경계 | 위험 |
|---|---|---|
| 작음 | 복잡, training에 민감 | overfitting |
| 큼 | 부드럽고 단순 | underfitting |

### Gradient ascent update (L2)
$$
w \leftarrow w + \eta\big(\nabla \ell\ell(w) - 2\lambda w\big)
$$
regularization 항이 weight를 **0 쪽으로 당긴다**. intercept는 penalize 안 하는 설정이 흔함.

---

# Part 3. SGD와 Online Learning

## Batch vs Stochastic

$$
\nabla \ell\ell(w) = \sum_i \nabla \ell\ell_i(w)
$$
전체 데이터로 매 update → 정확하지만 느림(update 횟수 적음).

**SGD**: 한 sample(또는 mini-batch)의 gradient로 자주 갱신
$$
w \leftarrow w + \eta\, \nabla \ell\ell_i(w)
\qquad(\text{L2: } +\eta(\nabla\ell\ell_i(w) - 2\lambda w))
$$
개별 gradient는 noisy하지만 **평균적으로 전체 gradient 방향** → path는 흔들려도 자주 갱신해 큰 데이터에서 빠르게 수렴.

| | Batch | SGD |
|---|---|---|
| update 비용 | 큼(전체) | 작음(일부) |
| 방향 정확도 | 높음 | noisy |
| 대규모 데이터 | 느림 | 효율적 |
| 수렴 경로 | 매끄러움 | 흔들림 |

- 후반에 learning rate를 줄이면(decay) gradient noise 진동↓.
- **Online learning**: 데이터가 시간에 따라 도착(광고 클릭 예측, 추천, ranking) → sample마다 갱신 → 분포가 변하는 문제에 적합.

## 복습 질문

- odds, logit, sigmoid의 관계와, "log-odds가 선형" 가정의 의미는?
- cross-entropy loss가 정답 클래스 확률과 어떻게 연결되는가? KL과의 관계는?
- L2 정규화가 gradient update에서 어느 방향으로 작용하는가?
- SGD가 noisy한데도 동작하는 이유, batch 대비 장단점은?
{% endraw %}

---

이전: [04. 정규화 회귀](04-regularized-regression.md) · 다음: [06. 결정 트리](06-decision-tree.md)
