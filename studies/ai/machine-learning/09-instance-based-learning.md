---
layout: page
title: "09. Instance-Based Learning (k-NN · Kernel Regression)"
permalink: /studies/ai/machine-learning/09-instance-based-learning/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `14` 정리·보강

{% raw %}
## 개요

전역 parametric 함수를 학습하는 대신, **query 근처의 training instance**로 local 예측. 1-NN, k-NN, weighted k-NN, kernel regression, local linear regression.

## 1. Global vs Local (Lazy Learning)

$$
\text{query } x_q \;\longrightarrow\; \text{가까운 training examples 검색} \;\longrightarrow\; \text{local average / local model}
$$
학습이 "게으르다"(lazy) — training은 데이터 저장뿐, 계산은 **예측 시점**에. 부동산 중개인이 비슷한 집의 최근 거래가를 참고하는 방식.

## 2. Distance Metric

$$
d(x, x') = \sqrt{\sum_j (x_j - x'_j)^2}\quad(\text{Euclidean, } L_2)
$$
그 외: $$L_1$$(Manhattan), $$L_\infty$$, Mahalanobis $$\sqrt{(x-x')^\top \Sigma^{-1}(x-x')}$$, cosine distance(문서), Hamming(범주형).

**feature scale**이 다르면 큰 scale feature가 거리를 지배 → **정규화(z-score) 또는 scaled Euclidean 필수**. 관련 없는 feature도 거리를 오염시킴 → feature 선택/가중이 중요.

## 3. 1-NN / k-NN

- **1-NN**: query에 가장 가까운 point 1개의 label/output 그대로.
  - training error 0, 하지만 **noise·outlier에 민감**, 경계 불규칙.
  - feature space를 각 point의 최근접 영역으로 나눈 것 = **Voronoi tessellation**.
  - 이론: $$N\to\infty$$에서 1-NN error $$\le 2 \times$$ Bayes error.
- **k-NN**:
  $$
  \hat y = \frac{1}{k}\sum_{j \in N_k(x_q)} y_j\ (\text{regression}),\qquad
  \hat y = \text{majority vote}\ (\text{classification, 확률} = \tfrac{1}{k}\sum \mathbb{1}[y_j = c])
  $$
  - $$k$$↓ → 경계 복잡, **variance↑ bias↓**. $$k$$↑ → 부드러움, **bias↑ variance↓**. $$k = N$$이면 전체 평균(상수).
  - $$k$$는 **홀수**(이진 분류 tie 방지), CV로 선택.
- **밀도 추정 관점**: $$k$$-NN 분류는 $$\hat p(c\mid x) \propto k_c / (k \cdot V)$$ ($$V$$ = $$k$$번째 이웃까지 부피)로 볼 수 있다.

## 4. Weighted k-NN / Kernel Regression

$$
\hat y = \frac{\sum_j w_j\, y_j}{\sum_j w_j}
$$
- **weighted k-NN**: 이웃 $$k$$개에만, 가까울수록 큰 weight (예: $$w_j = 1/d_j$$ 또는 $$1/d_j^2$$).
- **Kernel regression (Nadaraya–Watson)**: $$k$$개가 아니라 **모든** training point에 kernel weight
  $$
  w_i = K\!\left(\frac{d(x_i, x_q)}{\lambda}\right)
  $$
  대표 kernel: boxcar $$\mathbb{1}[|u|\le1]$$, Gaussian $$e^{-u^2/2}$$, Epanechnikov $$\frac{3}{4}(1-u^2)_+$$, tri-cube $$(1-|u|^3)^3_+$$.
  bandwidth $$\lambda$$↓ → 가까운 점만(**variance↑**), $$\lambda$$↑ → 많은 점(**bias↑**). **kernel 종류보다 $$\lambda$$ 선택이 훨씬 중요** — CV로.
- **local linear regression (LOESS)**: query 주변에 상수가 아닌 **locally weighted linear model**을 적합 → 데이터 경계·추세 구간에서 kernel regression의 편향(경계에서 한쪽으로 치우침)을 보정.

## 5. 계산 비용과 Curse of Dimensionality

- **비용**: naive k-NN 예측은 $$O(ND)$$. 가속: **kd-tree / ball-tree**(저차원), **LSH / 근사 최근접(ANN, HNSW)**(고차원), 벡터 양자화.
- **Curse of dimensionality**: 차원↑ → 임의의 두 점 거리가 균등화(가장 가까운 이웃과 가장 먼 점의 거리 비 $$\to 1$$) → local neighborhood가 의미를 잃음. 고정 반경 안에 점이 있으려면 $$N$$이 지수적으로 필요.
- 이미지 raw pixel distance는 semantic similarity를 잘 반영 못 함 → **학습된 embedding 공간에서 k-NN**을 쓰는 게 실무.

## 복습 질문

- lazy learning의 의미, 1-NN과 k-NN의 예측 절차, Voronoi tessellation은?
- $$k$$와 kernel bandwidth $$\lambda$$가 bias/variance에 주는 영향(양 극단 포함)은?
- distance 기반 방법에서 feature scaling·무관 feature가 왜 치명적인가?
- Nadaraya–Watson kernel regression 식과, "kernel 종류보다 $$\lambda$$가 중요"한 이유, local linear regression이 개선하는 지점은?
- curse of dimensionality가 거리 기반 방법을 무너뜨리는 메커니즘과, 실무의 우회책(embedding, ANN)은?
{% endraw %}

---

이전: [08. 분류기 평가](08-evaluating-classifiers.md) · 다음: [10. 군집화 (K-Means)](10-clustering-k-means.md)
