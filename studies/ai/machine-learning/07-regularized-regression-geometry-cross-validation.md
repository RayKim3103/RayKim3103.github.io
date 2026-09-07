---
layout: page
title: "07. 정규화회귀의 기하학과 Cross Validation"
permalink: /studies/ai/machine-learning/07-regularized-regression-geometry-cross-validation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/07%20Regularized%20Regression%20Geometry%20-%20%EC%A0%95%EA%B7%9C%ED%99%94%ED%9A%8C%EA%B7%80%EC%9D%98%20%EA%B8%B0%ED%95%98%ED%95%99%EA%B3%BC%20Cross%20Validation.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #regularization #ridge #lasso #cross-validation

관련 노트: [Lasso Regression과 Feature Selection](06-lasso-regression-feature-selection.md), [선형분류와 Logistic Regression](08-logistic-regression-part-1.md)

## 핵심 요약

이 강의는 ridge와 lasso 해가 왜 서로 다른 형태를 갖는지 기하학적으로 설명하고, regularization 강도 `lambda`를 선택하기 위한 validation set과 K-fold cross validation을 다룬다.

## Ridge의 기하학

Ridge regression의 목적함수는 다음과 같다.

```text
RSS(w) + lambda * ||w||_2^2
```

이를 constraint 형태로 보면 coefficient vector가 원형 또는 구형 영역 안에 있도록 제한하는 것과 같다.

```text
min RSS(w)
subject to ||w||_2^2 <= c
```

2차원에서 L2 constraint는 원에 가깝다. RSS contour가 이 원과 접하는 지점이 ridge 해가 된다. 원은 모서리가 없으므로 해가 좌표축에 정확히 놓일 가능성이 낮다. 따라서 ridge는 coefficient를 줄이지만 보통 0으로 만들지는 않는다.

## Lasso의 기하학

Lasso의 목적함수는 다음과 같다.

```text
RSS(w) + lambda * ||w||_1
```

Constraint 형태:

```text
min RSS(w)
subject to ||w||_1 <= c
```

2차원에서 L1 constraint는 마름모꼴이다. 마름모의 꼭짓점은 좌표축 위에 있으므로 RSS contour가 꼭짓점에 닿을 가능성이 크다. 이때 어떤 weight는 정확히 0이 된다.

## Ridge와 Lasso 해의 차이

| 관점 | Ridge | Lasso |
|---|---|---|
| 제약 영역 | 원형 | 마름모 |
| coefficient | 작아지지만 대부분 남음 | 일부가 0 |
| 모델 해석 | 모든 feature 활용 | feature selection 가능 |
| correlated feature | weight를 나눠 가질 수 있음 | 하나를 선택하는 경향 |

## Lambda의 의미

`lambda`는 모델이 training data를 맞추는 정도와 단순한 coefficient를 선호하는 정도 사이의 균형을 조절한다.

| `lambda` | 효과 |
|---:|---|
| 작음 | training data에 더 잘 맞춤, overfitting 위험 |
| 큼 | coefficient 축소 강함, underfitting 위험 |

## Validation Set

Training set만으로 `lambda`를 고르면 training error가 가장 낮은 값을 선택하기 쉽다. 따라서 데이터 일부를 validation set으로 분리해 hyperparameter를 선택한다.

```text
training set -> weight 학습
validation set -> lambda 선택
test set -> 최종 성능 평가
```

Test set은 마지막 한 번의 평가를 위해 남겨 두어야 한다.

## K-Fold Cross Validation

데이터가 충분하지 않으면 validation split 하나만으로 성능 추정이 불안정할 수 있다. K-fold cross validation은 데이터를 K개 fold로 나누고, 각 fold를 한 번씩 validation set으로 사용한다.

절차:

1. 데이터를 K개 fold로 나눈다.
2. 하나의 fold를 validation set으로 둔다.
3. 나머지 K-1개 fold로 학습한다.
4. validation error를 기록한다.
5. 모든 fold에 대해 반복한다.
6. 평균 validation error가 가장 낮은 `lambda`를 선택한다.

## Cross Validation의 해석

K-fold cross validation은 데이터 사용 효율을 높인다. 각 sample이 한 번은 validation에, 여러 번은 training에 사용된다. 다만 계산량은 K배 가까이 늘어난다.

## 시험ㆍ복습 체크포인트

- Ridge와 lasso constraint의 기하학적 모양을 그릴 수 있어야 한다.
- Lasso에서 sparse solution이 나오는 이유를 설명할 수 있어야 한다.
- `lambda`가 bias와 variance에 주는 영향을 말할 수 있어야 한다.
- Validation set과 test set의 역할을 구분해야 한다.
- K-fold cross validation 절차를 순서대로 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. 정규화회귀의 기하학과 Cross Validation**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **07. 정규화회귀의 기하학과 Cross Validation**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Lasso Regression과 Feature Selection](06-lasso-regression-feature-selection.md) · 다음: [08. 선형분류와 Logistic Regression](08-logistic-regression-part-1.md)
