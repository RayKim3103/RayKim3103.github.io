---
layout: page
title: "10. Generative Adversarial Networks"
permalink: /studies/ai/computer-vision/10-generative-adversarial-networks/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `10` 보강
- 표준 자료(GAN·DCGAN·pix2pix·CycleGAN·StyleGAN 논문) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

GAN은 두 네트워크의 게임으로 학습한다. **generator** $$G$$는 노이즈 $$z$$에서 가짜 이미지를 만들고, **discriminator** $$D$$는 진짜/가짜를 구분한다. $$G$$는 $$D$$를 속이도록, $$D$$는 안 속도록 번갈아 학습.

## 1. Adversarial objective

$$
\min_G \max_D \;\;
\mathbb{E}_{x\sim p_{\text{data}}}[\log D(x)]
+ \mathbb{E}_{z\sim p_z}[\log(1 - D(G(z)))]
$$

- 최적 $$D^*(x) = \dfrac{p_{\text{data}}(x)}{p_{\text{data}}(x)+p_g(x)}$$ → 이때 $$G$$ 목적은 $$\mathrm{JS}(p_{\text{data}}\Vert p_g)$$ 최소화.
- 실무: $$G$$는 $$\log(1-D(G(z)))$$ 대신 $$-\log D(G(z))$$를 최대화(초기 gradient가 강함, "non-saturating loss").
- **명시적 likelihood가 없다** — 표본 품질은 좋지만 밀도 평가는 불가.

## 2. 학습이 어려운 이유

- **불안정**: 두 손실이 동시에 안 내려감. $$D$$가 너무 강하면 $$G$$ gradient 소실, $$G$$가 너무 강하면 $$D$$가 무너짐.
- **mode collapse**: $$G$$가 몇 개 표본만 반복 생성(다양성 상실).
- 완화책: 균형 잡힌 학습률/스텝, spectral norm, gradient penalty, **WGAN**(Wasserstein 거리 + Lipschitz 제약), 다양한 정규화, EMA of $$G$$ weights.

## 3. 발전 흐름

| 모델 | 아이디어 |
|---|---|
| **DCGAN** | $$G$$/$$D$$를 conv/transposed-conv로, BatchNorm, 안정적 학습 레시피. latent 공간 산술(벡터 연산)이 의미를 가짐 |
| **Pix2Pix** | **쌍 데이터** image-to-image 변환. conditional GAN + L1 loss + PatchGAN discriminator |
| **CycleGAN** | **비쌍(unpaired)** 변환. cycle-consistency $$F(G(x))\approx x$$ 로 대응 없이 도메인 변환(말↔얼룩말) |
| **PGGAN** | 저해상도부터 점진적으로 층을 추가(progressive growing) → 고해상도 안정화 |
| **StyleGAN** | latent를 style로 각 해상도에 주입(AdaIN), noise 입력으로 stochastic detail, **style mixing**으로 속성 분리·제어 |

## 4. VAE / diffusion과의 대비

- VAE: 안정적 학습, likelihood 하한, 흐린 표본.
- GAN: 불안정, likelihood 없음, 선명한 표본.
- diffusion: 안정적, 반복 샘플링(느림), 현재 최고 품질 — GAN을 상당 부분 대체.

## 자주 틀리는 지점

- $$G$$의 "non-saturating" 손실($$-\log D(G(z))$$)과 원 minimax의 $$G$$ 항을 헷갈리지 말 것 — gradient 거동이 다르다.
- Pix2Pix는 **쌍** 데이터, CycleGAN은 **비쌍** — cycle-consistency가 대응 부재를 메운다.
- mode collapse는 loss가 낮아 보여도 발생한다(다양성 지표·FID를 따로 확인).
- GAN 품질↑는 adversarial loss 덕분이며 likelihood와 무관.

## 복습 질문

- GAN의 value function을 쓰고 최적 $$D$$와 그때 $$G$$가 최소화하는 대상을 유도하라.
- non-saturating generator loss를 쓰는 이유는?
- Pix2Pix와 CycleGAN의 데이터 요건과 핵심 loss 차이는?
- StyleGAN이 속성을 분리·제어하는 메커니즘(AdaIN, style mixing)은?

{% endraw %}

---

이전: [09. Autoregressive Models & VAE](09-autoregressive-and-vae.md) · 다음: [11. 3D Geometry](11-3d-geometry.md)
