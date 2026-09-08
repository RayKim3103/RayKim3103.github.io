---
layout: page
title: "10. 군집화 (Clustering · K-Means)"
permalink: /studies/ai/machine-learning/10-clustering-k-means/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `15` 정리·보강
- 표준 ML 교재 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

label 없는 데이터에서 유사한 sample끼리 그룹을 찾는 **비지도학습**. 대표 알고리즘 **k-means**는 assignment ↔ center update를 번갈아 하는 **coordinate descent**로 이해할 수 있다.

## 1. Cluster의 정의와 한계

"같은 cluster 안은 가깝고, 다른 cluster와는 멀다" — 중심 + 퍼짐으로 기술. 하지만 비구형·얽힌·밀도차 cluster에서는 k-means 가정이 깨진다.

## 2. K-Means Algorithm

cluster 수 $k$를 미리 정하고:

1. center $\mu_1,\dots,\mu_k$ 초기화
2. **Assignment step**: 각 point를 가장 가까운 center에 할당
3. **Update step**: 각 cluster에 속한 point의 평균으로 center 갱신
4. assignment가 안 바뀌거나 objective 감소가 작아질 때까지 반복

### Objective (cluster heterogeneity)
$$
J = \sum_{k=1}^{K} \sum_{i \in C_k} \lVert x_i - \mu_k \rVert^2
$$

### Coordinate descent 관점
| step | 고정 | 최적화 |
|---|---|---|
| Assignment | centers | 각 point의 cluster |
| Update | assignments | 각 center |
두 step 모두 $J$를 증가시키지 않음 → **수렴 보장**. 단 **local optimum**에 수렴 가능 → 초기값에 민감.

## 3. Initialization

나쁜 초기 center → 나쁜 clustering. 완화: 여러 초기값으로 반복 실행, 또는 **k-means++**(첫 center 무작위, 이후 center는 기존 center에서 먼 point를 확률적으로 선택).

## 4. k 선택

$k$↑ → heterogeneity $J$는 계속 감소($k{=}N$이면 $J{=}0$). 따라서 $J$만으로 고르면 과도하게 큰 $k$. 대안: **elbow method**, downstream 성능, domain knowledge.

## 5. 한계

- cluster가 대략 구형·비슷한 크기라는 가정
- outlier에 민감
- distance scale에 민감 → feature normalization 필요
- $k$를 미리 지정해야 함

## 복습 질문

- supervised vs unsupervised learning의 차이는?
- k-means의 assignment/update step과, coordinate descent로서 수렴이 보장되는 이유는?
- $k$가 증가하면 objective $J$가 어떻게 변하며, 그래서 $k$를 어떻게 고르는가?
- k-means가 잘 안 맞는 데이터 형태는?
{% endraw %}

---

이전: [09. Instance-Based Learning](09-instance-based-learning.md) · 다음: [11. 차원 축소 (PCA · LDA)](11-dimension-reduction-pca-lda.md)
