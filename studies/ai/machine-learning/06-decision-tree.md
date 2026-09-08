---
layout: page
title: "06. 결정 트리 (Decision Tree)"
permalink: /studies/ai/machine-learning/06-decision-tree/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `11` 정리·보강
- 표준 ML 교재(ISL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**Nonparametric** 접근의 대표. feature에 대한 질문을 순서대로 던져 데이터를 분할하고, leaf에서 class label(또는 확률)을 예측한다. greedy split selection, 연속 feature threshold, overfitting, pruning이 핵심.

## 1. Parametric vs Nonparametric

| | 설명 | 예 |
|---|---|---|
| Parametric | 고정된 수의 파라미터로 요약 | linear/logistic regression |
| Nonparametric | 데이터↑ → 모델 복잡도도 커질 수 있음 | decision tree, k-NN, kernel method |

## 2. 학습 — Greedy Recursive Partitioning

예: 대출 신청자를 safe/risky로. node마다 하나의 질문(Credit good? Income > threshold? …).

1. 현재 node에 도달한 데이터를 본다
2. 가능한 feature split을 평가
3. **불순도 감소가 가장 큰 split** 선택
4. child node에 대해 재귀
5. stopping condition 만족 시 leaf

**Decision stump** = 깊이 1 트리(한 번만 split). Greedy는 각 단계 최선을 고르되 전역 최적은 보장 X.

### Split 품질
node의 classification error = (다수 class로 예측 시 틀린 비율) $= \dfrac{\#\text{mistakes}}{\#\text{data in node}}$.
split 품질 = child node들의 **weighted average error**. (일반적으로는 Gini impurity $\sum_c p_c(1-p_c)$ 나 entropy $-\sum_c p_c\log p_c$ 를 쓰기도 한다.)

### Stopping conditions
- node의 모든 sample이 같은 class
- split할 feature 없음
- split해도 error가 충분히 안 줄어듦
- 최대 depth 도달
- node sample 수가 너무 작음

## 3. 연속 feature — Threshold Split

$$
h_j(x) \le \tau \quad \text{vs} \quad h_j(x) > \tau
$$
threshold 후보: 정렬된 feature 값들의 **중간점**. 각 후보에서 split error를 계산해 최적 $\tau$ 선택.

## 4. 결정경계와 확률

- feature space를 **축에 평행한 영역**들로 분할(계단형 경계). 로지스틱 회귀의 단일 선형 경계와 대조.
- leaf 확률: $P(y{=}c\mid \text{leaf}) = $ (leaf 안 class $c$ 비율). 예측은 argmax.

## 5. Overfitting과 Pruning

깊은 트리 → training을 거의 완벽히 맞춤 → leaf가 작아져 noise까지 외움 → test 성능↓.

**Pruning**: 큰 트리를 학습 후 불필요한 split 제거.
$$
C(T) = \text{Error}(T) + \lambda\, L(T)
$$
$L(T)$ = leaf 수(복잡도), $\lambda$ = 단순함에 대한 penalty. split을 제거한 작은 트리의 $C$가 더 낮으면 prune.

## 복습 질문

- decision tree가 nonparametric인 이유는?
- greedy split selection 절차를 순서대로, 그리고 전역 최적을 보장하지 못하는 이유는?
- 연속 feature에서 threshold 후보를 만드는 방법은?
- overfitting을 pruning objective $C(T) = \text{Error}(T) + \lambda L(T)$로 어떻게 다루는가?
{% endraw %}

---

이전: [05. 로지스틱 회귀](05-logistic-regression.md) · 다음: [07. 앙상블 학습](07-ensemble-learning.md)
