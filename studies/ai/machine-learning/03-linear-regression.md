---
layout: page
title: "03. 선형회귀 (모델·학습·성능평가·Bias–Variance)"
permalink: /studies/ai/machine-learning/03-linear-regression/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `03(Part 1)` + `04(Part 2)` 통합·보강

{% raw %}
## 개요

Part 1 — 선형회귀의 **모델, RSS, closed-form, gradient descent**, "입력이 아니라 feature에 대해 선형"(polynomial·basis·multiple).
Part 2 — 학습된 모델의 **평가**: training/test/generalization error, overfitting, **bias–variance tradeoff**.

---

# Part 1. 모델과 학습

## 1. 단순 선형회귀

$$
y_i = w_0 + w_1 x_i + \varepsilon_i,\qquad \hat y = \hat w_0 + \hat w_1 x
$$
$$w_0$$ intercept, $$w_1$$ slope(입력 1단위 증가 시 예측 변화량), $$\varepsilon_i$$ 설명 안 되는 오차. 확률 모델로는 $$\varepsilon_i \sim \mathcal{N}(0, \sigma^2)$$ i.i.d. → 다음의 RSS 최소화가 **MLE**와 일치.

## 2. Cost: RSS

$$
\text{RSS}(w_0,w_1) = \sum_i \big(y_i - (w_0 + w_1 x_i)\big)^2,\qquad
\hat w = \arg\min_w \text{RSS}(w)
$$

### 방법 1 — gradient = 0 (closed-form)

단순회귀는 두 정규방정식을 풀면:
$$
\hat w_1 = \frac{\sum_i (x_i - \bar x)(y_i - \bar y)}{\sum_i (x_i - \bar x)^2}
= \frac{\mathrm{Cov}(x,y)}{\mathrm{Var}(x)},\qquad
\hat w_0 = \bar y - \hat w_1 \bar x
$$
RSS는 $$w$$에 대해 볼록 이차식 → gradient를 0으로 두면 닫힌 해. **대부분의 ML 문제엔 closed-form이 없다** — 선형회귀는 예외적으로 쉬운 경우.

### 방법 2 — Gradient Descent
$$
w^{(t+1)} = w^{(t)} - \eta\,\nabla \text{RSS}(w^{(t)})
$$
$$\eta$$(learning rate) 너무 크면 발산, 너무 작으면 느림. 수렴 판정: gradient norm↓ / cost 감소량↓ / 파라미터 변화량↓ / 최대 반복. 볼록 함수 + 적절한 $$\eta$$ → local optimum = global optimum. ($$\eta < 2/\lambda_{\max}(2H^\top H)$$ 이면 수렴.)

## 3. feature에 대해 선형

### Polynomial regression
$$
y_i = w_0 + w_1 x_i + w_2 x_i^2 + \dots + w_p x_i^p + \varepsilon_i
$$
$$x$$에 대해선 비선형이지만 **파라미터 $$w$$에 대해선 선형** → 선형회귀로 학습 가능. degree $$p$$가 model complexity 손잡이.

### Basis expansion
$$
y_i = \sum_{j=0}^{D} w_j\, h_j(x_i) + \varepsilon_i
$$
$$h_j$$: 임의의 feature 함수 — 선형 시간항, sine/cosine(추세·계절성), radial basis $$\exp(-\lVert x - c_j\rVert^2/\tau)$$, indicator(step) 등. 모델이 표현할 수 있는 함수 클래스를 $$h_j$$가 결정.

### Multiple regression
입력이 vector $$x=(x_1,\dots,x_d)$$. 여러 feature를 함께 사용.

## 4. 행렬 형태와 정규방정식 유도

$$H \in \mathbb{R}^{N\times (D+1)}$$ (행 = sample, 열 = feature, 첫 열 = 1):
$$
y = Hw + \varepsilon,\qquad
\text{RSS}(w) = (y - Hw)^\top (y - Hw) = y^\top y - 2w^\top H^\top y + w^\top H^\top H w
$$
$$
\nabla_w \text{RSS}(w) = -2 H^\top y + 2 H^\top H w = -2 H^\top (y - Hw) \overset{!}{=} 0
$$
$$
\boxed{\;\hat w = (H^\top H)^{-1} H^\top y\;}\quad(\text{normal equation})
$$
- $$H^\top H$$가 invertible해야 함 → feature가 선형독립, 보통 $$N > D$$.
- 기하: $$\hat y = H\hat w = H(H^\top H)^{-1}H^\top y$$ = $$y$$를 $$H$$의 열공간에 **직교 투영**. residual $$y - \hat y \perp$$ 열공간.
- 계산: $$O(ND^2 + D^3)$$. $$D$$가 크면 GD/SGD가 유리.

---

# Part 2. 성능 평가와 Bias–Variance

## 5. Loss / Error 계층

| 개념 | 정의 | 성질 |
|---|---|---|
| **Loss** $$L(y, f(x))$$ | 예: $$(y-f(x))^2$$, $$\|y-f(x)\|$$ | 의사결정 비용과 연결 |
| **Training error** | $$\frac{1}{N_{\text{tr}}}\sum L(y_i, f_{\hat w}(x_i))$$ | 복잡도↑ → 감소. **낙관적**(모델 선택·평가가 같은 데이터) |
| **Generalization error** | $$\mathbb{E}_{x,y}[L(y, f_{\hat w}(x))]$$ | 참값, 계산 불가(분포 미지) |
| **Test error** | held-out set에서의 평균 loss | generalization error의 **불편 추정** (단, test set을 모델 선택에 쓰면 오염) |

## 6. Model Complexity

| 복잡도 | Training error | Test error |
|---|---|---|
| 너무 낮음 | 높음 | 높음 — **underfitting** (bias 큼) |
| 적절 | 낮음 | 낮음 |
| 너무 높음 | 매우 낮음 | 높음 — **overfitting** (variance 큼) |

- **Training/Test split**: test point가 적으면 generalization 추정 variance↑, 너무 크면 학습 데이터 부족. 흔히 train/validation/test (예: 60/20/20).

## 7. Bias–Variance 분해 — 유도

한 test point $$x_0$$에서 $$y = f(x_0) + \varepsilon$$, $$\mathbb{E}[\varepsilon] = 0$$, $$\mathrm{Var}(\varepsilon) = \sigma^2$$. training set $$\mathcal{D}$$가 random이므로 $$\hat f(x_0) = \hat f_{\mathcal{D}}(x_0)$$도 random. 기대 제곱오차:
$$
\mathbb{E}_{\mathcal{D},\varepsilon}\big[(y - \hat f(x_0))^2\big]
$$
$$\bar f(x_0) = \mathbb{E}_{\mathcal{D}}[\hat f(x_0)]$$ 을 넣고 전개하면 교차항이 사라져:
$$
\boxed{\;\mathbb{E}[(y - \hat f(x_0))^2]
= \underbrace{\sigma^2}_{\text{irreducible}}
+ \underbrace{\big(f(x_0) - \bar f(x_0)\big)^2}_{\text{Bias}^2}
+ \underbrace{\mathbb{E}_{\mathcal{D}}\big[(\hat f(x_0) - \bar f(x_0))^2\big]}_{\text{Variance}}\;}
$$

| 요소 | 의미 | 줄이는 법 |
|---|---|---|
| noise $$\sigma^2$$ | 데이터 자체 불확실성 | 불가(irreducible) |
| Bias² | 평균 모델이 true $$f$$에서 벗어난 정도 | 모델 표현력 증가(복잡도↑) |
| Variance | train set 변화에 흔들리는 정도 | regularization, 데이터 증가, 앙상블 |

복잡도↑ → bias↓, variance↑. **왜 training error만 최소화하면 위험한가**를 이 분해가 설명한다. (deep model의 "double descent"처럼 예외적 현상도 존재.)

## 복습 질문

- 단순회귀 $$\hat w_1 = \mathrm{Cov}(x,y)/\mathrm{Var}(x)$$, $$\hat w_0 = \bar y - \hat w_1\bar x$$를 정규방정식에서 유도하라.
- normal equation $$\hat w = (H^\top H)^{-1}H^\top y$$를 $$\nabla_w\text{RSS} = 0$$에서 유도하고, "직교 투영" 해석을 설명하라.
- polynomial regression이 왜 여전히 "파라미터에 대해 선형"인가? basis $$h_j$$가 모델의 무엇을 결정하는가?
- bias–variance 분해의 세 항을 유도의 개요와 함께 쓰고, 복잡도가 각 항에 주는 영향은?
- training error가 optimistic한 이유, test set을 모델 선택에 쓰면 안 되는 이유는?
{% endraw %}

---

이전: [02. 점추정과 MLE](02-point-estimation-mle.md) · 다음: [04. 정규화 회귀 (Ridge·Lasso·CV)](04-regularized-regression.md)
