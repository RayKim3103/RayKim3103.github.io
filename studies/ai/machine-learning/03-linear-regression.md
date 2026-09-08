---
layout: page
title: "03. 선형회귀 (모델·학습·성능평가·Bias–Variance)"
permalink: /studies/ai/machine-learning/03-linear-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `03(Part 1)` + `04(Part 2)` 통합·보강
- 표준 ML 교재(ISL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

Part 1 — 선형회귀의 **모델, RSS, closed-form, gradient descent**, 그리고 "입력이 아니라 feature에 대해 선형"이라는 관점(polynomial·basis·multiple regression).
Part 2 — 학습된 모델의 **평가**: training/test/generalization error, overfitting, **bias–variance tradeoff**.

---

# Part 1. 모델과 학습

## 1. 단순 선형회귀

$$
y_i = w_0 + w_1 x_i + \varepsilon_i,\qquad \hat y = \hat w_0 + \hat w_1 x
$$
$w_0$ intercept, $w_1$ slope(입력 1단위 증가 시 예측 변화량), $\varepsilon_i$ 설명 안 되는 오차.

## 2. Cost: RSS

$$
\text{RSS}(w_0,w_1) = \sum_i \big(y_i - (w_0 + w_1 x_i)\big)^2,\qquad
\hat w = \arg\min_w \text{RSS}(w)
$$

### 방법 1 — gradient = 0 (closed-form)
RSS는 볼록 이차식 → gradient를 0으로 두면 닫힌 해. **대부분의 ML 문제엔 closed-form이 없다** — 선형회귀는 예외적으로 쉬운 경우.

### 방법 2 — Gradient Descent
$$
w^{(t+1)} = w^{(t)} - \eta\,\nabla \text{RSS}(w^{(t)})
$$
$\eta$(learning rate) 너무 크면 발산, 너무 작으면 느림. 수렴 판정: gradient norm↓ / cost 감소량↓ / 파라미터 변화량↓ / 최대 반복. 볼록 함수에서 local optimum = global optimum.

## 3. feature에 대해 선형

### Polynomial regression
$$
y_i = w_0 + w_1 x_i + w_2 x_i^2 + \dots + w_p x_i^p + \varepsilon_i
$$
$x$에 대해선 비선형이지만 **파라미터 $w$에 대해선 선형** → 선형회귀로 학습 가능.

### Basis expansion
$$
y_i = \sum_{j=0}^{D} w_j\, h_j(x_i) + \varepsilon_i
$$
$h_j$: 임의의 feature 함수(선형 시간항, sine/cosine 등 — 추세·계절성 제거).

### Multiple regression
입력이 vector $x=(x_1,\dots,x_d)$. 여러 feature를 함께 사용.

## 4. 행렬 형태

$$
y = Hw + \varepsilon,\qquad
\text{RSS}(w) = (y - Hw)^\top (y - Hw)
$$
$$
\nabla \text{RSS}(w) = -2 H^\top (y - Hw)
$$
$$
\boxed{\;\hat w = (H^\top H)^{-1} H^\top y\;}\quad(\text{normal equation})
$$
$H^\top H$가 invertible해야 함 → 보통 관측 수 $N \gg$ feature 수여야 안정적.

---

# Part 2. 성능 평가와 Bias–Variance

## 5. Loss / Error 계층

| 개념 | 정의 | 성질 |
|---|---|---|
| **Loss** $L(y, f(x))$ | 예: $(y-f(x))^2$, $|y-f(x)|$ | 의사결정 비용과 연결 |
| **Training error** | $\frac{1}{N_{\text{train}}}\sum L(y_i, f_{\hat w}(x_i))$ | 복잡도↑ → 감소. **낙관적**(모델 선택·평가가 같은 데이터) |
| **Generalization error** | $\mathbb{E}_{x,y}[L(y, f_{\hat w}(x))]$ | 참값, 계산 불가(분포 미지) |
| **Test error** | held-out set에서의 평균 loss | generalization error의 근사 |

## 6. Model Complexity

| 복잡도 | Training error | Test error |
|---|---|---|
| 너무 낮음 | 높음 | 높음 — **underfitting** (bias 큼) |
| 적절 | 낮음 | 낮음 |
| 너무 높음 | 매우 낮음 | 높음 — **overfitting** (variance 큼) |

- **Training/Test split**: test point가 적으면 generalization 추정 variance↑, 너무 크면 학습 데이터 부족.

## 7. Bias–Variance 분해

$$
\mathbb{E}[(\hat f(x) - y)^2]
= \underbrace{\sigma^2_{\text{noise}}}_{\text{irreducible}}
+ \underbrace{\text{Bias}[\hat f(x)]^2}_{\text{평균 모델이 true에서 벗어난 정도}}
+ \underbrace{\text{Var}[\hat f(x)]}_{\text{train set 변화에 흔들리는 정도}}
$$

| 요소 | 줄이는 법 |
|---|---|
| noise | 불가(irreducible) |
| bias | 모델 표현력 증가 |
| variance | regularization, 데이터 증가 |

복잡도↑ → bias↓, variance↑. **왜 training error만 최소화하면 위험한가**를 이 분해가 설명한다.

## 복습 질문

- 선형회귀 모델과 RSS, gradient descent update 식을 쓰라.
- polynomial regression이 왜 여전히 "파라미터에 대해 선형"인가?
- normal equation과 그것이 성립하는 조건은?
- training error가 optimistic한 이유와, bias–variance 분해의 세 항은?
{% endraw %}

---

이전: [02. 점추정과 MLE](02-point-estimation-mle.md) · 다음: [04. 정규화 회귀 (Ridge·Lasso·CV)](04-regularized-regression.md)
