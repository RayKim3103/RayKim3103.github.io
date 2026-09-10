---
layout: page
title: "10. Lab12 — Pix2Pix & CycleGAN"
permalink: /studies/ai/deep-learning-experiment/10-lab12-pix2pix-cyclegan/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(23) + 본보고서(11) 통합·보강
- 이론 배경은 Pix2Pix / CycleGAN 논문 수준으로 보강. **실험 설정·로그 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

image-to-image translation 두 방식을 비교한다.
- **Pix2Pix**: 입력–정답이 **쌍(paired)**으로 존재 → cGAN loss + L1.
- **CycleGAN**: 쌍이 **없는(unpaired)** 두 도메인 사이 변환 → **cycle consistency**로 content 보존. 구현은 **MNIST ↔ SVHN**.

## 1. 이론 배경

### Pix2Pix (paired)
$$
L = L_{\text{cGAN}}(G,D) + \lambda\, L_1(G)
$$
- **L1 loss**: L2보다 blur가 덜하고 구조 보존에 유리.
- **PatchGAN discriminator**: 전체 이미지 1개 score가 아니라 **local patch별 texture realism** 평가.
- paired supervision → mapping이 명확하지만 paired dataset 구축 비용이 큼.

### CycleGAN (unpaired)
$$
L_{\text{cyc}} = \mathbb{E}_x\lVert F(G(x)) - x\rVert_1 + \mathbb{E}_y\lVert G(F(y)) - y\rVert_1
$$
- $$G:X\to Y$$, $$F:Y\to X$$에 대해 $$F(G(x))\approx x$$, $$G(F(y))\approx y$$ 강제.
- paired target이 없으면 adversarial loss만으로는 **입력 content가 보존되지 않고** target 도메인 이미지로 collapse 가능 → cycle loss가 **구조 보존 장치**.
- image buffer, least-squares GAN loss 등이 oscillation 완화에 도움. cycle consistency가 semantic correctness를 **완벽히 보장하진 않음**.

## 2. 실습 설계 (CycleGAN, MNIST↔SVHN)

- MNIST = 1-ch 32×32, SVHN = 3-ch 32×32로 맞춰 loader 구성.
- `Generator_X`: MNIST → SVHN-like, `Generator_Y`: SVHN → MNIST-like.
- `Discriminator_X`, `Discriminator_Y`: 각 도메인 real/fake **patch** probability.
- **GAN loss**(target realism) + **cycle L1 loss**(복원 가능성).
- learning rate: 초반 0.0002 유지 → decay point 이후 전체 iteration에 비례해 **선형 감소 → 0**.

## 3. 결과 (학습 로그)

- **20 epoch**, 총 **18,740 iteration**.
- 초반 `Loss_D ≈ 1.09`, `Loss_G ≈ 1.84`, `cycle loss ≈ 0.24`.
- iteration 진행에 따라 GAN loss ↔ cycle loss가 **trade-off**.
- 생성 품질은 loss 숫자보다 **변환 이미지의 domain realism + digit identity 보존**을 함께 확인.
- 결론: CycleGAN은 paired data 없이 학습 가능하지만, 정답쌍을 직접 쓰는 Pix2Pix보다 **mapping 제약이 약하다**.

## 4. 자주 틀리는 지점

- 매 iteration `next(iter(loader))`로 Y batch를 새로 뽑으면 sampling 비효율 → iterator 관리 분리.
- MNIST(1-ch) ↔ SVHN(3-ch) — generator/discriminator 입출력 채널을 명확히 구분.
- cycle loss weight ↑ → reconstruction 우세(스타일 약함), ↓ → 내용 보존 약함.
- MNIST-SVHN에서 핵심 정성 기준은 **digit identity를 보존하면서 domain style만 바꾸는가**.

## 복습 질문

- Pix2Pix와 CycleGAN의 데이터 요건 차이와, 각자 사용하는 loss는?
- cycle consistency loss가 필요한 이유(adversarial loss만으로는 안 되는 이유)는?
- PatchGAN discriminator가 일반 discriminator와 다른 점은?
- CycleGAN에서 cycle loss weight를 키우거나 줄이면 어떻게 되는가?
{% endraw %}

---

이전: [09. Lab11 — DCGAN](09-lab11-dcgan.md) · 다음: [11. Lab13 — RNN·Seq2Seq·Attention](11-lab13-rnn-seq2seq-attention.md)
