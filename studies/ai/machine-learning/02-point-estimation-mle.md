---
layout: page
title: "02. 점추정과 Maximum Likelihood Estimation"
permalink: /studies/ai/machine-learning/02-point-estimation-mle/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `02` 정리·보강
- 표준 통계/ML 교재 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**점추정**: 관측 데이터 $D$로부터 미지의 확률분포 파라미터 $\theta$의 한 값을 추정한다. **MLE**는 관측 데이터를 가장 그럴듯하게 만드는 $\theta$를 고른다. 이항(동전/thumbtack)과 Gaussian 예제로 유도한다.

## 1. Estimator

$$
D \;\longrightarrow\; \text{estimator} \;\longrightarrow\; \hat\theta(D)
$$
estimator는 데이터에서 파라미터 추정값을 계산하는 **규칙**(함수).

## 2. Probability vs Likelihood

| | 고정 | 변수 |
|---|---|---|
| **Probability** $P(D\mid\theta)$ | 파라미터 $\theta$ | 데이터 $D$ |
| **Likelihood** $\mathcal{L}(\theta)=P(D\mid\theta)$ | 데이터 $D$ | 파라미터 $\theta$ |

같은 수식이라도 무엇을 고정하고 무엇을 변수로 보느냐가 다르다.

## 3. 이항분포와 MLE

각 시행이 i.i.d., 성공확률 $\theta$. head가 $\alpha_H$번, tail이 $\alpha_T$번:
$$
P(D\mid\theta) = \theta^{\alpha_H}(1-\theta)^{\alpha_T}
$$

$$
\hat\theta_{\text{MLE}} = \arg\max_\theta P(D\mid\theta) = \arg\max_\theta \log P(D\mid\theta)
$$
(곱 → log로 바꿔 합으로.) log-likelihood를 $\theta$로 미분해 0으로 두면:
$$
\boxed{\;\hat\theta_{\text{MLE}} = \dfrac{\alpha_H}{\alpha_H + \alpha_T}\;}\quad(\text{성공 비율})
$$

**max vs argmax**: $\max f$ 는 함수값의 최댓값, $\arg\max f$ 는 그 값을 만드는 입력. 학습에서 필요한 건 보통 파라미터 자체이므로 argmax.

## 4. PAC 관점 (Hoeffding)

관측 수 $N$이 커질수록 경험적 비율 $\hat\theta$가 실제 $\theta$에 수렴. **Hoeffding inequality**는 추정 오차가 일정 이상 벗어날 확률을 bound:
$$
P\big(|\hat\theta - \theta| \ge \epsilon\big) \le 2e^{-2N\epsilon^2}
$$
**PAC(Probably Approximately Correct)**: "높은 확률로($1-\delta$), 근사적으로 정확한($\epsilon$) 추정" → 필요 sample 수 $N$은 $\epsilon,\delta$로 결정.

## 5. Gaussian의 MLE

$X\sim\mathcal{N}(\mu,\sigma^2)$, i.i.d. $x_1,\dots,x_N$:
$$
\hat\mu_{\text{MLE}} = \frac{1}{N}\sum_i x_i,\qquad
\hat\sigma^2_{\text{MLE}} = \frac{1}{N}\sum_i (x_i - \hat\mu)^2
$$

- $\hat\mu$는 **unbiased**.
- $\hat\sigma^2_{\text{MLE}}$는 $1/N$을 써서 **biased**($\mathbb{E}[\hat\sigma^2_{\text{MLE}}] = \frac{N-1}{N}\sigma^2$). unbiased sample variance는 $1/(N-1)$.

## 복습 질문

- probability와 likelihood의 차이를 같은 식으로 설명하라.
- 이항분포 MLE가 성공 비율이 되는 과정을 유도하라.
- Gaussian 평균·분산의 MLE를 쓰고, 분산 MLE가 biased인 이유는?
- PAC에서 필요 sample 수가 무엇에 의해 결정되는가?
{% endraw %}

---

이전: [01. 머신러닝 개요](01-introduction.md) · 다음: [03. 선형회귀](03-linear-regression.md)
