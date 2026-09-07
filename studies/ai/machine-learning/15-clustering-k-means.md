---
layout: page
title: "15. Clustering과 K-Means"
permalink: /studies/ai/machine-learning/15-clustering-k-means/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/15%20Clustering%20-%20K%20Means%20%EA%B5%B0%EC%A7%91%ED%99%94.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #clustering #unsupervised-learning #k-means

관련 노트: [Instance-Based Learning: Nearest Neighbor와 Kernel Regression](14-instance-based-learning.md), [Dimension Reduction: PCA와 LDA](16-dimension-reduction-pca-lda.md)

## 핵심 요약

이 강의는 label이 없는 데이터에서 유사한 sample끼리 그룹을 찾는 clustering을 다룬다. 대표 알고리즘인 k-means는 cluster assignment와 center update를 번갈아 수행하는 coordinate descent 방식으로 이해할 수 있다.

## Clustering의 목적

Clustering은 unsupervised learning task이다. 정답 label이 주어지지 않은 상태에서 데이터 내부 구조를 발견한다.

예:

- 문서를 주제별로 묶기
- 사용자 행동 패턴을 군집화해 추천에 활용
- 이미지 검색 결과를 시각적 주제별로 정리
- 데이터 탐색과 visualization

## Cluster의 정의

Cluster는 보통 중심과 퍼짐으로 설명된다.

```text
같은 cluster 안의 point는 서로 가깝고,
다른 cluster의 point와는 멀다.
```

하지만 실제 데이터에는 비구형 cluster, 서로 얽힌 cluster, 밀도 차이가 큰 cluster가 있어 k-means 가정이 맞지 않을 수 있다.

## k-Means Algorithm

k-means는 cluster 수 `k`를 미리 정하고, 각 cluster center와 data assignment를 반복적으로 갱신한다.

절차:

1. Cluster center `mu_1, ..., mu_k`를 초기화한다.
2. 각 data point를 가장 가까운 center에 할당한다.
3. 각 cluster에 속한 point의 평균으로 center를 갱신한다.
4. assignment가 더 이상 바뀌지 않거나 objective 감소가 작아질 때까지 반복한다.

## Objective Function

k-means가 줄이려는 값은 cluster heterogeneity이다.

```text
sum over clusters sum over points in cluster ||x_i - mu_k||^2
```

즉, 각 point가 속한 cluster center에서 얼마나 떨어져 있는지를 제곱거리로 측정한다.

## Coordinate Descent 관점

k-means는 두 종류의 변수를 번갈아 최적화한다.

| 단계 | 고정 | 최적화 |
|---|---|---|
| Assignment step | centers | 각 point의 cluster |
| Update step | assignments | 각 cluster center |

각 단계는 objective를 증가시키지 않으므로 알고리즘은 수렴한다. 다만 local optimum에 수렴할 수 있어 초기값에 민감하다.

## Initialization

초기 center가 나쁘면 좋지 않은 clustering에 수렴할 수 있다. 따라서 여러 초기값으로 k-means를 반복 실행하거나 k-means++ 같은 초기화 방법을 사용한다.

## k 선택

`k`가 커질수록 cluster heterogeneity는 계속 감소한다. 극단적으로 `k=N`이면 각 point가 자기 cluster를 가져 training objective는 0이 된다. 따라서 objective 감소만으로 `k`를 고르면 과도하게 큰 값을 고를 위험이 있다.

대표적인 선택 방법:

- elbow method
- validation 목적에 맞춘 downstream 성능
- domain knowledge

## k-Means의 한계

- cluster가 대략 구형이고 비슷한 크기라는 가정에 잘 맞는다.
- outlier에 민감하다.
- distance scale에 민감하므로 feature normalization이 필요하다.
- `k`를 미리 정해야 한다.

## 시험ㆍ복습 체크포인트

- Supervised learning과 unsupervised learning의 차이를 설명할 수 있어야 한다.
- k-means의 assignment/update step을 순서대로 말할 수 있어야 한다.
- k-means objective와 cluster heterogeneity의 의미를 이해해야 한다.
- `k`가 증가하면 objective가 어떻게 변하는지 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **15. Clustering과 K-Means**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **15. Clustering과 K-Means**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [14. Instance-Based Learning: Nearest Neighbor와 Kernel Regression](14-instance-based-learning.md) · 다음: [16. Dimension Reduction: PCA와 LDA](16-dimension-reduction-pca-lda.md)
