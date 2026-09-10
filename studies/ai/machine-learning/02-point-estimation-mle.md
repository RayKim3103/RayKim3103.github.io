---
layout: page
title: "02. 점추정과 Maximum Likelihood Estimation"
permalink: /studies/ai/machine-learning/02-point-estimation-mle/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `02` 정리·보강

{% raw %}
## 개요

**점추정**: 관측 데이터 $$D$$로부터 미지의 확률분포 파라미터 $$\theta$$의 한 값을 추정한다. **MLE**는 관측 데이터를 가장 그럴듯하게 만드는 $$\theta$$를 고른다. 이항(동전/thumbtack)과 Gaussian 예제로 유도한다.

## 1. Estimator

$$
D \;\longrightarrow\; \text{estimator} \;\longrightarrow\; \hat\theta(D)
$$
estimator는 데이터에서 파라미터 추정값을 계산하는 **규칙**(함수). 추정값 $$\hat\theta(D)$$는 데이터가 확률변수이므로 그 자체도 확률변수 → **bias**, **variance**, **consistency**로 품질을 논한다.
- **bias**: $$\mathbb{E}_D[\hat\theta] - \theta$$
- **variance**: $$\mathrm{Var}_D[\hat\theta]$$
- **MSE**: $$\mathbb{E}[(\hat\theta - \theta)^2] = \text{bias}^2 + \text{variance}$$
- **consistent**: $$N\to\infty$$ 일 때 $$\hat\theta \xrightarrow{P} \theta$$

## 2. Probability vs Likelihood

| | 고정 | 변수 |
|---|---|---|
| **Probability** $$P(D\mid\theta)$$ | 파라미터 $$\theta$$ | 데이터 $$D$$ |
| **Likelihood** $$\mathcal{L}(\theta)=P(D\mid\theta)$$ | 데이터 $$D$$ | 파라미터 $$\theta$$ |

같은 수식이라도 무엇을 고정하고 무엇을 변수로 보느냐가 다르다. likelihood는 $$\theta$$에 대해 **확률분포가 아니다**(적분해도 1이 안 됨).

## 3. 이항분포와 MLE — 전체 유도

각 시행이 i.i.d., 성공확률 $$\theta$$. head가 $$\alpha_H$$번, tail이 $$\alpha_T$$번:
$$
P(D\mid\theta) = \theta^{\alpha_H}(1-\theta)^{\alpha_T}
$$

곱 → **log**로 바꿔 합으로 (log는 단조증가라 argmax 보존):
$$
\ell(\theta) = \log P(D\mid\theta) = \alpha_H \log\theta + \alpha_T \log(1-\theta)
$$

$$\theta$$로 미분해 0:
$$
\frac{d\ell}{d\theta} = \frac{\alpha_H}{\theta} - \frac{\alpha_T}{1-\theta} = 0
\;\Longrightarrow\;
\alpha_H(1-\theta) = \alpha_T\,\theta
\;\Longrightarrow\;
\boxed{\;\hat\theta_{\text{MLE}} = \dfrac{\alpha_H}{\alpha_H + \alpha_T}\;}
$$
(2계 도함수 $$-\alpha_H/\theta^2 - \alpha_T/(1-\theta)^2 < 0$$ → 최대.)

**max vs argmax**: $$\max f$$ 는 함수값의 최댓값, $$\arg\max f$$ 는 그 값을 만드는 입력. 학습에서 필요한 건 보통 파라미터 자체이므로 argmax.

### MAP와의 대비 (참고)
$$\hat\theta_{\text{MAP}} = \arg\max_\theta P(\theta\mid D) = \arg\max_\theta \big[\log P(D\mid\theta) + \log P(\theta)\big]$$.
이항 + **Beta$$(\beta_H, \beta_T)$$ prior** → $$\hat\theta_{\text{MAP}} = \dfrac{\alpha_H + \beta_H - 1}{\alpha_H + \alpha_T + \beta_H + \beta_T - 2}$$ (pseudo-count). 데이터가 적을 때 극단값을 방지.

## 4. PAC 관점 (Hoeffding)

관측 수 $$N$$이 커질수록 경험적 비율 $$\hat\theta$$가 실제 $$\theta$$에 수렴 (대수의 법칙). **Hoeffding inequality**는 유한 $$N$$에서의 편차를 bound:
$$
P\big(|\hat\theta - \theta| \ge \epsilon\big) \le 2e^{-2N\epsilon^2}
$$
우변을 $$\delta$$로 두고 $$N$$에 대해 풀면:
$$
N \ge \frac{1}{2\epsilon^2}\ln\frac{2}{\delta}
$$
**PAC(Probably Approximately Correct)**: "확률 $$\ge 1-\delta$$로, 오차 $$\le \epsilon$$인 추정". 필요 sample 수는 $$\epsilon$$(정확도)과 $$\delta$$(신뢰도)로 결정되며, $$1/\epsilon^2$$·$$\ln(1/\delta)$$ 스케일.

## 5. Gaussian의 MLE — 유도

$$X\sim\mathcal{N}(\mu,\sigma^2)$$, i.i.d. $$x_1,\dots,x_N$$. log-likelihood:
$$
\ell(\mu,\sigma^2) = -\frac{N}{2}\log(2\pi\sigma^2) - \frac{1}{2\sigma^2}\sum_i (x_i - \mu)^2
$$

$$\partial\ell/\partial\mu = \dfrac{1}{\sigma^2}\sum_i(x_i - \mu) = 0 \Rightarrow \hat\mu = \dfrac{1}{N}\sum_i x_i$$.

$$\partial\ell/\partial\sigma^2 = -\dfrac{N}{2\sigma^2} + \dfrac{1}{2\sigma^4}\sum_i(x_i-\hat\mu)^2 = 0 \Rightarrow \hat\sigma^2 = \dfrac{1}{N}\sum_i(x_i - \hat\mu)^2$$.

$$
\boxed{\;\hat\mu_{\text{MLE}} = \frac{1}{N}\sum_i x_i,\qquad
\hat\sigma^2_{\text{MLE}} = \frac{1}{N}\sum_i (x_i - \hat\mu)^2\;}
$$

### 분산 MLE가 biased인 이유

$$\sum_i(x_i - \hat\mu)^2 = \sum_i(x_i-\mu)^2 - N(\hat\mu - \mu)^2$$ 이고, $$\mathbb{E}[(x_i-\mu)^2] = \sigma^2$$, $$\mathbb{E}[(\hat\mu-\mu)^2] = \sigma^2/N$$ 이므로:
$$
\mathbb{E}\!\left[\sum_i(x_i-\hat\mu)^2\right] = N\sigma^2 - \sigma^2 = (N-1)\sigma^2
\;\Longrightarrow\;
\mathbb{E}[\hat\sigma^2_{\text{MLE}}] = \frac{N-1}{N}\sigma^2 < \sigma^2
$$
$$\hat\mu$$를 데이터에서 추정하며 자유도 1을 소모하기 때문. **unbiased sample variance**는 $$s^2 = \dfrac{1}{N-1}\sum_i(x_i-\hat\mu)^2$$.

## 복습 질문

- probability와 likelihood의 차이를 같은 식으로 설명하고, likelihood가 확률분포가 아닌 이유는?
- 이항분포 MLE $$\hat\theta = \alpha_H/(\alpha_H+\alpha_T)$$를 log-likelihood 미분으로 유도하라.
- Gaussian $$\hat\mu$$, $$\hat\sigma^2$$의 MLE를 유도하고, $$\mathbb{E}[\hat\sigma^2_{\text{MLE}}] = \frac{N-1}{N}\sigma^2$$임을 보여라.
- PAC에서 $$N \ge \frac{1}{2\epsilon^2}\ln\frac{2}{\delta}$$가 나오는 과정과, $$\epsilon$$·$$\delta$$ 의존성은?
- MLE와 MAP의 차이, Beta prior가 이항 MLE를 어떻게 바꾸는가?
{% endraw %}

---

이전: [01. 머신러닝 개요](01-introduction.md) · 다음: [03. 선형회귀](03-linear-regression.md)
