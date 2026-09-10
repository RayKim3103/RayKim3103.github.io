---
layout: page
title: "과제 1 — Regression (Linear · Ridge · Lasso · Elastic Net)"
permalink: /studies/ai/machine-learning/hw1-regression/
sitemap: false
---

- **원본 코드**: [GitHub — Artificial Intelligence / Regression](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence/Regression) (`2024_P01_regression.ipynb`, `project1.py`, `crime-train.csv`, `crime-test.csv`)
- EEE3314 Introduction to Artificial Intelligence · Assignment I (2024-10-27 마감)
- 관련 강의 노트: [03. 선형회귀](03-linear-regression.md) · [04. 정규화 회귀](04-regularized-regression.md)

{% raw %}
## 개요

**Overfitting과 regularization**을 직접 구현하며 관찰하는 과제. Linear / Ridge / Lasso / Elastic Net 회귀를 **NumPy만으로**(`linalg.inv`, `transpose`, `dot`, `sum`, `concatenate`, `random`, `min`, `max`) 구현 — `sklearn.linear_model` 사용 금지. **정규화하지 않은(unnormalized) feature** 사용, intercept term은 강의 노트 방식.

### 데이터셋 — Communities and Crime (UCI)
- `crime-train.csv`: 1,595개 지역, **95개 feature** (경찰력 규모, 고졸 비율 등) → intercept 포함 $$X \in \mathbb{R}^{1595\times97}$$
- `crime-test.csv`: 399개 지역 ($$X \in \mathbb{R}^{399\times97}$$)
- 타깃: `ViolentCrimesPerPop` (인구당 폭력범죄율)

---

## P1. 데이터 로딩 (5점)

`Dataset` 클래스: `load_data` (csv 읽기), `parse_data(features)` → $$(X, y)$$ (intercept column 추가), `gather_data(X, y, n)` (앞 $$n$$개 subsample).

## P2. Linear Regression (25점)

`RSSloss(X, y, w) = ‖y − Xw‖²`, `LinearRegressor`에 세 가지 solver:

| solver | 방식 | 조건 |
|---|---|---|
| **coordinate descent** | 좌표 하나씩 순차 갱신 | init $$w \sim \mathcal{N}(0,1)$$, 수렴 조건 $$\lVert \hat w_t - \hat w_{t+1}\rVert_\infty < \tau\ (=10^{-3})$$ |
| **gradient descent** | $$w \leftarrow w - \eta\,\nabla \text{RSS}$$ | $$\eta = 10^{-5}$$ |
| **closed form** | normal equation $$\hat w = (X^\top X)^{-1}X^\top y$$ | — |

### 측정 결과 (전체 train)

| solver | final RSS loss | 소요 시간 |
|---|---:|---:|
| coordinate descent | **1.1120** | 2.74 s |
| gradient descent | 465.53 | 0.06 s |
| closed form | 3.98e-24 (≈ 0) | ~0 s |

→ closed form이 정확해에 도달, GD는 $$\eta$$가 작아 50 000 iter로도 미수렴, coordinate descent는 안정적.

## P3. Ridge Regression (15점)

$$\text{cost} = \text{RSS}(w) + \lambda \sum_j w_j^2$$ (bias term은 penalty 제외). 같은 세 solver, $$\lambda = 15$$.

| solver | final RSS loss | 시간 |
|---|---:|---:|
| coordinate descent | 3.3813 | 2.44 s |
| gradient descent | 3.2752 | 1.00 s |
| closed form | 3.3807 | ~0 s |

## P4. Lasso Regression (5점)

$$\text{cost} = \text{RSS}(w) + \lambda \sum_j |w_j|$$. Coordinate descent + **soft-thresholding**:

$$
S(\rho, \lambda) =
\begin{cases}
\rho + \lambda/2, & \rho < -\lambda/2 \\
0, & -\lambda/2 \le \rho \le \lambda/2 \\
\rho - \lambda/2, & \rho > \lambda/2
\end{cases}
$$

$$\lambda = 5$$ → final loss **0.1255** (1.34 s).

## P5. Elastic Net (10점)

$$
\text{Total cost} = \text{RSS}(w) + \lambda\left(\alpha\sum_j |w_j| + (1-\alpha)\sum_j w_j^2\right)
$$

Coordinate descent 갱신에서 L1 항은 threshold를 $$\lambda\alpha/2$$로 사용 (**threshold 유도 과정 답안 필수**). $$\lambda = 5,\ \alpha = 0.5$$ → final loss **0.3405** (1.25 s).

---

## P6. 분석 (45점)

### P6.1 train 데이터 수의 효과

| linear regressor | train RSS | **test RSS** |
|---|---:|---:|
| 100개로 학습 | 0.5704 | **291.91** |
| 전체(1595)로 학습 | 1.1120 | **0.2605** |

→ 데이터가 적으면 train loss는 낮지만 test error가 폭발 = **overfitting**. 데이터 증가가 가장 직접적인 해법이지만 현실에서는 확보가 어려움 → regularization 필요.

### P6.2 regressor별 비교 (800개로 학습)

| regressor | train RSS | test RSS |
|---|---:|---:|
| Linear (800) | 0.7944 | 0.4641 |
| Linear (전체) | — | 0.2605 |
| Ridge ($$\lambda$$=15) | 3.6185 | 2.2740 |
| Lasso ($$\lambda$$=5) | 0.2211 | **0.1102** |
| Elastic Net ($$\lambda$$=5, $$\alpha$$=0.5) | 0.5339 | 0.2774 |

→ 이 설정에서는 **Lasso가 test error 최소**. Ridge는 $$\lambda$$가 커서 과도하게 shrink (bias 증가). 기대와 다른 결과는 toy experiment로 원인 규명하도록 요구.

### P6.3 계수 크기

regressor별 feature weight 히스토그램 비교 — Lasso는 다수 계수가 정확히 0(**sparse**, feature selection), Ridge는 전반적으로 축소되지만 0은 아님, Elastic Net은 그 사이.

### P6.4 Ridge vs Lasso vs Elastic Net
penalty 기하학(L2 원 / L1 마름모 / 혼합), sparsity 여부, correlated feature 처리(Lasso는 하나만 선택 vs Elastic Net은 grouping) 비교.

### P6.5 tuning parameter $$\lambda$$의 효과
$$\lambda \in \{0, 5, 10, \dots, 200\}$$ sweep → train error는 단조 증가, test error는 U자 (적정 $$\lambda$$에서 최소). $$\lambda \to 0$$이면 least squares, $$\lambda \to \infty$$이면 $$w \to 0$$.

---

## 핵심 정리

- Linear regression 3 solver: closed form(정확·빠름, $$X^\top X$$ 역행렬 필요) / coordinate descent(안정) / gradient descent($$\eta$$ 민감).
- Regularization: Ridge(L2, shrink) / Lasso(L1, sparse, soft-threshold $$S(\rho,\lambda)$$) / Elastic Net(혼합, threshold $$\lambda\alpha/2$$).
- overfitting은 **데이터 수 부족**에서 심화 (train 100개 → test RSS 291.91). 데이터 증가 또는 regularization으로 완화.
- 이 데이터셋에서는 Lasso가 test 성능 최고 (test RSS 0.1102), $$\lambda$$가 과하면 Ridge처럼 bias 증가.

## 복습 질문

- coordinate descent의 수렴 조건 $$\lVert \hat w_t - \hat w_{t+1}\rVert_\infty < \tau$$의 의미와, closed form이 왜 정확해를 주는가?
- Lasso soft-thresholding $$S(\rho,\lambda)$$가 sparsity를 만드는 이유, Elastic Net에서 threshold가 $$\lambda\alpha/2$$인 이유는?
- train 100개 vs 전체로 학습했을 때 test RSS가 291.91 → 0.26으로 바뀐 이유를 bias–variance로 설명할 수 있나?
- $$\lambda$$를 0에서 키울 때 train error와 test error가 각각 어떻게 움직이나?
{% endraw %}

---

다음 과제: [과제 2 — Ensemble](hw2-ensemble.md)
