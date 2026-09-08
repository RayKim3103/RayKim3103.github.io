---
layout: page
title: "09. Instance-Based Learning (k-NN · Kernel Regression)"
permalink: /studies/ai/machine-learning/09-instance-based-learning/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `14` 정리·보강
- 표준 ML 교재(ISL·ESL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

전역 parametric 함수를 학습하는 대신, **query 근처의 training instance**로 local 예측. 1-NN, k-NN, weighted k-NN, kernel regression, local linear regression.

## 1. Global vs Local

$$
\text{query } x_q \;\longrightarrow\; \text{가까운 training examples 검색} \;\longrightarrow\; \text{local average / local model}
$$
학습이 "게으르다"(lazy) — 예측 시점에 계산.

## 2. Distance Metric

$$
d(x, x') = \sqrt{\sum_j (x_j - x'_j)^2}\quad(\text{Euclidean})
$$
**feature scale**이 다르면 큰 scale feature가 거리를 지배 → scaled Euclidean 또는 정규화 필수.

## 3. 1-NN / k-NN

- **1-NN**: query에 가장 가까운 point 1개의 label/output 그대로. training엔 잘 맞지만 noise에 민감, 경계 불규칙. feature space를 각 point 최근접 영역으로 나눈 것 = **Voronoi tessellation**.
- **k-NN**:
  $$
  \hat y = \text{avg}\{y_j : j \in \text{k nearest}\}\ (\text{regression}),\qquad
  \hat y = \text{majority vote}\ (\text{classification})
  $$
  $k$↓ → variance↑, $k$↑ → bias↑.

## 4. Weighted k-NN / Kernel Regression

$$
\hat y = \frac{\sum_j w_j\, y_j}{\sum_j w_j}
$$
- **weighted k-NN**: 가까운 이웃에 큰 weight.
- **kernel regression**: k개가 아니라 **모든** training point에 kernel weight $w_i = K_\lambda(d(x_i, x_q))$.
  bandwidth $\lambda$↓ → 가까운 점만(variance↑), $\lambda$↑ → 많은 점(bias↑). **kernel 종류보다 bandwidth 선택이 더 중요**한 경우가 많다.
- **local linear regression**: query 주변에 상수가 아닌 **locally weighted linear model**을 적합 → 경계·추세 구간에서 유리.

## 5. Curse of Dimensionality

차원↑ → 대부분의 점이 서로 멀어짐 → local neighborhood가 비거나 의미 약화. 거리 기반 방법이 고차원에서 불안정. 이미지의 raw pixel distance는 semantic similarity를 잘 반영하지 못함.

## 복습 질문

- 1-NN과 k-NN의 예측 절차, Voronoi tessellation의 의미는?
- $k$와 kernel bandwidth $\lambda$가 bias/variance에 주는 영향은?
- distance 기반 방법에서 feature scaling이 중요한 이유는?
- kernel regression과 local linear regression의 차이, 그리고 curse of dimensionality는?
{% endraw %}

---

이전: [08. 분류기 평가](08-evaluating-classifiers.md) · 다음: [10. 군집화 (K-Means)](10-clustering-k-means.md)
