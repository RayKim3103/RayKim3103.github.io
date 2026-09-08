---
layout: page
title: "04. 정규화 회귀 (Ridge · Lasso · Cross-Validation)"
permalink: /studies/ai/machine-learning/04-regularized-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `05(Ridge)` + `06(Lasso)` + `07(기하학·CV)` 통합·보강
- 표준 ML 교재(ISL·ESL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

많은 feature·고차 polynomial → overfitting(큰 coefficient → 높은 variance). **Ridge(L2)**는 coefficient를 부드럽게 축소, **Lasso(L1)**는 일부를 정확히 0으로(feature selection). 기하학적으로 왜 다른지, 그리고 $\lambda$를 **validation / K-fold CV**로 고르는 법.

## 1. Overfitting의 증상

$$
\text{큰 coefficient} \;\Rightarrow\; \text{입력의 작은 변화에 예측이 크게 흔들림} \;\Rightarrow\; \text{높은 variance}
$$
관측 수 $N$이 작고 feature 수 $D$가 크면 위험 ↑.

## 2. Ridge Regression (L2)

$$
\text{cost}(w) = \text{RSS}(w) + \lambda \lVert w \rVert_2^2
= (y - Hw)^\top(y - Hw) + \lambda\, w^\top w
$$
$$
\nabla\text{cost}(w) = -2H^\top(y - Hw) + 2\lambda w
\;\Rightarrow\;
\boxed{\;\hat w = (H^\top H + \lambda I)^{-1} H^\top y\;}
$$

- $\lambda I$ 덕분에 $H^\top H$가 singular/ill-conditioned여도 **안정적**.
- $\lambda\!\uparrow$ → coefficient↓, **bias↑ variance↓**.
- **MAP 해석**: Gaussian noise 가정 시 least squares = MLE. L2 penalty = 파라미터에 **Gaussian prior**를 둔 MAP(작은 coefficient를 사전 선호).
- **intercept $w_0$에는 penalty를 주지 않는다**(전체 평균 위치 조정 항이라 복잡도와 무관).

## 3. Lasso Regression (L1)

$$
\text{cost}(w) = \text{RSS}(w) + \lambda \lVert w \rVert_1,\qquad
\lVert w \rVert_1 = \sum_j |w_j|
$$

- L1은 해가 **좌표축에 닿기 쉬운 구조** → 일부 $w_j$가 정확히 0 → **feature selection**.
- 0에서 미분 불가 → **coordinate descent**(한 번에 한 좌표만 최적화).

### Feature selection의 대안들
- **All subsets**: $2^D$개 전부 시도 → $D$ 크면 불가.
- **Greedy**: forward/backward stepwise — 빠르지만 전역 최적 보장 X.
- **Ridge + thresholding**: correlated feature에서 coefficient를 나눠 가져 불안정.

### Coordinate descent + Soft thresholding
$w_j$만 갱신(나머지 고정), 정규화된 feature 가정 시:
$$
w_j =
\begin{cases}
\rho_j + \lambda/2 & \rho_j < -\lambda/2 \\
0 & |\rho_j| \le \lambda/2 \\
\rho_j - \lambda/2 & \rho_j > \lambda/2
\end{cases}
$$
$\rho_j$(다른 feature 제거 후 $j$의 상관)가 $\lambda/2$보다 작으면 $w_j=0$ → **이것이 lasso가 feature를 선택하는 메커니즘**.

> **Feature normalization 필수**: scale이 큰 feature는 같은 $w_j$라도 영향이 다르다 → 평균 0, 분산 1(또는 norm 1)로.

## 4. 기하학 (constraint 형태)

$$
\min_w \text{RSS}(w) \quad \text{s.t.} \quad \lVert w \rVert_2^2 \le c \;\;(\text{Ridge}) \quad\text{또는}\quad \lVert w \rVert_1 \le c \;\;(\text{Lasso})
$$

| | 제약 영역(2D) | RSS contour가 닿는 곳 | 결과 |
|---|---|---|---|
| **Ridge** | 원 (모서리 없음) | 축이 아닌 지점 | coefficient 축소, 대부분 nonzero |
| **Lasso** | 마름모 (꼭짓점이 축 위) | 꼭짓점에 닿기 쉬움 | 일부 coefficient = 0 (sparse) |
| correlated feature | weight를 나눠 가짐 | — | Lasso는 하나를 선택하는 경향 |

## 5. $\lambda$ 선택 — Validation & K-fold CV

$\lambda$는 **training error로 고르면 안 됨**(항상 최소 $\lambda$를 고름).

$$
\text{training set} \to \text{weight 학습},\quad
\text{validation set} \to \lambda \text{ 선택},\quad
\text{test set} \to \text{최종 평가(한 번만)}
$$

### K-Fold Cross Validation
데이터가 적어 single split이 불안정할 때:
1. 데이터를 $K$개 fold로 분할
2. 한 fold를 validation, 나머지 $K{-}1$개로 학습
3. validation error 기록
4. 모든 fold에 대해 반복
5. 평균 validation error가 최소인 $\lambda$ 선택

각 sample이 한 번은 validation, 여러 번은 training에 쓰여 **데이터 효율↑**. 계산량은 약 $K$배.

## 복습 질문

- Ridge/Lasso objective와 각 closed-form(또는 최적화 방법)을 쓰라.
- L1이 sparse solution을 만드는 이유를 기하학적으로 설명하라.
- soft thresholding 식과, $w_j=0$이 되는 조건은?
- validation set과 test set의 역할 차이, K-fold CV 절차는?
{% endraw %}

---

이전: [03. 선형회귀](03-linear-regression.md) · 다음: [05. 로지스틱 회귀](05-logistic-regression.md)
