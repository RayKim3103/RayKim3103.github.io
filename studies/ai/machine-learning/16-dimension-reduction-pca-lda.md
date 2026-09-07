---
layout: page
title: "16. Dimension Reduction: PCA와 LDA"
permalink: /studies/ai/machine-learning/16-dimension-reduction-pca-lda/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/16%20Dimension%20Reduction%20-%20PCA%EC%99%80%20LDA.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #dimension-reduction #pca #lda #curse-of-dimensionality

관련 노트: [Clustering과 K-Means](15-clustering-k-means.md), [Neural Networks Part 1: 다층신경망 기초](17-neural-networks-part-1.md)

## 핵심 요약

이 강의는 curse of dimensionality를 완화하기 위한 dimension reduction을 다룬다. PCA는 label 없이 variance를 최대한 보존하는 projection을 찾고, LDA는 class label을 사용해 class separability를 보존하는 projection을 찾는다.

## Curse of Dimensionality

Feature dimension이 증가하면 공간의 부피가 급격히 커진다. 같은 granularity로 각 축을 나누면 필요한 sample 수가 지수적으로 증가한다.

문제점:

- 고차원 공간에서 데이터가 희박해진다.
- 거리 기반 방법이 불안정해진다.
- 모델이 쉽게 overfitting된다.
- 계산량과 저장량이 증가한다.

## Dimension Reduction

Dimension reduction은 원래 feature vector `x`를 더 낮은 차원의 vector `y`로 변환한다.

```text
x in R^N -> y in R^M,  M < N
```

목표는 정보 손실을 최소화하면서 더 간단한 표현을 얻는 것이다.

## Feature Selection과 Feature Extraction

| 방법 | 설명 |
|---|---|
| Feature selection | 기존 feature 중 일부 선택 |
| Feature extraction | 기존 feature를 조합해 새로운 feature 생성 |

PCA와 LDA는 feature extraction 방법이다.

## PCA

PCA(Principal Components Analysis)는 데이터의 variance를 가장 많이 보존하는 orthogonal projection 축을 찾는다.

### 목적

```text
projection된 sample들의 variance를 최대화하는 방향 w를 찾는다.
```

첫 번째 principal component는 분산이 가장 큰 방향이고, 두 번째 component는 첫 번째와 직교하면서 남은 분산을 가장 많이 설명하는 방향이다.

## PCA 계산

1. 데이터 평균을 빼서 center한다.
2. covariance matrix를 계산한다.
3. covariance matrix의 eigenvector와 eigenvalue를 구한다.
4. 큰 eigenvalue에 대응하는 eigenvector를 선택한다.
5. 데이터를 선택한 eigenvector span으로 projection한다.

```text
Sigma w = lambda w
```

Eigenvalue가 큰 방향은 데이터 variance를 많이 설명한다. 작은 eigenvalue 방향은 noise나 중복 정보일 수 있다.

## PCA의 한계

PCA는 class label을 사용하지 않는다. 따라서 variance가 큰 방향이 classification에 좋은 방향이라는 보장은 없다. Class를 구분하는 정보가 variance가 작은 방향에 있을 수도 있다.

## LDA

LDA(Linear Discriminant Analysis)는 class separability를 보존하면서 차원을 줄이는 supervised dimension reduction이다.

두 class 문제에서 LDA는 class mean 사이의 거리는 크게, class 내부 scatter는 작게 만드는 projection 방향을 찾는다.

## Fisher Criterion

Fisher linear discriminant는 다음 비율을 최대화한다.

```text
J(w) = between-class scatter / within-class scatter
```

행렬 형태로는 between-class scatter matrix `S_B`와 within-class scatter matrix `S_W`를 사용한다.

```text
maximize (w^T S_B w) / (w^T S_W w)
```

최적화는 generalized eigenvalue problem으로 이어진다.

## LDA의 한계

- Class별 분포가 Gaussian이고 unimodal하다는 가정에 의존한다.
- 구분 정보가 평균 차이가 아니라 분산 차이에 있을 때 실패할 수 있다.
- 복잡한 nonlinear class boundary에는 적합하지 않을 수 있다.

## PCA와 LDA 비교

| 항목 | PCA | LDA |
|---|---|---|
| 학습 종류 | Unsupervised | Supervised |
| label 사용 | 사용 안 함 | 사용 |
| 보존 목표 | 전체 variance | class separability |
| 한계 | 분류에 필요한 방향을 놓칠 수 있음 | 분포 가정과 class 구조에 민감 |

## 시험ㆍ복습 체크포인트

- Curse of dimensionality가 왜 문제인지 설명할 수 있어야 한다.
- PCA의 eigenvalue/eigenvector 해석을 이해해야 한다.
- PCA가 label을 사용하지 않는다는 한계를 말할 수 있어야 한다.
- Fisher criterion에서 between-class와 within-class scatter의 의미를 설명할 수 있어야 한다.
- PCA와 LDA를 목적과 데이터 요구사항 관점에서 비교할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **16. Dimension Reduction: PCA와 LDA**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **16. Dimension Reduction: PCA와 LDA**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [15. Clustering과 K-Means](15-clustering-k-means.md) · 다음: [17. Neural Networks Part 1: 다층신경망 기초](17-neural-networks-part-1.md)
