---
layout: page
title: "12. Ensemble Learning"
permalink: /studies/ai/machine-learning/12-ensemble-learning-bagging-random-forest-boosting/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/12%20Ensemble%20Learning%20-%20Bagging%20Random%20Forest%20Boosting.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #ensemble #bagging #random-forest #boosting #adaboost

관련 노트: [Decision Tree](11-decision-tree.md), [Evaluating Classifiers: Precision과 Recall](13-evaluating-classifiers-precision-recall.md)

## 핵심 요약

이 강의는 여러 약한 classifier를 결합해 강한 classifier를 만드는 ensemble learning을 다룬다. Bagging은 bootstrap sample로 모델을 여러 개 만들어 variance를 줄이고, random forest는 decision tree bagging에 feature randomness를 더한다. Boosting은 이전 모델이 틀린 sample에 더 집중해 순차적으로 classifier를 학습한다.

## Ensemble의 아이디어

약한 learner 하나는 성능이 제한적일 수 있다. 하지만 여러 learner가 서로 다른 오류를 만든다면, 예측을 평균하거나 투표함으로써 더 안정적인 모델을 만들 수 있다.

```text
ensemble prediction = 여러 모델 예측의 결합
```

## Bagging

Bagging은 Bootstrap Aggregation의 줄임말이다.

절차:

1. Training set에서 복원추출로 bootstrap dataset을 여러 개 만든다.
2. 각 dataset에 model을 학습한다.
3. Regression은 평균, classification은 majority vote로 예측한다.

Bagging은 model variance를 줄이는 데 특히 효과적이다. Decision tree처럼 data 변화에 민감한 모델과 잘 맞는다.

## Bootstrap Sample

Bootstrap은 원래 training data에서 같은 크기의 dataset을 복원추출로 만드는 방법이다. 어떤 sample은 여러 번 뽑히고, 어떤 sample은 빠질 수 있다. 각 learner가 조금씩 다른 데이터를 보므로 ensemble diversity가 생긴다.

## Random Forest

Random forest는 decision tree ensemble이다. Bagging에 더해 각 split에서 전체 feature가 아니라 무작위로 선택한 feature subset만 고려한다.

### 효과

- Tree 사이의 correlation을 줄인다.
- 평균 또는 투표의 variance 감소 효과를 키운다.
- 많은 feature가 있는 문제에서 강력하다.

## Boosting

Boosting은 learner를 순차적으로 학습한다. 각 단계에서 이전 learner들이 틀린 sample의 weight를 키워 다음 learner가 어려운 sample에 집중하게 한다.

```text
잘 맞춘 sample -> weight 감소
틀린 sample -> weight 증가
```

Boosting은 bias와 variance를 모두 줄일 수 있지만, 너무 오래 반복하면 결국 overfitting될 수 있다.

## AdaBoost

AdaBoost는 대표적인 boosting algorithm이다.

### 학습 절차

1. 모든 training sample에 같은 weight를 준다.
2. 현재 weight를 고려해 weak classifier를 학습한다.
3. Weighted error를 계산한다.
4. classifier의 coefficient를 계산한다.
5. 틀린 sample의 weight를 증가시키고 맞춘 sample의 weight를 감소시킨다.
6. weight를 normalize한다.
7. 정해진 반복 수 `T`까지 반복한다.

최종 classifier는 weak classifier들의 weighted sum이다.

```text
score(x) = sum_t alpha_t * f_t(x)
y_hat = sign(score(x))
```

## AdaBoost 계수

각 classifier의 weight `alpha_t`는 weighted error가 낮을수록 커진다. 즉, 더 잘 맞춘 weak learner가 최종 decision에 더 큰 영향을 준다.

## Boosting의 Training Error

AdaBoost 이론은 각 weak classifier가 random보다 조금만 나아도 반복이 진행되며 training error가 빠르게 줄어들 수 있음을 보여준다. 실제로 boosting은 training error를 0까지 낮추는 경우가 많다.

## 언제 멈출 것인가

Boosting도 무한정 반복하면 overfitting이 생길 수 있으므로 최대 component 수 `T`를 선택해야 한다. 이 값은 validation set이나 cross validation으로 고른다.

## Bagging과 Boosting 비교

| 항목 | Bagging | Boosting |
|---|---|---|
| 학습 방식 | 병렬적, 독립적 | 순차적 |
| 데이터 weight | bootstrap sample | 틀린 sample에 더 큰 weight |
| 주효과 | variance 감소 | bias 감소와 어려운 sample 집중 |
| 대표 모델 | Random forest | AdaBoost |

## 시험ㆍ복습 체크포인트

- Weak learner를 ensemble로 결합하는 이유를 설명할 수 있어야 한다.
- Bagging과 bootstrap sample의 관계를 이해해야 한다.
- Random forest가 split마다 feature subset을 쓰는 이유를 말할 수 있어야 한다.
- AdaBoost의 sample weight update 직관을 설명할 수 있어야 한다.
- Bagging과 boosting의 차이를 비교할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. Ensemble Learning**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **12. Ensemble Learning**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11. Decision Tree](11-decision-tree.md) · 다음: [13. Evaluating Classifiers: Precision과 Recall](13-evaluating-classifiers-precision-recall.md)
