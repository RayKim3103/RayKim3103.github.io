---
layout: page
title: "13. Evaluating Classifiers: Precision과 Recall"
permalink: /studies/ai/machine-learning/13-evaluating-classifiers-precision-recall/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/13%20Evaluating%20Classifiers%20-%20Precision%20Recall.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #classification #precision #recall #confusion-matrix #pr-curve

관련 노트: [Ensemble Learning](12-ensemble-learning-bagging-random-forest-boosting.md), [Instance-Based Learning: Nearest Neighbor와 Kernel Regression](14-instance-based-learning.md)

## 핵심 요약

이 강의는 classifier 평가에서 accuracy만으로 충분하지 않은 이유를 설명하고, precision, recall, precision-recall curve를 다룬다. Positive 문장을 찾아 레스토랑 리뷰 홍보에 쓰는 예제를 통해 false positive와 false negative의 비용이 다를 수 있음을 보여준다.

## Accuracy의 한계

Accuracy는 전체 sample 중 맞춘 비율이다.

```text
accuracy = (TP + TN) / total
```

하지만 positive class가 희귀하거나, false positive와 false negative의 비용이 다르면 accuracy는 좋은 평가 지표가 아니다.

예를 들어 긍정 리뷰 문장을 홍보용으로 고르는 상황에서는 부정 문장을 긍정으로 잘못 고르는 false positive가 특히 문제가 될 수 있다.

## Confusion Matrix

Binary classification 결과는 네 가지로 나뉜다.

| 실제 / 예측 | Positive 예측 | Negative 예측 |
|---|---:|---:|
| 실제 Positive | TP | FN |
| 실제 Negative | FP | TN |

| 용어 | 의미 |
|---|---|
| TP | positive를 positive로 맞춤 |
| FP | negative를 positive로 잘못 예측 |
| FN | positive를 negative로 놓침 |
| TN | negative를 negative로 맞춤 |

## Precision

Precision은 positive라고 예측한 것 중 실제 positive의 비율이다.

```text
precision = TP / (TP + FP)
```

Precision이 높다는 것은 모델이 positive라고 말할 때 신뢰할 수 있다는 뜻이다.

## Recall

Recall은 실제 positive 중 모델이 찾아낸 비율이다.

```text
recall = TP / (TP + FN)
```

Recall이 높다는 것은 positive sample을 많이 놓치지 않는다는 뜻이다.

## Precision과 Recall의 Tradeoff

Classifier가 class probability를 출력하면 threshold를 조정할 수 있다.

```text
P(y=positive | x) >= t -> positive
```

Threshold를 높이면 positive 예측이 줄어 precision은 올라갈 수 있지만 recall은 떨어진다. Threshold를 낮추면 많은 sample을 positive로 잡아 recall은 올라가지만 false positive가 늘어 precision이 낮아질 수 있다.

## Precision-Recall Curve

Precision-recall curve는 threshold를 바꾸며 precision과 recall의 변화를 그린 것이다. Curve가 오른쪽 위에 가까울수록 좋은 classifier이다.

완벽한 모델은 높은 recall에서도 precision을 유지한다.

## 모델 비교

여러 classifier를 비교할 때 precision-recall curve 전체를 보거나, 하나의 숫자로 요약해야 할 수 있다. 예를 들어 top-k 결과를 보는 문제에서는 `k=5`에서 precision을 비교할 수 있다.

모델 선택에서 중요한 점은 실제 응용에서 무엇이 더 비싼 오류인지 정하는 것이다.

## Positive Sentiment 예제

레스토랑 리뷰에서 홍보 문구를 찾는 경우:

- Precision 중요: 홍보용으로 뽑은 문장이 실제로 긍정이어야 한다.
- Recall 중요: 좋은 홍보 문구를 많이 놓치지 않아야 한다.

둘 중 무엇을 더 중시할지는 서비스 목적에 따라 달라진다.

## 지금까지의 분류 평가 흐름

1. Baseline으로 random classifier나 majority classifier를 생각한다.
2. Accuracy를 계산한다.
3. Confusion matrix로 오류 유형을 본다.
4. Precision과 recall을 계산한다.
5. Threshold를 바꿔 tradeoff를 분석한다.

## 시험ㆍ복습 체크포인트

- TP, FP, FN, TN을 confusion matrix에서 찾을 수 있어야 한다.
- Precision과 recall 공식을 외우고 의미를 설명할 수 있어야 한다.
- Threshold를 올리거나 내릴 때 precision/recall이 어떻게 변하는지 이해해야 한다.
- Accuracy가 불충분한 예시를 들 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. Evaluating Classifiers: Precision과 Recall**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 분류 주제에서는 decision boundary, score/probability 변환, threshold 선택, class imbalance를 함께 보아야 한다.
- 정확도만 보지 말고 false positive와 false negative의 실제 비용을 문제 맥락에서 비교한다.
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
- **13. Evaluating Classifiers: Precision과 Recall**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. Ensemble Learning](12-ensemble-learning-bagging-random-forest-boosting.md) · 다음: [14. Instance-Based Learning: Nearest Neighbor와 Kernel Regression](14-instance-based-learning.md)
