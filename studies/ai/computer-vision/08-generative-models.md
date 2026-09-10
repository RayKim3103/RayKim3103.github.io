---
layout: page
title: "08. Generative Models"
permalink: /studies/ai/computer-vision/08-generative-models/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `08` 보강
- 표준 자료(CS231n Generative Models · Deep Learning Book) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

생성 모델은 데이터 분포 $$p_{\text{data}}(x)$$를 근사해 **새 표본을 생성**한다. 이 노트는 discriminative와 generative의 차이, 그리고 주요 계열(autoregressive, VAE, GAN, diffusion)을 objective와 sampling 방식으로 비교한다.

## 1. Discriminative vs Generative

- **discriminative**: $$p(y\mid x)$$ 또는 결정경계만 학습. 분류/회귀에 직접적.
- **generative**: $$p(x)$$ 또는 $$p(x,y)$$ 학습. 표본 생성, 밀도 추정, 이상탐지, 결측 보완 가능.
- Bayes: $$p(y\mid x) = \dfrac{p(x\mid y)\,p(y)}{p(x)}$$ — generative는 $$p(x\mid y),p(y)$$를 모델링.

## 2. 주요 계열 비교

| 계열 | 학습 objective | sampling | likelihood | 특징 |
|---|---|---|---|---|
| **Autoregressive** | $$-\sum_i \log p(x_i \mid x_{<i})$$ (정확한 NLL) | 순차(픽셀/토큰 하나씩) → **느림** | tractable | 안정적 학습, 고품질, 느린 생성 |
| **VAE** | ELBO(하한) = 복원 − KL | latent Gaussian에서 한 번에 decode → 빠름 | 근사(하한) | 학습 안정, 표본이 다소 흐림 |
| **GAN** | minimax(adversarial) | 한 번에 generate → 빠름 | 없음(암시적) | 표본 선명, 학습 불안정·mode collapse |
| **Diffusion** | noise 예측(denoising score matching) | 반복적 denoising(수십~수백 step) → 느림 | 하한/score | 현재 최고 품질, 학습 안정, 샘플링 비용 큼 |

핵심 trade-off: **sampling 속도 ↔ likelihood tractability ↔ 표본 품질**. 한 축을 얻으면 다른 축을 내준다.

## 3. 최대가능도의 언어

대부분의 명시적 모델은 KL divergence 최소화 = 음의 로그가능도 최소화로 학습:
$$
\min_\theta \; \mathbb{E}_{x\sim p_{\text{data}}}\big[-\log p_\theta(x)\big]
= \min_\theta \; \mathrm{KL}\!\big(p_{\text{data}}\,\Vert\,p_\theta\big) + \text{const}
$$
GAN은 이 대신 분포 간 거리를 **판별자**로 암시적으로 추정한다(JS divergence 근사, WGAN은 Wasserstein).

## 4. Latent variable model

$$
p_\theta(x) = \int p_\theta(x\mid z)\,p(z)\,dz
$$
잠재변수 $$z$$(저차원, 보통 Gaussian prior)로 데이터의 변동요인을 요약. VAE는 이 적분이 불가능하므로 **variational inference**로 하한(ELBO)을 최적화한다 → [09. Autoregressive & VAE](09-autoregressive-and-vae.md).

## 5. Foundation model 관점

대규모 데이터로 사전학습한 생성/표현 모델(diffusion backbone, VLM 등)을 다양한 downstream에 적응. text-to-image는 텍스트 인코더 + 조건부 diffusion의 조합이 표준.

## 자주 틀리는 지점

- **학습 objective**와 **sampling 방식**은 별개로 비교해야 한다(표에서 두 열이 다른 이유).
- autoregressive는 likelihood가 정확하지만 생성이 순차라 느리다 — 품질이 좋다고 빠른 게 아니다.
- GAN 표본이 선명한 것은 adversarial loss 때문이지 likelihood가 좋아서가 아니다(오히려 likelihood 개념이 없음).
- VAE 표본이 흐린 주된 원인: 픽셀 단위 복원손실 + 사후분포 근사의 한계.

## 복습 질문

- discriminative와 generative 모델이 각각 무엇을 학습하며, generative만 할 수 있는 일은?
- 4개 계열을 objective / sampling / likelihood 세 축으로 비교하라.
- "최대가능도 = KL 최소화"를 유도하라. GAN은 왜 이 틀에서 벗어나는가?
- latent variable model에서 적분이 왜 문제이고 VAE는 어떻게 우회하는가?

{% endraw %}

---

이전: [07. Transformers](07-transformers.md) · 다음: [09. Autoregressive Models & VAE](09-autoregressive-and-vae.md)
