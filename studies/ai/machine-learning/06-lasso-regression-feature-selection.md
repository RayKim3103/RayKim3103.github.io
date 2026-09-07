---
layout: page
title: "06. Lasso Regression과 Feature Selection"
permalink: /studies/ai/machine-learning/06-lasso-regression-feature-selection/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/06%20Lasso%20Regression%20-%20%EB%9D%BC%EC%8F%98%ED%9A%8C%EA%B7%80%EC%99%80%20Feature%20Selection.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #lasso #l1-regularization #feature-selection #coordinate-descent

관련 노트: [Ridge Regression](05-ridge-regression.md), [정규화회귀의 기하학과 Cross Validation](07-regularized-regression-geometry-cross-validation.md)

## 핵심 요약

이 강의는 feature selection을 위한 lasso regression을 다룬다. Ridge가 coefficient를 작게 만들지만 보통 정확히 0으로 만들지는 않는 반면, lasso는 L1 penalty를 사용해 일부 coefficient를 0으로 만들어 sparse model을 만든다.

## Feature Selection

Feature selection은 많은 입력 feature 중 예측에 중요한 feature만 선택하는 작업이다.

### 필요한 이유

- 모델을 해석하기 쉬워진다.
- 불필요한 feature가 만든 noise를 줄일 수 있다.
- 측정 비용이 높은 feature를 줄일 수 있다.
- overfitting을 완화할 수 있다.

집값 예측에서 dishwasher, bathroom 수, lot size, year 등 수많은 feature가 있을 때 모든 feature가 의미 있는 것은 아니다.

## All Subsets와 Greedy 방법

### All subsets

모든 feature subset을 시도해 가장 좋은 조합을 찾는다. Feature가 `D`개이면 가능한 subset은 `2^D`개라서 feature 수가 커지면 계산이 불가능해진다.

### Greedy algorithms

- Forward stepwise: 빈 모델에서 시작해 하나씩 feature를 추가한다.
- Backward stepwise: 전체 feature에서 시작해 하나씩 제거한다.

Greedy 방법은 빠르지만 전체 최적 subset을 보장하지 않는다.

## Ridge Thresholding의 한계

작은 ridge coefficient를 나중에 0으로 잘라내는 방법은 feature 간 상관관계가 있을 때 불안정하다. Ridge는 correlated feature의 coefficient를 함께 나눠 가지는 경향이 있으므로, 단순 thresholding은 어떤 feature를 남길지 명확한 기준을 주지 못한다.

## Lasso Objective

Lasso는 RSS에 L1 norm penalty를 더한다.

```text
cost(w) = RSS(w) + lambda * ||w||_1
||w||_1 = sum_j |w_j|
```

L1 penalty는 해가 좌표축에 닿기 쉬운 구조를 만들며, 그 결과 일부 coefficient가 정확히 0이 된다.

## Ridge와 Lasso 비교

| 항목 | Ridge | Lasso |
|---|---|---|
| Penalty | L2, `sum w_j^2` | L1, `sum |w_j|` |
| 효과 | coefficient shrinkage | shrinkage + feature selection |
| 해의 형태 | 대부분 nonzero | sparse 가능 |
| 최적화 | 미분 가능 | 0에서 미분 불가능 |

## Coordinate Descent

Lasso objective는 절댓값 때문에 모든 좌표에 대해 단순 gradient를 적용하기 어렵다. Coordinate descent는 한 번에 하나의 weight만 최적화한다.

```text
repeat until convergence:
    for each coordinate j:
        w_j만 바꾸고 나머지 w는 고정
```

좌표 선택은 순차적으로 할 수도 있고, 가장 개선이 큰 좌표를 선택할 수도 있다.

## Feature Normalization

Lasso에서는 feature scale이 매우 중요하다. Scale이 큰 feature는 같은 coefficient라도 예측에 미치는 영향이 다르기 때문이다. 따라서 보통 각 feature를 평균 0, 분산 1 또는 norm 1로 정규화한 뒤 lasso를 적용한다.

## Soft Thresholding

Lasso coordinate update의 핵심은 soft thresholding이다.

```text
rho_j < -lambda/2  -> w_j = (rho_j + lambda/2) / z_j
|rho_j| <= lambda/2 -> w_j = 0
rho_j > lambda/2   -> w_j = (rho_j - lambda/2) / z_j
```

즉, `rho_j`가 충분히 크지 않으면 해당 feature의 weight가 0이 된다. 이것이 lasso가 feature selection을 수행하는 직접적인 메커니즘이다.

## Normalized와 Unnormalized Update

Feature가 normalized되어 있으면 `z_j`가 일정하거나 단순해져 update가 간결해진다. Unnormalized feature에서는 각 feature의 scale을 반영하는 `z_j`가 필요하다.

## Lasso의 영향

Lasso는 통계학, 머신러닝, 전기전자 분야에서 sparse model을 만들기 위한 중요한 도구이다. 특히 많은 feature 중 일부만 실제로 중요한 high-dimensional setting에서 강력하다.

## 시험ㆍ복습 체크포인트

- Feature selection이 왜 필요한지 설명할 수 있어야 한다.
- L1 penalty와 L2 penalty의 차이를 말할 수 있어야 한다.
- Lasso가 coefficient를 정확히 0으로 만들 수 있는 이유를 기하학적으로 이해해야 한다.
- Coordinate descent와 soft thresholding update를 설명할 수 있어야 한다.
- Lasso 전에 feature normalization이 필요한 이유를 알아야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Lasso Regression과 Feature Selection**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **06. Lasso Regression과 Feature Selection**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. Ridge Regression](05-ridge-regression.md) · 다음: [07. 정규화회귀의 기하학과 Cross Validation](07-regularized-regression-geometry-cross-validation.md)
