---
layout: page
title: "03. 선형회귀 모델과 학습"
permalink: /studies/ai/machine-learning/03-linear-regression-part-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/03%20Linear%20Regression%20Part%201%20-%20%EC%84%A0%ED%98%95%ED%9A%8C%EA%B7%80%20%EB%AA%A8%EB%8D%B8%EA%B3%BC%20%ED%95%99%EC%8A%B5.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #linear-regression #gradient-descent #least-squares

관련 노트: [점추정과 Maximum Likelihood Estimation](02-point-estimation-mle.md), [성능평가와 Bias-Variance Tradeoff](04-linear-regression-part-2-bias-variance.md)

## 핵심 요약

이 강의는 집값 예측 예제를 통해 linear regression의 모델, cost function, closed-form solution, gradient descent를 설명한다. 또한 polynomial regression, basis expansion, multiple regression을 통해 선형회귀가 입력 자체가 아니라 feature에 대해 선형인 모델이라는 점을 강조한다.

## Regression 문제

Regression은 입력 `x`로부터 연속값 출력 `y`를 예측하는 문제이다. 집값 예측에서는 `x`가 면적, 방 개수, 위치 같은 feature이고 `y`가 가격이다.

```text
training data = {(x_i, y_i)}
goal = new x에 대한 y 예측
```

## Simple Linear Regression

가장 단순한 모델은 직선이다.

```text
y_i = w0 + w1*x_i + epsilon_i
```

| 기호 | 의미 |
|---|---|
| `w0` | intercept |
| `w1` | slope |
| `epsilon_i` | noise 또는 모델로 설명되지 않는 오차 |

예측값은 다음과 같다.

```text
y_hat = w0_hat + w1_hat*x
```

`w1`은 입력이 한 단위 증가할 때 예측값이 얼마나 변하는지를 의미한다.

## Cost Function: RSS

주어진 직선이 데이터를 얼마나 잘 맞추는지는 residual sum of squares로 측정한다.

```text
RSS(w0, w1) = sum_i (y_i - (w0 + w1*x_i))^2
```

목표는 RSS를 최소화하는 parameter를 찾는 것이다.

```text
w_hat = argmin_w RSS(w)
```

## Approach 1: Gradient를 0으로 두기

RSS는 quadratic convex function이므로 gradient를 0으로 두면 해를 닫힌 형태로 구할 수 있다. 다만 대부분의 머신러닝 문제에서는 이렇게 closed-form solution이 존재하지 않으므로, linear regression은 예외적으로 쉬운 사례이다.

## Approach 2: Gradient Descent

Gradient descent는 cost function의 음의 gradient 방향으로 parameter를 반복적으로 갱신한다.

```text
w^(t+1) = w^t - eta * gradient RSS(w^t)
```

| 요소 | 의미 |
|---|---|
| `eta` | learning rate 또는 step size |
| gradient | cost가 가장 빨리 증가하는 방향 |
| negative gradient | cost가 가장 빨리 감소하는 방향 |

learning rate가 너무 크면 발산하고, 너무 작으면 수렴이 느리다. 고정 learning rate와 감소 learning rate scheduling을 사용할 수 있다.

## Convergence Criteria

수렴 판단에는 다음 기준을 쓸 수 있다.

- gradient norm이 충분히 작아짐
- cost 감소량이 충분히 작아짐
- parameter 변화량이 충분히 작아짐
- 최대 반복 횟수 도달

Convex function에서는 local optimum이 global optimum이다.

## Polynomial Regression

입력 하나만 사용하더라도 feature를 확장하면 곡선 모델을 만들 수 있다.

```text
y_i = w0 + w1*x_i + w2*x_i^2 + ... + wp*x_i^p + epsilon_i
```

이 모델은 `x`에 대해서는 nonlinear이지만, parameter `w`에 대해서는 linear이므로 linear regression으로 학습할 수 있다.

## Basis Expansion

더 일반적으로 feature function `h_j(x)`를 정의하면 다음과 같다.

```text
y_i = w0*h0(x_i) + w1*h1(x_i) + ... + wD*hD(x_i) + epsilon_i
```

예를 들어 시간 추세와 계절성을 제거할 때는 선형 시간항, sine, cosine feature를 사용할 수 있다.

## Multiple Regression

입력이 vector `x`일 때 여러 feature를 함께 사용한다.

```text
x = (x[1], x[2], ..., x[d])
```

집값 예측에서는 면적, 화장실 수, 침실 수, lot size, 건축 연도 등이 feature가 될 수 있다.

## Matrix Form

전체 데이터를 행렬로 쓰면 다음과 같다.

```text
y = H*w + epsilon
RSS(w) = (y - H*w)^T (y - H*w)
```

gradient는 다음과 같다.

```text
gradient RSS(w) = -2 H^T (y - H*w)
```

closed-form solution은 normal equation으로 얻는다.

```text
w_hat = (H^T H)^(-1) H^T y
```

이 해는 `H^T H`가 invertible일 때 가능하며, 보통 관측 수 `N`이 feature 수보다 충분히 많아야 안정적이다.

## 시험ㆍ복습 체크포인트

- linear regression 모델과 RSS 목적함수를 쓸 수 있어야 한다.
- gradient descent update 식을 설명할 수 있어야 한다.
- polynomial regression이 왜 여전히 parameter에 대해 linear인지 이해해야 한다.
- matrix form의 `H`, `w`, `y`가 무엇을 의미하는지 말할 수 있어야 한다.
- closed-form solution의 조건과 한계를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. 선형회귀 모델과 학습**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회귀 주제에서는 잔차 패턴을 보고 모델 가정 위반 여부를 확인한다.
- regularization은 계수를 작게 만드는 계산 기법이면서 variance를 줄이는 inductive bias로 해석한다.
- 모델의 수식은 외울 식이 아니라 어떤 확률적 가정이나 기하학적 가정에서 나왔는지 함께 보아야 한다.
- training error와 validation/test 성능이 달라지는 이유를 bias, variance, regularization으로 연결한다.
- 분류와 회귀는 모두 feature representation, objective, optimization, evaluation의 선택 문제로 정리할 수 있다.

### 문제 풀이 또는 구현 루틴

- 입력/출력, label의 의미, loss가 벌주는 오류의 종류를 먼저 적는다.
- 수식 전개에서는 차원을 확인하고 scalar objective와 vector/matrix gradient가 맞는지 본다.
- 결과는 accuracy 하나로 끝내지 말고 confusion matrix, precision/recall, calibration, overfitting을 함께 해석한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 모델이 복잡하면 항상 좋은 것이 아니라 데이터 수, noise, regularization과 함께 봐야 한다.
- test set으로 hyperparameter를 고르면 일반화 성능을 과대평가한다.
- loss를 최소화하는 것과 실제 의사결정 비용을 최소화하는 것은 다를 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제의 가정이 깨지는 데이터는 어떤 모습인가?
- regularization이나 prior가 모델의 해를 어느 방향으로 움직이는가?
- 성능이 나빠졌을 때 데이터, feature, loss, optimization 중 무엇부터 의심할 것인가?
- **03. 선형회귀 모델과 학습**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. 점추정과 Maximum Likelihood Estimation](02-point-estimation-mle.md) · 다음: [04. 성능평가와 Bias-Variance Tradeoff](04-linear-regression-part-2-bias-variance.md)
