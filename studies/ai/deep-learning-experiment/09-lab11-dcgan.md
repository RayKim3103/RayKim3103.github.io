---
layout: page
title: "09. Lab11 — DCGAN"
permalink: /studies/ai/deep-learning-experiment/09-lab11-dcgan/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(22) + 본보고서(10) 통합·보강
- 이론 배경은 GAN / DCGAN 논문 수준으로 보강. **실험 설정·로그 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

**Deep Convolutional GAN**을 구현해 latent noise에서 이미지를 생성한다. Generator $$G$$와 Discriminator $$D$$를 번갈아 학습시키며 adversarial objective가 어떻게 균형을 이루는지 관찰.

## 1. 이론 배경

### Minimax objective
$$
\min_G \max_D \; \mathbb{E}_{x\sim p_{\text{data}}}[\log D(x)]
+ \mathbb{E}_{z\sim p_z}[\log(1 - D(G(z)))]
$$
- 이론상 최적 $$D$$ 아래에서 $$G$$는 $$p_{\text{data}}$$에 가까워지는 방향으로 학습.
- 원래 $$G$$ objective는 초기 saturation 문제 → 실제로는 **non-saturating** $$\max_G \mathbb{E}_z[\log D(G(z))]$$ 사용.
- **명시적 likelihood 계산 없이** discriminator가 주는 학습 신호를 이용.
- **mode collapse**: $$G$$가 다양한 sample 대신 $$D$$를 속이기 쉬운 일부 mode만 생성.

### DCGAN 레시피
- pooling 대신 **strided conv / transposed conv**.
- 중간 layer에 **BatchNorm**(단, $$D$$ 마지막·$$G$$ 출력 layer에는 넣지 않음).
- $$G$$ 마지막 **Tanh**([-1,1] 이미지), $$D$$ 마지막 **Sigmoid**(BCELoss).
- **LeakyReLU**: 음수 영역에서도 gradient를 남겨 $$D$$가 죽는 현상 완화.

## 2. 실습 설계

- 이미지 **64×64 RGB** 정규화, batch loader.
- **Generator**: 100-dim noise → 4×4 feature로 펼침 → transposed conv stack → 64×64.
- **Discriminator**: conv stack으로 압축 → real/fake probability.
- **BCE loss**: real→1, fake→0으로 $$D$$ 학습. $$G$$는 fake를 real(1)로 판단하도록 학습.
- 생성 이미지를 주기적으로 시각화.

## 3. 결과 (학습 로그)

- **5 epoch**, 약 **1,582 iteration/epoch**.
- 초기 `Loss_D ≈ 2.1402`, `Loss_G ≈ 2.0192` → 이후 두 loss가 **오르내리는 adversarial dynamics**.
  - Generator loss 급증 구간 = $$D$$가 fake를 쉽게 구분하는 시점.
  - Discriminator loss 급증 구간 = $$G$$가 상대적으로 성공한 시점.
- 반복이 진행되며 생성 이미지가 random noise → 색·형태를 가진 샘플로 변화.

## 4. 자주 틀리는 지점

- GAN loss는 supervised loss처럼 **단조 감소하지 않는다** → 두 모델의 균형 + 시각적 샘플 grid를 함께 봐야.
- $$D$$가 너무 강하면 $$G$$ gradient 약화, 너무 약하면 학습 신호 무의미.
- label shape ↔ output shape 불일치 시 BCE가 broadcasting되어 잘못된 loss.
- BatchNorm은 $$G$$ 출력·$$D$$ 마지막 layer에는 넣지 않는다.

## 복습 질문

- GAN의 minimax objective와, 실제 구현에서 non-saturating $$G$$ loss를 쓰는 이유는?
- mode collapse란 무엇인가?
- DCGAN이 학습 안정성을 위해 채택한 구조적 선택들(strided conv, BN 위치, Tanh/Sigmoid, LeakyReLU)은?
- GAN에서 loss curve만으로 품질을 판단하기 어려운 이유는?
{% endraw %}

---

이전: [08. Lab10 — Neural Style Transfer](08-lab10-neural-style-transfer.md) · 다음: [10. Lab12 — Pix2Pix & CycleGAN](10-lab12-pix2pix-cyclegan.md)
