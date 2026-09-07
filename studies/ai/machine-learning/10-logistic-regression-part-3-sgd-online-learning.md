---
layout: page
title: "10. SGD와 Online Learning"
permalink: /studies/ai/machine-learning/10-logistic-regression-part-3-sgd-online-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/10%20Logistic%20Regression%20Part%203%20-%20SGD%EC%99%80%20Online%20Learning.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #logistic-regression #sgd #online-learning #optimization

관련 노트: [분류 Overfitting과 L2 정규화](09-logistic-regression-part-2-overfitting-l2.md), [Decision Tree](11-decision-tree.md)

## 핵심 요약

이 강의는 logistic regression 학습을 대규모 데이터로 확장하기 위한 stochastic gradient descent를 다룬다. 전체 데이터 gradient를 매번 계산하는 batch gradient ascent는 느리므로, 한 data point 또는 mini-batch의 gradient로 자주 update하는 SGD를 사용한다. 이어서 streaming data에 적합한 online learning을 설명한다.

## Batch Gradient Ascent의 비용

Logistic regression의 log likelihood gradient는 모든 training data point의 contribution을 합한 것이다.

```text
gradient ll(w) = sum_i gradient_i
```

데이터가 매우 크면 매 update마다 전체 데이터를 훑어야 하므로 계산 비용이 크다. 한 번의 update는 정확하지만 update 횟수가 적어 학습이 느릴 수 있다.

## Stochastic Gradient Ascent

Stochastic gradient ascent는 하나의 data point 또는 작은 mini-batch에서 계산한 gradient로 weight를 갱신한다.

```text
w <- w + eta * gradient_i
```

개별 gradient는 noisy하지만 평균적으로는 전체 gradient 방향을 따른다. 그래서 path는 흔들리지만 자주 update할 수 있어 큰 데이터에서는 빠르게 좋은 해에 접근한다.

## Logistic Regression의 SGD

각 data point `(x_i, y_i)`에 대해 다음을 반복한다.

1. 현재 weight로 `P(y_i | x_i, w)`를 계산한다.
2. 해당 sample의 log likelihood gradient를 계산한다.
3. weight를 update한다.
4. 다음 sample로 넘어간다.

L2 regularization이 있으면 update에 weight decay 항이 포함된다.

```text
w <- w + eta * (sample_gradient - 2*lambda*w)
```

## Batch Gradient와 SGD 비교

| 항목 | Batch gradient | Stochastic gradient |
|---|---|---|
| 한 update 비용 | 큼, 전체 데이터 사용 | 작음, 일부 데이터 사용 |
| 방향 정확도 | 높음 | noisy |
| 대규모 데이터 | 느림 | 효율적 |
| 수렴 경로 | 매끄러움 | 흔들림 |

## 왜 SGD가 동작하는가

전체 gradient는 각 data point가 주는 작은 방향의 합이다. 임의로 선택한 sample의 gradient는 완벽한 방향은 아니지만, 여러 update를 거치면 평균적으로 steepest direction을 따라간다.

학습 후반에는 learning rate를 줄이면 gradient noise로 인한 진동을 줄일 수 있다.

## Online Learning

Online learning은 데이터가 한 번에 모두 주어지지 않고 시간에 따라 도착하는 상황에서 모델을 계속 갱신하는 방식이다.

예:

- 광고 클릭 예측
- 추천 시스템
- 검색 ranking
- 실시간 사용자 행동 모델링

새로운 sample이 들어올 때마다 모델을 update할 수 있으므로, 데이터 분포가 시간에 따라 변하는 문제에 적합하다.

## Learning Rate Scheduling

SGD는 learning rate에 민감하다. 너무 크면 발산하거나 optimum 근처에서 계속 흔들리고, 너무 작으면 학습이 느리다. 따라서 시간이 지날수록 learning rate를 줄이는 decay schedule을 자주 사용한다.

## 시험ㆍ복습 체크포인트

- Batch gradient와 stochastic gradient의 계산 비용 차이를 설명할 수 있어야 한다.
- SGD가 noisy하지만 평균적으로 좋은 방향을 따르는 이유를 이해해야 한다.
- L2-regularized SGD update에 regularization term이 어떻게 들어가는지 말할 수 있어야 한다.
- Online learning이 batch learning과 다른 점을 예시와 함께 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. SGD와 Online Learning**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **10. SGD와 Online Learning**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. 분류 Overfitting과 L2 정규화](09-logistic-regression-part-2-overfitting-l2.md) · 다음: [11. Decision Tree](11-decision-tree.md)
