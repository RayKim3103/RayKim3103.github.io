---
layout: page
title: "14. Instance-Based Learning: Nearest Neighbor와 Kernel Regression"
permalink: /studies/ai/machine-learning/14-instance-based-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/14%20Instance%20Based%20Learning%20-%20%EC%B5%9C%EA%B7%BC%EC%A0%91%EC%9D%B4%EC%9B%83%EA%B3%BC%20%EC%BB%A4%EB%84%90%ED%9A%8C%EA%B7%80.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #knn #kernel-regression #nonparametric #local-model

관련 노트: [Evaluating Classifiers: Precision과 Recall](13-evaluating-classifiers-precision-recall.md), [Clustering과 K-Means](15-clustering-k-means.md)

## 핵심 요약

이 강의는 전역적인 parametric function을 학습하는 대신, query point 근처의 training instance를 사용해 local prediction을 수행하는 instance-based learning을 다룬다. 1-NN, k-NN, weighted k-NN, kernel regression, local linear regression이 핵심이다.

## Global Fit과 Local Fit

Linear regression이나 polynomial regression은 전체 데이터에 하나의 global function을 맞춘다. 반면 instance-based learning은 query가 들어온 뒤 그 주변 data point를 찾아 예측한다.

```text
query x_q -> 가까운 training examples 검색 -> local average 또는 local model
```

부동산 중개인이 비슷한 집의 최근 거래가를 찾아 집값을 추정하는 방식이 좋은 예이다.

## Distance Metric

Nearest neighbor를 찾으려면 similarity 또는 distance를 정의해야 한다. 가장 기본은 Euclidean distance이다.

```text
distance(x, x') = sqrt(sum_j (x[j] - x'[j])^2)
```

Feature scale이 다르면 큰 scale feature가 distance를 지배하므로 scaled Euclidean distance나 feature normalization을 사용한다.

## 1-Nearest Neighbor

1-NN은 query에 가장 가까운 training point 하나의 label 또는 output을 그대로 사용한다.

절차:

1. query와 모든 training point의 거리를 계산한다.
2. 가장 작은 거리의 point를 찾는다.
3. regression은 그 point의 `y`, classification은 그 point의 class를 예측한다.

1-NN은 training data에는 잘 맞지만 noise에 민감하고 decision boundary가 매우 불규칙할 수 있다.

## Voronoi Tessellation

1-NN은 feature space를 각 training point에 가장 가까운 영역으로 나눈다. 이 partition을 Voronoi tessellation이라고 한다. 각 영역 안의 모든 query는 같은 nearest neighbor를 가진다.

## k-Nearest Neighbors

k-NN은 가장 가까운 `k`개 이웃을 사용한다.

Regression:

```text
y_hat = average of y values among k nearest neighbors
```

Classification:

```text
y_hat = majority vote among k nearest neighbors
```

`k`가 작으면 variance가 크고, `k`가 크면 bias가 커진다.

## Weighted k-NN

가까운 이웃일수록 더 큰 weight를 주는 방법이다.

```text
y_hat = sum_j weight_j * y_j / sum_j weight_j
```

단순 평균보다 query와 매우 가까운 point의 영향을 더 크게 반영한다.

## Kernel Regression

Kernel regression은 k개의 neighbor만이 아니라 모든 training point에 kernel weight를 부여한다.

```text
weight_i = Kernel_lambda(distance(x_i, x_q))
y_hat = weighted average
```

Kernel bandwidth `lambda`는 local neighborhood의 폭을 정한다.

| `lambda` | 효과 |
|---:|---|
| 작음 | 가까운 점만 영향, variance 증가 |
| 큼 | 많은 점이 영향, bias 증가 |

Kernel 종류보다 bandwidth 선택이 더 중요할 때가 많다.

## Local Linear Regression

Kernel regression이 query 주변에서 상수 함수를 맞추는 방식이라면, local linear regression은 query 주변 데이터에 locally weighted linear model을 맞춘다. 경계 부근이나 trend가 있는 구간에서 단순 local average보다 나을 수 있다.

## k-NN Classification

Classification에서도 같은 원리를 쓴다. 문서나 이메일을 embedding space에 놓고, query와 가까운 labeled example들의 다수결로 class를 정한다.

이미지에서는 pixel distance를 그대로 쓰는 k-NN이 직관적이지만, raw pixel distance는 semantic similarity를 잘 반영하지 못할 수 있다.

## Curse of Dimensionality

Feature dimension이 커지면 가까운 이웃을 찾기가 어려워진다. 고차원에서는 대부분의 점들이 서로 멀어지고, 충분한 data가 없으면 local neighborhood가 비어 있거나 의미가 약해진다.

## 시험ㆍ복습 체크포인트

- 1-NN과 k-NN의 예측 절차를 설명할 수 있어야 한다.
- `k`와 kernel bandwidth가 bias/variance에 미치는 영향을 이해해야 한다.
- Feature scaling이 distance 기반 방법에서 중요한 이유를 말할 수 있어야 한다.
- Kernel regression과 local linear regression의 차이를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14. Instance-Based Learning: Nearest Neighbor와 Kernel Regression**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **14. Instance-Based Learning: Nearest Neighbor와 Kernel Regression**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. Evaluating Classifiers: Precision과 Recall](13-evaluating-classifiers-precision-recall.md) · 다음: [15. Clustering과 K-Means](15-clustering-k-means.md)
