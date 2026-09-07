---
layout: page
title: "04. 성능평가와 Bias-Variance Tradeoff"
permalink: /studies/ai/machine-learning/04-linear-regression-part-2-bias-variance/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/04%20Linear%20Regression%20Part%202%20-%20%EC%84%B1%EB%8A%A5%ED%8F%89%EA%B0%80%EC%99%80%20Bias%20Variance.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #linear-regression #generalization #bias-variance #test-error

관련 노트: [선형회귀 모델과 학습](03-linear-regression-part-1.md), [Ridge Regression](05-ridge-regression.md)

## 핵심 요약

이 강의는 학습된 regression model을 어떻게 평가할 것인가를 다룬다. Training error는 학습 데이터에 맞춘 정도를 측정하지만 일반화 성능을 과대평가하기 쉽다. Test error는 generalization error의 근사이며, 모델 복잡도에 따른 overfitting과 bias-variance tradeoff를 이해하는 것이 핵심이다.

## Loss Function

Loss function은 실제값 `y`와 예측값 `f_w(x)` 사이의 손실을 수치화한다.

예:

```text
squared error = (y - f_w(x))^2
absolute error = |y - f_w(x)|
```

완벽한 예측이면 loss는 0이다. 실제 문제에서는 loss가 의사결정 비용과 연결되므로 어떤 loss를 선택하는지가 중요하다.

## Training Error

Training error는 학습에 사용한 데이터에서 평균 loss를 계산한 값이다.

```text
Training error = (1/N_train) * sum_i L(y_i, f_w_hat(x_i))
```

모델 복잡도가 커질수록 training error는 보통 감소한다. 높은 차수의 polynomial은 학습 데이터 점을 매우 잘 통과할 수 있기 때문이다.

## Training Error의 한계

학습된 parameter `w_hat`은 training data에 맞춰 선택된다. 따라서 같은 데이터에서 평가한 training error는 실제 새 데이터 성능보다 낙관적이다.

```text
training error는 모델 선택과 평가가 같은 데이터에서 일어나기 때문에 과대평가된다.
```

## Generalization Error

Generalization error는 아직 보지 못한 모든 가능한 데이터에 대한 기대 손실이다.

```text
E_{x,y}[ L(y, f_w_hat(x)) ]
```

현실에서는 전체 데이터 분포를 모르므로 정확히 계산할 수 없다. 따라서 별도 test set으로 근사한다.

## Test Error

Test set은 모델 fitting에 사용하지 않은 데이터이다.

```text
Test error = (1/N_test) * sum_{test} L(y_i, f_w_hat(x_i))
```

좋은 test set은 실제 배포 환경에서 만날 데이터 분포를 대표해야 한다.

## Model Complexity와 Error

모델 복잡도가 낮으면 데이터의 구조를 충분히 표현하지 못해 bias가 크다. 모델 복잡도가 너무 높으면 학습 데이터의 noise까지 맞춰 variance가 커진다.

| 복잡도 | Training error | Test/generalization error |
|---|---|---|
| 너무 낮음 | 높음 | 높음, underfitting |
| 적절함 | 중간 또는 낮음 | 낮음 |
| 너무 높음 | 매우 낮음 | 높음, overfitting |

## Training/Test Split

전체 데이터를 training set과 test set으로 나눈다. Training set은 parameter fitting에 사용하고, test set은 최종 성능 추정에 사용한다.

test point가 너무 적으면 generalization error estimate의 variance가 커진다. 반대로 test set이 너무 크면 training data가 줄어 모델 학습이 약해질 수 있다.

## 세 가지 Error Source

예측 오차는 세 요소로 분해해서 볼 수 있다.

| 요소 | 의미 | 줄이는 방법 |
|---|---|---|
| Noise | 데이터 자체의 불확실성, irreducible error | 모델로 제거 불가 |
| Bias | 평균 모델이 true function에서 벗어난 정도 | 모델 표현력 증가 |
| Variance | training set 변화에 따라 모델이 흔들리는 정도 | regularization, 데이터 증가 |

## Bias-Variance Tradeoff

모델 복잡도가 증가하면 bias는 줄어드는 경향이 있지만 variance는 증가한다. 최적의 모델은 bias와 variance 사이의 균형점에 있다.

```text
Expected prediction error = noise + bias^2 + variance
```

이 분해는 왜 training error만 최소화하는 것이 위험한지 설명한다.

## 시험ㆍ복습 체크포인트

- Loss, training error, test error, generalization error를 구분할 수 있어야 한다.
- Training error가 optimistic한 이유를 설명할 수 있어야 한다.
- Underfitting과 overfitting을 모델 복잡도 관점에서 해석할 수 있어야 한다.
- bias, variance, noise의 의미를 각각 말할 수 있어야 한다.
- bias-variance tradeoff 그래프를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. 성능평가와 Bias-Variance Tradeoff**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **04. 성능평가와 Bias-Variance Tradeoff**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. 선형회귀 모델과 학습](03-linear-regression-part-1.md) · 다음: [05. Ridge Regression](05-ridge-regression.md)
