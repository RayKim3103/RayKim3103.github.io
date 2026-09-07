---
layout: page
title: "08. 선형분류와 Logistic Regression"
permalink: /studies/ai/machine-learning/08-logistic-regression-part-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/08%20Logistic%20Regression%20Part%201%20-%20%EC%84%A0%ED%98%95%EB%B6%84%EB%A5%98%EC%99%80%20%EB%A1%9C%EC%A7%80%EC%8A%A4%ED%8B%B1%ED%9A%8C%EA%B7%80.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #classification #logistic-regression #cross-entropy #softmax

관련 노트: [정규화회귀의 기하학과 Cross Validation](07-regularized-regression-geometry-cross-validation.md), [분류 Overfitting과 L2 정규화](09-logistic-regression-part-2-overfitting-l2.md)

## 핵심 요약

이 강의는 classification 문제와 linear classifier, logistic regression을 설명한다. 핵심은 score를 threshold로 자르는 단순 분류에서 나아가 class probability를 모델링하고, likelihood 또는 cross entropy를 최적화해 classifier를 학습하는 것이다.

## Classification 문제

Classification은 출력이 범주형 label인 문제이다.

예:

- spam filtering
- image classification
- sentiment analysis
- personalized medical diagnosis
- brain activity decoding

Binary classification에서는 `y`가 `+1` 또는 `-1`처럼 두 class 중 하나이다. Multiclass classification에서는 class가 세 개 이상이다.

## Linear Classifier

입력 `x`를 feature vector `h(x)`로 바꾸고, weight vector `w`와 내적해 score를 계산한다.

```text
Score(x) = w^T h(x)
y_hat = sign(Score(x))
```

결정경계는 `Score(x)=0`인 점들의 집합이다. Feature 공간에서 이 결정경계는 hyperplane이다.

## Threshold Classifier의 문제

단어 기반 sentiment classifier에서 positive word와 negative word 목록을 직접 만들면 다음 문제가 생긴다.

- 단어 중요도를 사람이 일일이 정해야 한다.
- 문맥과 조합 효과를 반영하기 어렵다.
- 예측 confidence를 얻기 어렵다.

따라서 weight를 데이터에서 학습하고, score를 probability로 해석하는 방법이 필요하다.

## Odds와 Logit

확률 `p`에 대한 odds는 다음과 같다.

```text
odds = p / (1 - p)
```

logit은 odds의 log이다.

```text
logit(p) = log(p / (1 - p))
```

Logistic regression은 log-odds가 feature의 linear combination이라고 가정한다.

```text
logit(P(y=+1 | x)) = w^T h(x)
```

## Sigmoid Function

Sigmoid는 real-valued score를 0과 1 사이 확률로 바꾼다.

```text
sigmoid(z) = 1 / (1 + exp(-z))
P(y=+1 | x, w) = sigmoid(w^T h(x))
```

score가 0이면 확률은 0.5이고, score가 양수로 커질수록 positive class 확률이 커진다.

## Logistic Regression 학습

학습 데이터의 label이 관측될 likelihood를 최대화한다.

```text
w_hat = argmax_w log P(D | w)
```

Linear regression과 달리 logistic regression은 closed-form solution이 없으므로 gradient ascent 또는 gradient descent로 최적화한다.

## Cross Entropy Loss

Log likelihood를 최대화하는 것은 negative log likelihood를 최소화하는 것과 같다. Binary logistic regression의 cross entropy loss는 다음과 같다.

```text
J(w) =
- 1[y=+1] log P(y=+1 | x,w)
- 1[y=-1] log P(y=-1 | x,w)
```

정답 class에 높은 확률을 주면 loss가 작고, 정답 class에 낮은 확률을 주면 loss가 매우 커진다.

## Entropy, Cross Entropy, KL Divergence

| 개념 | 의미 |
|---|---|
| Entropy | 분포 자체의 불확실성 |
| Cross entropy | 실제 분포를 다른 분포로 encoding할 때의 평균 비용 |
| KL divergence | 두 분포의 차이, extra cost |

분류 학습에서는 model distribution이 true label distribution에 가까워지도록 cross entropy를 줄인다.

## Multiclass Logistic Regression

Class가 여러 개이면 softmax를 사용한다.

```text
P(y=c | x,w) = exp(score_c) / sum_k exp(score_k)
```

각 class마다 score를 만들고, softmax가 모든 class 확률의 합을 1로 정규화한다.

## Accuracy와 Confusion Matrix

Accuracy는 전체 sample 중 맞춘 비율이다.

```text
accuracy = # correct / # total
error = # mistakes / # total
```

하지만 false positive와 false negative의 비용이 다르면 accuracy만으로 모델을 평가하기 어렵다. Confusion matrix는 예측 label과 실제 label의 조합을 보여준다.

## 시험ㆍ복습 체크포인트

- Linear classifier의 score와 decision boundary를 설명할 수 있어야 한다.
- Logistic regression이 class probability를 어떻게 모델링하는지 알아야 한다.
- Odds, logit, sigmoid의 관계를 이해해야 한다.
- Cross entropy loss가 정답 class 확률과 어떻게 연결되는지 설명할 수 있어야 한다.
- Binary와 multiclass logistic regression의 차이를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. 선형분류와 Logistic Regression**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **08. 선형분류와 Logistic Regression**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. 정규화회귀의 기하학과 Cross Validation](07-regularized-regression-geometry-cross-validation.md) · 다음: [09. 분류 Overfitting과 L2 정규화](09-logistic-regression-part-2-overfitting-l2.md)
