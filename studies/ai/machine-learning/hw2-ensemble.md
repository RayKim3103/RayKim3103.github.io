---
layout: page
title: "과제 2 — Ensemble (Random Forest · AdaBoost · Precision/Recall)"
permalink: /studies/ai/machine-learning/hw2-ensemble/
sitemap: false
---

- **원본 코드**: [GitHub — Artificial Intelligence / Classifier](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence/Classifier) (`2024_P02_ensemble.ipynb`, `project2.py`)
- EEE3314 Introduction to Artificial Intelligence · Assignment II (2024-11-22 마감)
- 관련 강의 노트: [06. 결정 트리](06-decision-tree.md) · [07. 앙상블 학습](07-ensemble-learning.md) · [08. 분류기 평가](08-evaluating-classifiers.md) · [05. 로지스틱 회귀](05-logistic-regression.md)

{% raw %}
## 개요

**SimpleRandomForest**(bagging)와 **AdaBoost**(boosting)를 직접 구현하고, Precision / Recall / F1 / PR curve로 특성을 분석. weak learner는 `sklearn.tree.DecisionTreeClassifier`만 허용 (`sklearn.ensemble` 금지).

### 데이터셋
- `sklearn.datasets.make_classification(n_samples=2000, n_features=15, n_informative=12, n_redundant=0, n_clusters_per_class=2, random_state=1)`, $$y \in \{-1, +1\}$$, **1500 / 500** split
- decision boundary 시각화용: `make_blobs` (4 centers → 2 class로 병합)

---

## P1. Simple Random Forest (20점)

**Bagging + randomization** — 원본 RF는 노드마다 random feature subset을 쓰지만, 이 과제는 **트리마다 하나의 random feature subset**만 사용:
1. bootstrap sample (복원 추출)
2. random feature subset 선택 → `DecisionTreeClassifier(max_depth=self.max_depth)` 학습
3. 저장: `{'feature_indices': indices_t, 'classifier': f_t}` → `self.classifiers`에 append

**예측 (voting)**:
$$
\hat{\mathbf{y}} = \text{sign}\left(\sum_{t=1}^{T} f_t\big(\mathbf{x}[:, \text{indices}_t]\big)\right)
$$

- **P1.2**: `#classifiers` (1–20), `max_depth` (1–10)에 따른 train/test accuracy 곡선 분석
- **P1.3**: `compute_each_accuracies` — 개별 트리의 train/test accuracy

### 측정 결과
`SimpleRandomForest(50, max_depth=5)` → train **0.9307**, test **0.864**

## P2. AdaBoost (20점)

**Weighted sampling with replacement** — 데이터 가중치 $$\alpha$$에 비례한 확률로 복원 추출, weak learner = `DecisionTreeClassifier(max_depth=1)` (decision **stump**).

| 단계 | 식 |
|---|---|
| classifier 계수 | $$\hat w_t = \dfrac{1}{2}\ln\dfrac{1 - \epsilon_t}{\epsilon_t}$$ |
| 가중치 갱신 | $$\alpha_{t+1}^{i} = \alpha_{t}^{i}\, e^{-\hat w_t\, y_i\, f_t(\mathbf{x})}$$, $$\;y_i \in \{-1, +1\}$$ |
| 정규화 | $$\alpha_t^i \leftarrow \alpha_t^i / \sum_i \alpha_t^i$$ |
| 예측 | $$\hat{\mathbf{y}} = \text{sign}\left(\sum_{t=1}^T \hat w_t\, f_t(\mathbf{x})\right)$$ |

- **P2.2**: weighted error ↔ coefficient 관계 분석 — $$\epsilon_t \uparrow$$ → $$\hat w_t \downarrow$$ (약한 분류기에 낮은 표결권)
- **P2.3**: `compute_staged_accuracies` — 분류기 수에 따른 누적 예측 accuracy

### 측정 결과
`AdaBoost(50)` → train **0.8913**, test **0.866**. 첫 stump 계수 $$\hat w_1 \approx 0.456$$, weighted error가 초반 ~0.287에서 반복될수록 ~0.45–0.48로 상승 (어려운 샘플만 남아 stump가 점점 무력) → 후반 계수는 0.05–0.15로 감소.

## P3. Precision & Recall (20점)

$$
\text{Precision} = \frac{TP}{TP + FP}, \qquad
\text{Recall} = \frac{TP}{TP + FN}, \qquad
F_1 = \frac{2\,PR}{P + R}
$$

`get_precision_recall_curve`: positive class 확률을 threshold로 sweep, x축 recall / y축 precision.

### 측정 결과 (RandomForest, test)
precision **0.891**, recall **0.926**, f1 **0.908**

---

## P4. 분석 (40점)

### P4.1 Bagging vs Boosting
- **Bagging** (RF): 병렬, 독립 bootstrap, **variance 감소**, 개별 트리는 깊어도 됨(low bias)
- **Boosting** (AdaBoost): 순차, 이전 오차에 가중, **bias 감소**, weak learner(stump)를 결합

### P4.2 목적 함수 비교
logistic regression(로그 손실) / RF(분류 오차 기반 voting, 명시적 global objective 없음) / AdaBoost(**exponential loss** $$\sum_i e^{-y_i F(x_i)}$$의 stagewise 최소화).

### P4.3 지표 비교 (make_classification, test)

| classifier | Precision | Recall | F1 |
|---|---:|---:|---:|
| Logistic Regression | 0.8635 | 0.9141 | 0.8880 |
| Simple Random Forest | 0.8910 | 0.9258 | **0.9080** |
| AdaBoost | 0.8566 | 0.8867 | 0.8714 |

### P4.4 결정 경계 / overfitting (make_blobs)
Logistic Regression(선형) / bagged tree(부드러운 비선형) / boosted stump(축 정렬 계단형) 비교. 학습 샘플을 늘렸을 때(200 → 400) 각 모델의 overfitting 완화 정도 대조.

### 참고 — 같은 데이터에서 baseline 비교 (train / test)

| 모델 | train | test |
|---|---:|---:|
| Logistic Regression | 0.887 | 0.882 |
| Decision Stump (depth 1) | 0.713 | 0.728 |
| Decision Tree (full) | **1.000** | 0.800 (overfit) |
| Simple Random Forest (50) | 0.999 | **0.904** |
| AdaBoost (50) | 0.891 | 0.866 |

---

## 핵심 정리

- **Random Forest** = bootstrap + random feature subset + voting → variance 감소. `SimpleRandomForest(50, depth=5)` test 0.864~0.904.
- **AdaBoost** = weighted resampling + stump, $$\hat w_t = \frac12\ln\frac{1-\epsilon_t}{\epsilon_t}$$, $$\alpha^i \leftarrow \alpha^i e^{-\hat w_t y_i f_t}$$ → bias 감소. weighted error가 커질수록 계수 감소.
- full decision tree는 train 1.0 / test 0.80으로 overfit; ensemble이 이를 완화.
- Precision/Recall/F1과 PR curve로 분류기 성격 비교 (이 데이터에선 RF가 F1 최고 0.908).

## 복습 질문

- 이 과제의 SimpleRandomForest가 원본 RF와 다른 점(노드별 vs 트리별 random feature)은?
- AdaBoost에서 $$\hat w_t = \frac12\ln\frac{1-\epsilon_t}{\epsilon_t}$$이 $$\epsilon_t \to 0.5$$일 때 0에 수렴하는 의미, 가중치 갱신식의 부호가 하는 일은?
- bagging은 variance를, boosting은 bias를 줄인다는 말을 두 구현의 train/test 격차로 설명할 수 있나?
- Precision과 Recall이 trade-off인 이유, PR curve로 세 분류기를 어떻게 비교하나?
{% endraw %}

---

이전 과제: [과제 1 — Regression](hw1-regression.md) · 다음 과제: [과제 3 — Neural Networks](hw3-neural-networks.md)
