---
layout: page
title: "05. Ridge Regression"
permalink: /studies/ai/machine-learning/05-ridge-regression/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/05%20Ridge%20Regression%20-%20%EB%A6%BF%EC%A7%80%ED%9A%8C%EA%B7%80.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #ridge-regression #l2-regularization #overfitting #cross-validation

관련 노트: [성능평가와 Bias-Variance Tradeoff](04-linear-regression-part-2-bias-variance.md), [Lasso Regression과 Feature Selection](06-lasso-regression-feature-selection.md)

## 핵심 요약

이 강의는 많은 feature나 높은 차수 polynomial을 사용할 때 발생하는 overfitting을 줄이기 위해 ridge regression을 도입한다. Ridge regression은 RSS에 L2 penalty를 더해 큰 coefficient를 억제하고, bias-variance tradeoff를 조절하는 regularization 방법이다.

## Overfitting의 증상

높은 차수 polynomial이나 많은 feature를 가진 모델은 training data를 지나치게 잘 맞출 수 있다. 이때 학습된 coefficient가 매우 커지는 경우가 많다.

```text
큰 coefficient -> 입력의 작은 변화에도 예측이 크게 흔들림 -> 높은 variance
```

관측 수 `N`이 작고 feature 수 `D`가 크면 가능한 입력 조합을 충분히 관찰하기 어렵기 때문에 overfitting 위험이 커진다.

## Ridge Regression의 목적함수

Ridge regression은 데이터 적합도와 coefficient 크기를 함께 고려한다.

```text
cost(w) = RSS(w) + lambda * ||w||_2^2
```

| 항 | 의미 |
|---|---|
| `RSS(w)` | training data에 맞추는 정도 |
| `lambda * ||w||_2^2` | coefficient가 커지는 것을 벌점 |
| `lambda` | regularization strength |

`lambda`가 크면 coefficient가 더 작아지고 모델이 단순해진다.

## Bias-Variance 관점

| `lambda` | 모델 특성 | Bias | Variance |
|---:|---|---:|---:|
| 작음 | training data에 많이 맞춤 | 낮음 | 높음 |
| 큼 | coefficient를 강하게 축소 | 높음 | 낮음 |

Ridge는 variance를 줄이는 대신 bias를 약간 증가시키는 방법이다.

## Matrix Form

Linear regression의 RSS는 다음과 같다.

```text
RSS(w) = (y - H w)^T (y - H w)
```

Ridge objective는 다음과 같다.

```text
cost(w) = (y - H w)^T (y - H w) + lambda * w^T w
```

gradient:

```text
gradient cost(w) = -2 H^T (y - H w) + 2 lambda I w
```

closed-form solution:

```text
w_hat = (H^T H + lambda I)^(-1) H^T y
```

`lambda I`가 더해지면 `H^T H`가 singular하거나 ill-conditioned일 때도 안정성이 좋아진다.

## Gradient Descent

Ridge regression은 gradient descent로도 풀 수 있다.

```text
w_j <- w_j - eta * gradient_j
```

regularization term 때문에 각 weight는 데이터 gradient에 의해 갱신되면서도 0 방향으로 shrink된다.

## Gaussian Noise 관점

Linear regression에서 Gaussian noise를 가정하면 least squares는 maximum likelihood와 연결된다. Ridge penalty는 parameter에 Gaussian prior를 둔 MAP estimation으로 해석할 수도 있다. 즉, coefficient가 너무 큰 모델보다 작은 coefficient를 가진 모델을 사전적으로 선호한다.

## Lambda 선택

`lambda`는 training error만 보고 고르면 안 된다. regularization strength는 모델 성능 평가에 직접 영향을 주는 hyperparameter이므로 validation set이나 cross validation으로 선택해야 한다.

전형적인 데이터 분할:

| 데이터 | 용도 |
|---|---|
| Training set | 여러 lambda에서 weight 학습 |
| Validation set | lambda 선택 |
| Test set | 최종 일반화 성능 보고 |

## Intercept Penalty

표준 ridge cost는 모든 coefficient에 penalty를 줄 수 있지만, intercept `w0`에는 penalty를 주지 않는 경우가 많다. intercept는 전체 평균 위치를 조정하는 항이므로 크다고 해서 모델 복잡도가 높다고 해석하기 어렵기 때문이다.

## 시험ㆍ복습 체크포인트

- Ridge objective를 RSS와 L2 penalty로 쓸 수 있어야 한다.
- `lambda`가 커질 때 coefficient, bias, variance가 어떻게 변하는지 설명할 수 있어야 한다.
- Ridge closed-form solution을 linear regression closed-form과 비교할 수 있어야 한다.
- Hyperparameter 선택에 validation set이 필요한 이유를 말할 수 있어야 한다.
- Intercept를 penalize하지 않는 이유를 이해해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Ridge Regression**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **05. Ridge Regression**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. 성능평가와 Bias-Variance Tradeoff](04-linear-regression-part-2-bias-variance.md) · 다음: [06. Lasso Regression과 Feature Selection](06-lasso-regression-feature-selection.md)
