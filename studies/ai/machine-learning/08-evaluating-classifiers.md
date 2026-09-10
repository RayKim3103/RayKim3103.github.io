---
layout: page
title: "08. 분류기 평가 (Precision · Recall · PR curve)"
permalink: /studies/ai/machine-learning/08-evaluating-classifiers/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `13` 정리·보강

{% raw %}
## 개요

accuracy만으로 부족한 이유(희귀 positive, FP/FN 비용 차이)와 **precision, recall, F1, PR/ROC curve**. 레스토랑 리뷰에서 홍보용 긍정 문장을 고르는 예제.

## 1. Confusion Matrix

|  | 예측 Positive | 예측 Negative |
|---|---|---|
| 실제 Positive | **TP** | **FN** (Type II error, "놓침") |
| 실제 Negative | **FP** (Type I error, "오경보") | **TN** |

## 2. 지표

$$
\text{accuracy} = \frac{TP + TN}{\text{total}},\qquad
\text{error rate} = \frac{FP + FN}{\text{total}}
$$
$$
\text{Precision} = \frac{TP}{TP + FP}\quad(\text{positive라 말할 때 신뢰도}, \text{PPV})
$$
$$
\text{Recall (TPR, Sensitivity)} = \frac{TP}{TP + FN}\quad(\text{실제 positive를 놓치지 않는 정도})
$$
$$
\text{Specificity (TNR)} = \frac{TN}{TN + FP},\qquad
\text{FPR} = 1 - \text{Specificity} = \frac{FP}{FP + TN}
$$
$$
F_1 = \frac{2\,\text{P}\cdot\text{R}}{\text{P} + \text{R}} = \text{P·R의 조화평균},\qquad
F_\beta = (1 + \beta^2)\frac{\text{P·R}}{\beta^2\text{P} + \text{R}}
$$
$$\beta > 1$$이면 recall 중시, $$\beta < 1$$이면 precision 중시. 조화평균이라 한쪽이 낮으면 $$F_1$$도 낮다.

**accuracy의 한계**: positive가 1%인 데이터에서 "전부 negative" → accuracy 99%인데 recall 0. 클래스 불균형·비용 비대칭이면 accuracy 부적절 → **majority classifier를 baseline**으로 반드시 비교.

## 3. Precision–Recall Tradeoff

분류기가 확률/score를 출력하면 임계값 $$t$$ 조정 가능: $$\;P(y{=}\text{pos}\mid x) \ge t \Rightarrow$$ positive.

- $$t\!\uparrow$$ → positive 예측 수↓ → FP↓ → **precision↑**, 그러나 TP도 줄어 **recall↓**
- $$t\!\downarrow$$ → 많이 positive로 → **recall↑**, FP↑ → **precision↓**
- $$t = 0$$: 전부 positive (recall 1, precision = base rate). $$t = 1$$: 전부 negative.

## 4. PR Curve vs ROC Curve

| | 축 | 좋은 방향 | 언제 |
|---|---|---|---|
| **PR curve** | x = recall, y = precision | 오른쪽 위 | **positive가 희소**할 때 정보량 큼 (FP 영향이 두드러짐) |
| **ROC curve** | x = FPR, y = TPR(recall) | 왼쪽 위 | 클래스 균형, threshold 무관 순위 품질 |

- **요약 수치**: PR → **Average Precision (AP)** = $$\sum_n (R_n - R_{n-1})P_n$$; ROC → **AUC** = 임의의 positive가 임의의 negative보다 높은 score를 받을 확률.
- 불균형 데이터에서 ROC는 낙관적으로 보일 수 있음(TN이 커서 FPR가 잘 안 오름) → PR-AUC 병행.
- 모델 비교: 곡선 전체를 보거나, top-$$k$$ 문제라면 "$$k{=}5$$에서의 precision" 같은 운영점 단일 수치로.

## 5. 분류 평가 흐름

1. **baseline**(random / majority classifier)의 accuracy와 비교
2. accuracy·error rate 계산
3. confusion matrix로 오류 유형(FP vs FN) 확인
4. precision, recall, $$F_1$$(또는 $$F_\beta$$) 계산
5. 임계값을 쓸며 PR/ROC curve → 응용에 맞는 **운영점(threshold)** 선택
6. **어느 오류가 더 비싼지 먼저 정의** — 홍보 문구 선정이면 precision 중시(뽑은 게 실제로 긍정), 질병 스크리닝이면 recall 중시(놓치면 치명적)

## 복습 질문

- confusion matrix에서 TP/FP/FN/TN을 정의하고 precision·recall·specificity·FPR 식을 쓰라.
- $$F_1$$이 조화평균인 이유, $$F_\beta$$에서 $$\beta$$의 역할은?
- 임계값 $$t$$를 올리면 precision/recall이 각각 어떻게 변하는지, $$t=0$$과 $$t=1$$의 극단은?
- 양성이 희소한 데이터에서 PR curve가 ROC보다 나은 이유, AP와 AUC의 의미는?
- accuracy가 오해를 부르는 구체적 예와, majority-classifier baseline을 봐야 하는 이유는?
{% endraw %}

---

이전: [07. 앙상블 학습](07-ensemble-learning.md) · 다음: [09. Instance-Based Learning](09-instance-based-learning.md)
