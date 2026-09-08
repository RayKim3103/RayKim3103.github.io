---
layout: page
title: "08. 분류기 평가 (Precision · Recall · PR curve)"
permalink: /studies/ai/machine-learning/08-evaluating-classifiers/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `13` 정리·보강
- 표준 ML 교재 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

accuracy만으로 부족한 이유(희귀 positive, FP/FN 비용 차이)와 **precision, recall, PR curve**. 레스토랑 리뷰에서 홍보용 긍정 문장을 고르는 예제.

## 1. Confusion Matrix

|  | 예측 Positive | 예측 Negative |
|---|---|---|
| 실제 Positive | **TP** | **FN** |
| 실제 Negative | **FP** | **TN** |

## 2. 지표

$$
\text{accuracy} = \frac{TP + TN}{\text{total}}
$$
$$
\text{Precision} = \frac{TP}{TP + FP}\quad(\text{positive라 말할 때 신뢰도})
$$
$$
\text{Recall (TPR)} = \frac{TP}{TP + FN}\quad(\text{실제 positive를 놓치지 않는 정도})
$$
$$
\text{Specificity} = \frac{TN}{TN + FP},\qquad
F_1 = \frac{2\cdot\text{P}\cdot\text{R}}{\text{P} + \text{R}}
$$

**accuracy의 한계**: positive가 1%인 데이터에서 "전부 negative" → accuracy 99%. FP와 FN의 비용이 다르면 accuracy 부적절.

## 3. Precision–Recall Tradeoff

분류기가 확률을 출력하면 임계값 $t$ 조정 가능: $P(y{=}\text{pos}\mid x) \ge t \Rightarrow$ positive.

- $t\!\uparrow$ → positive 예측↓ → precision↑, recall↓
- $t\!\downarrow$ → 많이 positive로 → recall↑, FP↑ → precision↓

## 4. PR Curve

임계값을 쓸며 (recall, precision) 궤적을 그린 것. **오른쪽 위에 가까울수록** 좋은 분류기(높은 recall에서도 precision 유지). 양성이 희소할 때 ROC보다 정보량이 크다.

- 모델 비교: 곡선 전체를 보거나, top-$k$ 문제라면 "$k{=}5$에서의 precision" 같은 단일 수치로.
- **핵심**: 응용에서 **무엇이 더 비싼 오류인지** 먼저 정해야 한다.
  - 홍보 문구 선정 — precision 중요(뽑은 문장이 실제로 긍정이어야) vs recall 중요(좋은 문구를 많이 놓치지 않아야) → 서비스 목적에 따라.

## 5. 분류 평가 흐름

1. baseline(random / majority classifier)의 accuracy와 비교
2. accuracy 계산
3. confusion matrix로 오류 유형 확인
4. precision, recall 계산
5. 임계값을 바꿔 tradeoff 분석 (PR curve)

## 복습 질문

- confusion matrix에서 TP/FP/FN/TN을 정의하고 precision·recall 식을 쓰라.
- 임계값을 올리거나 내리면 precision/recall이 각각 어떻게 변하는가?
- accuracy가 오해를 부르는 구체적 예시는?
- 양성이 희소한 데이터에서 PR curve가 ROC보다 나은 이유는?
{% endraw %}

---

이전: [07. 앙상블 학습](07-ensemble-learning.md) · 다음: [09. Instance-Based Learning](09-instance-based-learning.md)
