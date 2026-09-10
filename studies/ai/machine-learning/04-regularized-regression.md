---
layout: page
title: "04. 정규화 회귀 (Ridge · Lasso · Cross-Validation)"
permalink: /studies/ai/machine-learning/04-regularized-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `05(Ridge)` + `06(Lasso)` + `07(기하학·CV)` 통합·보강

{% raw %}
## 개요

많은 feature·고차 polynomial → overfitting(큰 coefficient → 높은 variance). **Ridge(L2)**는 coefficient를 부드럽게 축소, **Lasso(L1)**는 일부를 정확히 0으로(feature selection). 기하학적으로 왜 다른지, $$\lambda$$를 **validation / K-fold CV**로 고르는 법.

## 1. Overfitting의 증상

$$
\text{큰 coefficient} \;\Rightarrow\; \text{입력의 작은 변화에 예측이 크게 흔들림} \;\Rightarrow\; \text{높은 variance}
$$
관측 수 $$N$$이 작고 feature 수 $$D$$가 크면 위험 ↑. 정규화는 **가설 공간을 제약**해 variance를 줄이는 대가로 약간의 bias를 받는다.

## 2. Ridge Regression (L2)

$$
\text{cost}(w) = \text{RSS}(w) + \lambda \lVert w \rVert_2^2
= (y - Hw)^\top(y - Hw) + \lambda\, w^\top w
$$
$$
\nabla\text{cost}(w) = -2H^\top(y - Hw) + 2\lambda w \overset{!}{=} 0
\;\Longrightarrow\;
\boxed{\;\hat w_{\text{ridge}} = (H^\top H + \lambda I)^{-1} H^\top y\;}
$$

- $$\lambda I$$ 덕분에 $$H^\top H$$가 singular/ill-conditioned여도 항상 invertible → **수치 안정성**. ($$H^\top H + \lambda I$$의 최소 eigenvalue $$\ge \lambda > 0$$.)
- $$\lambda\!\to\!0$$: least squares. $$\lambda\!\to\!\infty$$: $$\hat w \to 0$$. → **bias↑ variance↓** (단조).
- **shrinkage 정도**: SVD $$H = U\Sigma V^\top$$ 로 보면 $$j$$번째 방향의 계수가 $$\dfrac{\sigma_j^2}{\sigma_j^2 + \lambda}$$ 배로 축소 → 분산이 작은(작은 $$\sigma_j$$) 방향을 더 강하게 억제.
- **MAP 해석**: $$y_i \sim \mathcal{N}(w^\top h(x_i), \sigma^2)$$ 이면 least squares = MLE. $$w_j \sim \mathcal{N}(0, \tau^2)$$ **Gaussian prior**를 두면 MAP objective가 $$\text{RSS}(w) + \frac{\sigma^2}{\tau^2}\lVert w\rVert_2^2$$ → $$\lambda = \sigma^2/\tau^2$$.
- **intercept $$w_0$$에는 penalty를 주지 않는다**(전체 평균 위치 조정 항이라 복잡도와 무관). 보통 $$y$$와 feature를 centering해서 처리.

## 3. Lasso Regression (L1)

$$
\text{cost}(w) = \text{RSS}(w) + \lambda \lVert w \rVert_1,\qquad
\lVert w \rVert_1 = \sum_j |w_j|
$$

- L1 constraint 영역은 **꼭짓점이 좌표축 위** → 해가 축에 닿기 쉬움 → 일부 $$w_j$$가 **정확히 0** → **feature selection**.
- 0에서 미분 불가 → gradient descent 대신 **coordinate descent**(한 번에 한 좌표) + **subgradient**.
- **MAP 해석**: $$w_j$$에 **Laplace(double exponential) prior** $$p(w_j) \propto e^{-|w_j|/b}$$.

### Feature selection의 대안들
- **All subsets**: $$2^D$$개 전부 → $$D$$ 크면 불가.
- **Greedy stepwise**: forward(빈 모델에서 추가) / backward(전체에서 제거) — 빠르지만 전역 최적 보장 X.
- **Ridge + thresholding**: correlated feature에서 coefficient를 나눠 가져 어느 것을 자를지 불안정.

### Coordinate descent + Soft thresholding — 유도

$$w_j$$만 갱신(나머지 고정). residual $$r^{(j)} = y - \sum_{k\ne j} w_k h_k$$ 에 대해 $$\rho_j = h_j^\top r^{(j)}$$, $$z_j = \lVert h_j\rVert_2^2$$ 로 두면 목적함수는 $$z_j w_j^2 - 2\rho_j w_j + \lambda|w_j| + \text{const}$$. subgradient를 0으로:

$$
w_j = \frac{1}{z_j}\,S(\rho_j, \lambda/2), \qquad
S(\rho, \gamma) =
\begin{cases}
\rho + \gamma & \rho < -\gamma \\
0 & |\rho| \le \gamma \\
\rho - \gamma & \rho > \gamma
\end{cases}
$$

$$\rho_j$$(다른 feature 제거 후 $$j$$의 상관)의 크기가 $$\lambda/2$$ 이하이면 $$w_j = 0$$ → **이것이 lasso가 feature를 선택하는 메커니즘**. (feature normalized면 $$z_j = 1$$ 로 단순화.)

> **Feature normalization 필수**: scale이 큰 feature는 같은 $$w_j$$라도 영향이 다르다 → 평균 0, 분산 1(또는 norm 1). Ridge도 마찬가지.

## 4. 기하학 (constraint 형태)

$$
\min_w \text{RSS}(w) \quad \text{s.t.} \quad \lVert w \rVert_2^2 \le c \;\;(\text{Ridge}) \quad\text{또는}\quad \lVert w \rVert_1 \le c \;\;(\text{Lasso})
$$
(라그랑주 쌍대성으로 penalty 형태와 등가; $$c$$와 $$\lambda$$는 1:1 대응.)

| | 제약 영역(2D) | RSS contour(타원)가 닿는 곳 | 결과 |
|---|---|---|---|
| **Ridge** | 원 (매끄러움) | 축이 아닌 지점 | coefficient 축소, 대부분 nonzero |
| **Lasso** | 마름모 (꼭짓점이 축 위) | 꼭짓점에 닿기 쉬움 | 일부 coefficient = 0 (sparse) |
| correlated feature | Ridge: weight 나눠 가짐 / Lasso: 하나 선택 | — | Elastic Net($$\alpha$$L1+$$(1-\alpha)$$L2)은 grouping |

## 5. $$\lambda$$ 선택 — Validation & K-fold CV

$$\lambda$$는 **training error로 고르면 안 됨**(항상 최소 $$\lambda$$를 고름 = overfit).

$$
\text{training set} \to \text{weight 학습},\quad
\text{validation set} \to \lambda \text{ 선택},\quad
\text{test set} \to \text{최종 평가(한 번만)}
$$

### K-Fold Cross Validation
데이터가 적어 single split이 불안정할 때:
1. 데이터를 $$K$$개 fold로 분할 (보통 $$K = 5$$ 또는 $$10$$)
2. fold $$k$$를 validation, 나머지 $$K{-}1$$개로 학습 → $$\text{err}_k(\lambda)$$
3. 모든 $$k$$ 반복
4. $$\overline{\text{CV}}(\lambda) = \frac{1}{K}\sum_k \text{err}_k(\lambda)$$ 가 최소인 $$\lambda$$ 선택 (또는 **1-SE rule**: 최소값 + 1 표준오차 이내에서 가장 단순한 모델)
5. 선택된 $$\lambda$$로 **전체 데이터** 재학습

각 sample이 한 번은 validation, $$K{-}1$$번은 training → **데이터 효율↑**. 계산량 약 $$K$$배. $$K=N$$이면 **LOOCV**(편향 작지만 분산·계산 큼).

## 복습 질문

- Ridge closed-form $$\hat w = (H^\top H + \lambda I)^{-1}H^\top y$$를 유도하고, $$\lambda I$$가 수치 안정성을 주는 이유(eigenvalue)는?
- soft-threshold $$S(\rho, \lambda/2)$$를 coordinate-descent 목적함수의 subgradient에서 유도하고, $$w_j = 0$$ 조건은?
- L1이 sparse solution을 만드는 이유를 constraint 기하로 설명하라. Elastic Net이 correlated feature에서 나은 점은?
- Ridge/Lasso의 MAP 해석(Gaussian / Laplace prior)과 $$\lambda$$의 의미는?
- K-fold CV 절차와 1-SE rule, LOOCV의 장단점은?
{% endraw %}

---

이전: [03. 선형회귀](03-linear-regression.md) · 다음: [05. 로지스틱 회귀](05-logistic-regression.md)
