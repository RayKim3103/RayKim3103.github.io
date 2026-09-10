---
layout: page
title: "06. 결정 트리 (Decision Tree)"
permalink: /studies/ai/machine-learning/06-decision-tree/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `11` 정리·보강

{% raw %}
## 개요

**Nonparametric** 접근의 대표. feature에 대한 질문을 순서대로 던져 데이터를 분할하고, leaf에서 class label(또는 확률)을 예측한다. greedy split selection, 불순도 척도, 연속 feature threshold, overfitting, pruning이 핵심.

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
5. stopping condition 만족 시 leaf → 다수 class(또는 class 비율) 저장

**Decision stump** = 깊이 1 트리(한 번만 split). Greedy는 각 단계 최선을 고르되 전역 최적은 보장 X (전역 최적 트리 찾기는 NP-hard).

### 불순도 척도

node $$m$$에서 class $$c$$의 비율을 $$p_{mc}$$ 라 하자.

| 척도 | 정의 | 특징 |
|---|---|---|
| **Classification error** | $$1 - \max_c p_{mc}$$ | 직관적이나 순도 변화에 **둔감** (구간별 선형) → split 선택에 부적합 |
| **Gini impurity** | $$\sum_c p_{mc}(1 - p_{mc}) = 1 - \sum_c p_{mc}^2$$ | 미분 가능, CART 기본 |
| **Entropy** | $$-\sum_c p_{mc}\log_2 p_{mc}$$ | information gain의 기반 (ID3/C4.5) |

세 척도 모두 순수 node에서 0, 균등 분포에서 최대. **error는 오목함이 약해** 두 자식이 error 합은 같지만 순도가 크게 개선되는 split을 못 고른다 → 실제 split 선택엔 Gini/entropy를 쓴다.

### Split 품질 = 불순도 감소 (information gain)
$$
\Delta I = I(\text{parent}) - \sum_{k \in \text{children}} \frac{N_k}{N}\, I(k)
\qquad(\text{weighted average})
$$
$$\Delta I$$가 최대인 (feature, threshold)를 선택.

### Stopping conditions
- node의 모든 sample이 같은 class
- split할 feature 없음 (또는 모든 feature 값이 동일)
- split해도 $$\Delta I$$가 충분히 안 줄어듦
- 최대 depth 도달 / node sample 수 $$<$$ min_samples_split / leaf sample 수 $$<$$ min_samples_leaf

## 3. 연속 feature — Threshold Split

$$
h_j(x) \le \tau \quad \text{vs} \quad h_j(x) > \tau
$$
threshold 후보: 정렬된 feature 값들의 **인접 중간점**($$N - 1$$개). 각 후보에서 $$\Delta I$$ 계산 → 최적 $$\tau$$. 정렬 후 누적 통계를 스캔하면 feature당 $$O(N\log N)$$.

## 4. 결정경계와 확률

- feature space를 **축에 평행한 초직사각형** 영역으로 분할(계단형 경계). 로지스틱 회귀의 단일 선형 경계와 대조. XOR·대각선 경계는 여러 split을 계단으로 근사.
- leaf 확률: $$P(y{=}c\mid \text{leaf}) = p_{\text{leaf},c}$$ (leaf 안 class $$c$$ 비율). 예측은 argmax. (regression tree면 leaf의 $$y$$ 평균, 불순도는 분산/MSE.)

## 5. Overfitting과 Pruning

깊은 트리 → training을 거의 완벽히 맞춤 → leaf가 작아져 noise까지 외움 → test 성능↓. (트리는 high variance, low bias 모델 → [07. 앙상블](07-ensemble-learning.md)에서 bagging으로 완화.)

**전략 두 가지**:
- **Pre-pruning (early stopping)**: max_depth·min_samples 등으로 성장 제한. 단, "지금은 이득 없지만 다음 split에서 큰 이득" 케이스를 놓칠 수 있음(horizon effect).
- **Post-pruning (cost-complexity pruning)**: 큰 트리를 학습 후 아래에서 위로 병합.
$$
C_\alpha(T) = \text{Error}(T) + \alpha\, |T_{\text{leaves}}|
$$
$$\alpha$$(단순함 penalty)를 키우며 $$C_\alpha$$가 낮아지는 subtree 시퀀스를 만들고, **validation/CV로 $$\alpha$$ 선택**.

## 6. 장단점

| 장점 | 단점 |
|---|---|
| 해석 쉬움(if-then 규칙), 시각화 | 단일 트리는 **불안정**(데이터 조금 바뀌면 구조 급변) |
| feature scaling 불필요, 결측·범주형 자연 처리 | 축 정렬 경계 → 대각선 관계 비효율 |
| 비선형·상호작용 자동 포착 | 깊으면 overfit, 얕으면 underfit |
| 학습·예측 빠름 | 클래스 불균형·회귀 외삽에 약함 |

## 복습 질문

- decision tree가 nonparametric인 이유, 그리고 greedy 학습이 전역 최적을 보장하지 못하는 이유는?
- classification error / Gini / entropy를 정의하고, **split 선택에 error를 안 쓰는 이유**를 설명하라.
- information gain(불순도 감소)의 weighted-average 식과, 연속 feature threshold 후보 만드는 법은?
- pre-pruning과 cost-complexity post-pruning의 차이, $$C_\alpha(T) = \text{Error}(T) + \alpha|T_{\text{leaves}}|$$에서 $$\alpha$$를 고르는 법은?
- 단일 결정 트리가 high-variance인 이유와, 그것이 앙상블(bagging)로 이어지는 맥락은?
{% endraw %}

---

이전: [05. 로지스틱 회귀](05-logistic-regression.md) · 다음: [07. 앙상블 학습](07-ensemble-learning.md)
