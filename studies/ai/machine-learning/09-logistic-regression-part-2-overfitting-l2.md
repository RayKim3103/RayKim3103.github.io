---
layout: page
title: "09. 분류 Overfitting과 L2 정규화"
permalink: /studies/ai/machine-learning/09-logistic-regression-part-2-overfitting-l2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/09%20Logistic%20Regression%20Part%202%20-%20%EB%B6%84%EB%A5%98%20Overfitting%EA%B3%BC%20L2%20%EC%A0%95%EA%B7%9C%ED%99%94.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #classification #logistic-regression #regularization #l2

관련 노트: [선형분류와 Logistic Regression](08-logistic-regression-part-1.md), [SGD와 Online Learning](10-logistic-regression-part-3-sgd-online-learning.md)

## 핵심 요약

이 강의는 logistic regression에서 feature complexity가 커질 때 decision boundary가 과하게 복잡해지는 overfitting 문제를 다룬다. 해결책으로 L2 regularization을 log likelihood objective에 추가하고, gradient ascent update가 어떻게 바뀌는지 설명한다.

## Classification Overfitting

2차원 feature에서 linear boundary는 단순한 직선이다. 그러나 quadratic, degree 6, degree 20 feature처럼 고차 feature를 추가하면 decision boundary가 매우 유연해진다.

```text
feature complexity 증가 -> training data에는 잘 맞음 -> test data에서는 성능 악화 가능
```

분류에서도 regression과 마찬가지로 모델 복잡도가 너무 높으면 noise나 우연한 패턴까지 학습한다.

## Logistic Regression Model 복습

```text
Score(x_i) = w^T h(x_i)
P(y_i=+1 | x_i,w) = sigmoid(w^T h(x_i))
```

학습은 log likelihood를 최대화하는 weight를 찾는 문제이다.

```text
max_w ll(w)
```

## Regularized Objective

큰 coefficient를 억제하기 위해 L2 penalty를 추가한다.

```text
max_w ll(w) - lambda * ||w||_2^2
```

또는 minimization 관점에서는 cross entropy에 L2 penalty를 더한다.

```text
min_w cross_entropy(w) + lambda * ||w||_2^2
```

## Lambda의 영향

| `lambda` | 결정경계 | 해석 |
|---:|---|---|
| 작음 | 복잡하고 training data에 민감 | overfitting 가능 |
| 큼 | 부드럽고 단순 | underfitting 가능 |

Degree 20 feature를 쓰더라도 regularization이 강하면 coefficient가 작아져 boundary가 과도하게 흔들리지 않는다.

## Gradient Ascent Update

Regularization이 없는 logistic regression은 log likelihood gradient 방향으로 weight를 증가시킨다. L2 penalty를 넣으면 weight를 0 쪽으로 당기는 항이 추가된다.

```text
w <- w + eta * (gradient ll(w) - 2*lambda*w)
```

intercept에는 penalty를 적용하지 않는 설정도 자주 사용한다.

## L2 Regularization의 직관

각 coefficient가 커질수록 score가 입력 변화에 과민하게 반응한다. L2 penalty는 모든 coefficient를 부드럽게 줄여 복잡한 boundary를 완화한다.

분류에서 regularization은 다음 균형을 만든다.

```text
data likelihood를 높이는 것
coefficient magnitude를 작게 유지하는 것
```

## 시험ㆍ복습 체크포인트

- Logistic regression에서 고차 feature가 overfitting을 일으키는 이유를 설명할 수 있어야 한다.
- L2-regularized logistic regression objective를 쓸 수 있어야 한다.
- `lambda`가 decision boundary와 coefficient 크기에 미치는 영향을 이해해야 한다.
- Gradient update에서 regularization term이 어떤 방향으로 작용하는지 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. 분류 Overfitting과 L2 정규화**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 분류 주제에서는 decision boundary, score/probability 변환, threshold 선택, class imbalance를 함께 보아야 한다.
- 정확도만 보지 말고 false positive와 false negative의 실제 비용을 문제 맥락에서 비교한다.
- 회귀 주제에서는 잔차 패턴을 보고 모델 가정 위반 여부를 확인한다.
- regularization은 계수를 작게 만드는 계산 기법이면서 variance를 줄이는 inductive bias로 해석한다.
- 모델의 수식은 외울 식이 아니라 어떤 확률적 가정이나 기하학적 가정에서 나왔는지 함께 보아야 한다.

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
- **09. 분류 Overfitting과 L2 정규화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. 선형분류와 Logistic Regression](08-logistic-regression-part-1.md) · 다음: [10. SGD와 Online Learning](10-logistic-regression-part-3-sgd-online-learning.md)
