---
layout: page
title: "11. 차원 축소 (PCA · LDA)"
permalink: /studies/ai/machine-learning/11-dimension-reduction-pca-lda/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `16` 정리·보강
- 표준 ML 교재(ESL) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**curse of dimensionality** 완화. **PCA**는 label 없이 variance를 최대한 보존하는 projection, **LDA**는 class label을 써서 class separability를 보존하는 projection.

## 1. Curse of Dimensionality

차원↑ → 공간 부피 급증, 같은 granularity로 각 축을 나누면 필요 sample 수 지수 증가. → 데이터 희박, 거리 기반 방법 불안정, overfitting 쉬움, 계산·저장 증가.

## 2. Feature Selection vs Extraction

| | 설명 |
|---|---|
| Feature selection | 기존 feature 중 일부 선택 |
| Feature **extraction** | 기존 feature를 조합해 새 feature 생성 — **PCA, LDA** |

$$
x \in \mathbb{R}^N \;\longrightarrow\; y \in \mathbb{R}^M,\quad M < N
$$

## 3. PCA (Principal Components Analysis)

데이터의 variance를 가장 많이 보존하는 **직교 projection 축**을 찾음.

1. 데이터 평균을 빼서 center
2. covariance matrix $\Sigma = \frac{1}{N}\sum_i (x_i-\bar x)(x_i-\bar x)^\top$
3. $\Sigma$의 eigen-분해: $\Sigma w = \lambda w$
4. 큰 eigenvalue에 대응하는 eigenvector 선택 (= 분산이 큰 방향)
5. 그 span으로 projection

- 1st PC = 분산 최대 방향, 2nd PC = 1st와 직교하며 남은 분산 최대 …
- 작은 eigenvalue 방향 = noise·중복 가능.
- **한계**: label을 안 씀 → variance가 큰 방향이 classification에 좋은 방향이라는 보장 없음(구분 정보가 작은 분산 방향에 있을 수도).

## 4. LDA (Linear Discriminant Analysis)

**supervised**. class 평균 사이 거리는 크게, class 내부 scatter는 작게 만드는 방향.

### Fisher criterion
$$
J(w) = \frac{w^\top S_B\, w}{w^\top S_W\, w}
\qquad
\begin{cases}
S_B: \text{between-class scatter} \\
S_W: \text{within-class scatter}
\end{cases}
$$
최대화 → **generalized eigenvalue problem** $S_B w = \lambda S_W w$.

- **한계**: class별 분포가 Gaussian·unimodal이라는 가정. 구분 정보가 평균 차이가 아닌 분산 차이에 있으면 실패. nonlinear 경계엔 부적합.

## 5. 비교

| | PCA | LDA |
|---|---|---|
| 학습 종류 | Unsupervised | Supervised |
| label | 사용 안 함 | 사용 |
| 보존 목표 | 전체 variance | class separability |
| 한계 | 분류에 필요한 방향을 놓칠 수 있음 | 분포 가정·class 구조에 민감 |

## 복습 질문

- curse of dimensionality가 왜 문제인가?
- PCA에서 eigenvalue/eigenvector의 해석과, PCA가 label을 안 쓰는 한계는?
- Fisher criterion에서 $S_B$, $S_W$의 의미와 최적화가 어떤 문제로 이어지는가?
- PCA와 LDA를 목적·데이터 요구 관점에서 비교하라.
{% endraw %}

---

이전: [10. 군집화 (K-Means)](10-clustering-k-means.md) · 다음: [12. 신경망 (Neural Networks)](12-neural-networks.md)
