---
layout: page
title: "11. 차원 축소 (PCA · LDA)"
permalink: /studies/ai/machine-learning/11-dimension-reduction-pca-lda/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `16` 정리·보강

{% raw %}
## 개요

**curse of dimensionality** 완화. **PCA**는 label 없이 variance를 최대한 보존하는 projection, **LDA**는 class label을 써서 class separability를 보존하는 projection.

## 1. Curse of Dimensionality

차원↑ → 공간 부피 급증. 같은 granularity로 각 축을 $$b$$구간으로 나누면 셀 수 $$b^D$$ → 필요 sample 수 지수 증가. 결과: 데이터 희박, 거리 균등화(거리 기반 방법 붕괴), overfitting 쉬움, 계산·저장 증가.

## 2. Feature Selection vs Extraction

| | 설명 |
|---|---|
| Feature **selection** | 기존 feature 중 일부 선택 (Lasso, stepwise) |
| Feature **extraction** | 기존 feature를 조합해 새 feature 생성 — **PCA, LDA**, autoencoder |

$$
x \in \mathbb{R}^N \;\xrightarrow{\;W \in \mathbb{R}^{M\times N}\;}\; y = Wx \in \mathbb{R}^M,\quad M < N
$$

## 3. PCA — 유도

데이터를 중심화($$\tilde x_i = x_i - \bar x$$). 단위벡터 $$w$$에 투영한 값 $$w^\top\tilde x_i$$의 **표본 분산**:
$$
\mathrm{Var} = \frac{1}{N}\sum_i (w^\top \tilde x_i)^2 = w^\top \Sigma w,
\qquad \Sigma = \frac{1}{N}\sum_i \tilde x_i \tilde x_i^\top
$$
$$\max_w w^\top\Sigma w$$ s.t. $$\lVert w\rVert = 1$$ → Lagrangian $$w^\top\Sigma w - \lambda(w^\top w - 1)$$, 미분 = 0:
$$
\boxed{\;\Sigma w = \lambda w\;}
$$
→ $$w$$는 $$\Sigma$$의 **eigenvector**, 투영 분산 = eigenvalue $$\lambda$$. 따라서 **가장 큰 eigenvalue의 eigenvector = 1st PC**, 그다음 직교 방향 = 2nd PC …

- **동치 관점**: PCA는 rank-$$M$$ 선형 **재구성 오차 $$\sum_i \lVert \tilde x_i - W^\top W \tilde x_i\rVert^2$$을 최소화**하는 부분공간과 같다 (분산 최대 = 잔차 최소).
- **explained variance ratio**: $$\lambda_m / \sum_j \lambda_j$$. 누적이 90~95% 되는 $$M$$을 자주 선택.
- **SVD로 계산**: $$\tilde X = U S V^\top$$ → PC = $$V$$의 열, $$\lambda_m = s_m^2/N$$. covariance를 직접 만들지 않아 수치적으로 안정.
- **whitening**: 투영 후 각 축을 $$1/\sqrt{\lambda_m}$$로 스케일 → 등방 공분산.
- **전처리**: feature scale이 다르면 표준화 후 PCA (아니면 큰 scale feature가 PC 지배).

### 한계
label을 안 씀 → variance가 큰 방향이 **classification에 좋은 방향이라는 보장 없음**. 구분 정보가 작은 분산 방향에 있을 수 있고, PCA가 그 방향을 버릴 수 있다. 선형이라 곡면 manifold엔 부적합(→ kernel PCA, t-SNE, UMAP).

## 4. LDA — Fisher Criterion

**supervised**. 2-class: class 평균 $$\mu_1, \mu_2$$, 각 class covariance. 투영 $$z = w^\top x$$ 후:
$$
J(w) = \frac{(w^\top(\mu_1 - \mu_2))^2}{w^\top S_W w}
= \frac{w^\top S_B w}{w^\top S_W w}
$$
- **within-class scatter** $$S_W = \sum_{c}\sum_{i \in c}(x_i - \mu_c)(x_i - \mu_c)^\top$$ — 작게
- **between-class scatter** $$S_B = \sum_c N_c (\mu_c - \bar\mu)(\mu_c - \bar\mu)^\top$$ — 크게

최대화 → $$S_B w = \lambda S_W w$$ (**generalized eigenvalue problem**). 2-class 해:
$$
\boxed{\;w \propto S_W^{-1}(\mu_1 - \mu_2)\;}
$$
$$C$$개 class면 최대 $$C - 1$$개의 discriminant 방향 ($$S_B$$의 rank $$\le C - 1$$).

### 한계
class별 분포가 **Gaussian·unimodal·동일 공분산**이라는 가정. 구분 정보가 평균 차이가 아니라 **분산·모양 차이**에 있으면 실패. nonlinear 경계엔 부적합(→ QDA는 공분산 class별 허용, kernel LDA).

## 5. 비교

| | PCA | LDA |
|---|---|---|
| 학습 종류 | Unsupervised | Supervised |
| label | 사용 안 함 | 사용 |
| 보존 목표 | 전체 variance | class separability (Fisher ratio) |
| 최대 차원 | $$\min(N, D)$$ | $$C - 1$$ |
| 해 | $$\Sigma$$의 eigen-분해 | $$S_W^{-1}S_B$$ generalized eigen-분해 |
| 한계 | 분류에 필요한 방향을 놓칠 수 있음 | Gaussian·동일공분산 가정, class 구조 민감 |

## 복습 질문

- curse of dimensionality가 "필요 sample 수 지수 증가"로 이어지는 이유는?
- PCA에서 $$\Sigma w = \lambda w$$를 "투영 분산 최대화 + $$\lVert w\rVert = 1$$"의 Lagrangian으로 유도하라. 재구성 오차 최소화와 왜 동치인가?
- explained variance ratio로 $$M$$을 고르는 법, SVD로 PCA를 계산하는 이점은?
- Fisher criterion $$J(w) = \frac{w^\top S_B w}{w^\top S_W w}$$에서 $$S_B$$, $$S_W$$의 정의와, 2-class 해 $$w \propto S_W^{-1}(\mu_1 - \mu_2)$$는?
- PCA와 LDA를 목적·label 요구·최대 차원·한계로 비교하라.
{% endraw %}

---

이전: [10. 군집화 (K-Means)](10-clustering-k-means.md) · 다음: [12. 신경망 (Neural Networks)](12-neural-networks.md)
