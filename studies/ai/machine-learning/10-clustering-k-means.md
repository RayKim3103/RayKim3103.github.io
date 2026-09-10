---
layout: page
title: "10. 군집화 (Clustering · K-Means)"
permalink: /studies/ai/machine-learning/10-clustering-k-means/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `15` 정리·보강

{% raw %}
## 개요

label 없는 데이터에서 유사한 sample끼리 그룹을 찾는 **비지도학습**. 대표 알고리즘 **k-means**는 assignment ↔ center update를 번갈아 하는 **coordinate descent**로, 목적함수를 단조 감소시키며 수렴한다.

## 1. Cluster의 정의와 한계

"같은 cluster 안은 가깝고, 다른 cluster와는 멀다" — 중심 + 퍼짐으로 기술. 하지만 비구형(초승달)·얽힌·밀도차·크기차 cluster에서는 k-means 가정이 깨진다.

## 2. K-Means Algorithm

cluster 수 $$k$$를 미리 정하고:

1. center $$\mu_1,\dots,\mu_k$$ 초기화
2. **Assignment step**: 각 point를 가장 가까운 center에 할당 $$\;z_i = \arg\min_j \lVert x_i - \mu_j\rVert^2$$
3. **Update step**: 각 cluster에 속한 point의 평균으로 center 갱신 $$\;\mu_j = \frac{1}{|C_j|}\sum_{i \in C_j} x_i$$
4. assignment가 안 바뀌거나 $$J$$ 감소가 작아질 때까지 반복

### Objective (within-cluster sum of squares)
$$
J(\{z_i\}, \{\mu_j\}) = \sum_{i=1}^{N} \lVert x_i - \mu_{z_i}\rVert^2
= \sum_{j=1}^{K}\sum_{i \in C_j}\lVert x_i - \mu_j\rVert^2
$$

### 수렴 증명 (단조 감소)

두 step 모두 $$J$$를 **증가시키지 않는다**:
- **Assignment**: $$\mu_j$$ 고정. 각 $$x_i$$를 최근접 center에 배정하는 것이 $$\lVert x_i - \mu_{z_i}\rVert^2$$을 최소화 → $$J\downarrow$$.
- **Update**: $$z_i$$ 고정. $$\sum_{i\in C_j}\lVert x_i - \mu_j\rVert^2$$을 $$\mu_j$$로 미분하면 $$-2\sum_{i\in C_j}(x_i - \mu_j) = 0 \Rightarrow \mu_j = $$ 평균. 즉 **cluster 평균이 SSE를 최소화** → $$J\downarrow$$.

$$J \ge 0$$이고 가능한 assignment가 유한($$k^N$$)하므로 → **유한 스텝에 수렴**. 단 **local optimum** (전역 최적 k-means는 NP-hard) → **초기값에 민감**.

| step | 고정 | 최적화 |
|---|---|---|
| Assignment | centers | 각 point의 cluster |
| Update | assignments | 각 center |

## 3. Initialization

나쁜 초기 center → 나쁜 clustering.
- **여러 번 재시작**(다른 random seed)해서 $$J$$ 최소인 결과 선택.
- **k-means++**: 첫 center 무작위, 이후 center는 기존 center들과의 최소거리 제곱 $$D(x)^2$$에 비례하는 확률로 선택 → center들이 퍼지게 배치. 기대 $$J$$가 최적의 $$O(\log k)$$배 이내로 보장.

## 4. k 선택

$$k$$↑ → $$J$$는 **단조 감소**($$k = N$$이면 $$J = 0$$). 따라서 $$J$$만으로 고르면 과도하게 큰 $$k$$. 대안:
- **Elbow method**: $$J$$ vs $$k$$ 곡선에서 꺾이는 지점
- **Silhouette score**: 각 점의 (다른 cluster까지 평균거리 − 자기 cluster 평균거리) 정규화, $$[-1, 1]$$
- **Gap statistic**, downstream 성능, domain knowledge

## 5. 변형·관계

- **k-medoids (PAM)**: center를 실제 데이터 점으로 → outlier에 강함, 임의 거리 사용 가능.
- **GMM + EM**: k-means는 **등방·동일분산 Gaussian, hard assignment**인 GMM의 특수·극한 case. EM은 soft assignment(responsibility) → 타원형 cluster·불확실성 표현.
- **표준화 필수**: 거리 기반이라 feature scale에 민감.

## 6. 한계

- cluster가 대략 **구형·비슷한 크기·비슷한 밀도**라는 암묵 가정
- outlier에 민감 (평균이 끌려감)
- $$k$$를 미리 지정
- 비구형 구조엔 spectral clustering / DBSCAN / 계층적 군집이 나음

## 복습 질문

- supervised vs unsupervised learning의 차이, clustering의 대표 용도는?
- k-means의 두 step이 각각 $$J$$를 감소시킴을 보이고(특히 "평균이 SSE 최소"), 유한 수렴하지만 local optimum인 이유는?
- k-means++의 $$D(x)^2$$ 비례 선택이 무엇을 개선하는가?
- $$k$$가 커지면 $$J$$가 단조 감소하는데도 $$k$$를 고를 수 있는 방법(elbow, silhouette)은?
- k-means와 GMM/EM의 관계, k-means가 잘 안 맞는 데이터 형태는?
{% endraw %}

---

이전: [09. Instance-Based Learning](09-instance-based-learning.md) · 다음: [11. 차원 축소 (PCA · LDA)](11-dimension-reduction-pca-lda.md)
