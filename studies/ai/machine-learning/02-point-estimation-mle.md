---
layout: page
title: "02. 점추정과 Maximum Likelihood Estimation"
permalink: /studies/ai/machine-learning/02-point-estimation-mle/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/02%20Point%20Estimation%20-%20%EC%A0%90%EC%B6%94%EC%A0%95%EA%B3%BC%20MLE.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #statistics #mle #pac-learning #gaussian

관련 노트: [머신러닝 개요](01-introduction.md), [선형회귀 모델과 학습](03-linear-regression-part-1.md)

## 핵심 요약

이 강의는 데이터로부터 미지의 확률분포 파라미터를 추정하는 점추정 문제를 다룬다. 이항분포의 동전 또는 thumbtack 예제에서 maximum likelihood estimation을 유도하고, 연속형 데이터에서는 Gaussian 평균과 분산의 MLE를 계산한다.

## Estimator

Estimation은 관측 데이터가 주어졌을 때 알려지지 않은 parameter의 값을 추정하는 과정이다. Estimator는 데이터에서 parameter estimate를 계산하는 규칙이다.

```text
data D -> estimator -> parameter estimate
```

예를 들어 thumbtack이 앞면으로 떨어질 확률 `theta`를 알고 싶을 때, 여러 번 던진 결과가 데이터가 된다.

## Binomial Distribution

각 시행이 독립이고 성공 확률이 `theta`인 경우, 성공 횟수는 binomial distribution으로 모델링된다.

```text
P(Heads) = theta
P(Tails) = 1 - theta
```

데이터에 head가 `alpha_H`, tail이 `alpha_T`번 관측되면 likelihood는 다음과 같은 형태이다.

```text
P(D | theta) = theta^(alpha_H) * (1 - theta)^(alpha_T)
```

## Probability와 Likelihood

| 개념 | 고정된 것 | 변하는 것 |
|---|---|---|
| Probability | parameter | data |
| Likelihood | data | parameter |

같은 식이라도 `P(y | theta)`를 data의 확률로 보면 probability이고, 관측된 data를 고정한 뒤 `theta`의 함수로 보면 likelihood이다.

## Maximum Likelihood Estimation

MLE는 관측된 데이터를 가장 그럴듯하게 만드는 parameter를 선택한다.

```text
theta_MLE = argmax_theta P(D | theta)
```

곱 형태 likelihood는 계산이 불편하므로 log likelihood를 최대화한다.

```text
theta_MLE = argmax_theta log P(D | theta)
```

이항분포에서 MLE는 성공 비율이다.

```text
theta_MLE = alpha_H / (alpha_H + alpha_T)
```

## max와 argmax

| 표현 | 의미 |
|---|---|
| `max f(x)` | 함수값의 최댓값 |
| `argmax f(x)` | 최댓값을 만드는 입력 `x` |

학습에서는 좋은 model parameter 자체가 필요하므로 보통 `argmax`가 중요하다.

## Hoeffding Bound와 PAC 관점

관측 횟수 `N`이 커질수록 경험적 비율 `theta_MLE`는 실제 확률 `theta`에 가까워진다. Hoeffding inequality는 추정값이 실제값에서 일정 오차 이상 벗어날 확률을 bound한다.

PAC는 Probably Approximately Correct의 약자이다. 핵심 요구는 다음과 같다.

```text
높은 확률로, 실제 parameter와 충분히 가까운 estimate를 얻고 싶다.
```

따라서 필요한 sample 수는 허용 오차와 실패 확률에 의해 결정된다.

## Gaussian Distribution

연속형 데이터는 Gaussian으로 모델링하는 경우가 많다.

```text
X ~ N(mu, sigma^2)
```

Gaussian은 평균 `mu`와 분산 `sigma^2`로 결정된다. Affine transformation을 적용하면 평균과 분산도 그에 맞게 변한다.

## Gaussian의 MLE

i.i.d. sample `x_1, ..., x_N`이 Gaussian에서 나왔다고 가정하면 평균의 MLE는 sample mean이다.

```text
mu_MLE = (1/N) * sum_i x_i
```

분산의 MLE는 다음과 같다.

```text
sigma_MLE^2 = (1/N) * sum_i (x_i - mu_MLE)^2
```

평균 MLE는 unbiased estimator이지만, 분산 MLE는 `1/N`을 사용하므로 true variance에 대해 biased이다. 통계학에서 unbiased sample variance는 보통 `1/(N-1)`을 사용한다.

## 시험ㆍ복습 체크포인트

- Probability와 likelihood의 차이를 설명할 수 있어야 한다.
- Binomial parameter의 MLE가 성공 비율이 되는 과정을 이해해야 한다.
- `max`와 `argmax`의 차이를 구분할 수 있어야 한다.
- Gaussian 평균과 분산의 MLE를 쓸 수 있어야 한다.
- MLE variance estimator가 biased인 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. 점추정과 Maximum Likelihood Estimation**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **02. 점추정과 Maximum Likelihood Estimation**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. 머신러닝 개요](01-introduction.md) · 다음: [03. 선형회귀 모델과 학습](03-linear-regression-part-1.md)
