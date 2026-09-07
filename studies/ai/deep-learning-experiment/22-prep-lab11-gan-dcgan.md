---
layout: page
title: "22. Lab11 사전보고서 GAN과 DCGAN"
permalink: /studies/ai/deep-learning-experiment/22-prep-lab11-gan-dcgan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/22%20Lab11%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20GAN%EA%B3%BC%20DCGAN.md)

## 한눈에 보기
GAN의 minimax 학습 원리와 DCGAN의 안정적 convolutional architecture를 정리한 사전보고서이다.

## 핵심 개념
- GAN
- minimax game
- generator
- discriminator
- mode collapse
- DCGAN architecture
- batch normalization

## 이론 정리
- GAN은 명시적 likelihood 계산 없이 adversarial learning으로 생성 모델을 학습한다.
- 이론적으로 최적 discriminator 아래에서 generator는 data distribution에 가까워지는 방향으로 학습된다.
- 원래 generator objective는 saturation 문제가 있어 실제 구현에서는 $\log D(G(z))$를 키우는 non-saturating loss를 자주 쓴다.
- DCGAN은 strided convolution, transposed convolution, BatchNorm, ReLU/LeakyReLU 조합으로 GAN 학습 안정성을 높였다.

## 중요한 수식과 관점
- Minimax objective: $\min_G\max_D E_{x\sim p_{data}}[\log D(x)] + E_{z\sim p_z}[\log(1-D(G(z)))]$.
- Generator non-saturating objective: $\max_G E_z[\log D(G(z))]$.

## 실습과의 연결
Lab11에서는 BCE loss, alternating update, Generator/Discriminator convolution block, 생성 이미지 주기적 시각화를 구현했다.

## 추가 해석 포인트
- GAN은 생성 분포와 실제 분포의 거리를 직접 수식으로 최소화하기보다 discriminator가 제공하는 학습 신호를 이용한다.
- mode collapse는 generator가 다양한 sample 대신 discriminator를 속이기 쉬운 일부 mode만 생성하는 현상이다.
- DCGAN의 BatchNorm은 activation scale을 안정화하지만, Discriminator 마지막 layer와 Generator output layer에는 보통 넣지 않는다.
- GAN 실험에서는 loss curve만으로 품질을 판단하기 어렵기 때문에 생성 이미지 grid를 주기적으로 확인해야 한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab11 DCGAN](10-lab11-dcgan.md)
- [Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)


---

이전: [21. Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md) · 다음: [23. Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)
