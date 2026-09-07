---
layout: page
title: "10. Lab11 DCGAN"
permalink: /studies/ai/deep-learning-experiment/10-lab11-dcgan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/10%20Lab11%20DCGAN.md)

## 한눈에 보기
Deep Convolutional GAN(DCGAN)을 구현하여 latent noise에서 이미지를 생성하는 실습이다. Generator와 Discriminator를 번갈아 학습시키며 adversarial objective가 어떻게 균형을 이루는지 관찰한다.

## 핵심 개념
- GAN
- DCGAN
- generator
- discriminator
- binary cross entropy
- transposed convolution
- LeakyReLU
- adversarial training

## 실습 흐름
- 이미지를 64x64 RGB로 정규화하고 batch loader를 구성한다.
- Generator는 100-dimensional noise를 4x4 feature로 펼친 뒤 transposed convolution으로 64x64 이미지를 만든다.
- Discriminator는 convolution stack으로 이미지를 압축하여 real/fake probability를 출력한다.
- BCE loss로 real image는 1, generated image는 0 label에 맞춰 Discriminator를 학습한다.
- Generator는 Discriminator가 fake를 real로 판단하도록 BCE target을 1로 두고 학습한다.

## 구현과 이론의 연결
- DCGAN guideline에 따라 pooling 대신 strided convolution/up-convolution을 쓰고, 중간 layer에는 BatchNorm을 넣어 학습을 안정화한다.
- Generator 마지막은 Tanh를 사용해 [-1,1] 범위 이미지와 맞추고, Discriminator는 BCELoss와 맞추기 위해 Sigmoid를 사용한다.
- LeakyReLU는 음수 영역에서도 gradient를 남겨 Discriminator가 죽는 현상을 줄인다.

## 결과와 해석
- 5 epoch 동안 약 1,582 iteration/epoch로 학습 로그가 기록되었다. 초기 Loss_D 2.1402, Loss_G 2.0192에서 시작해 두 loss가 오르내리는 adversarial dynamics를 보였다.
- Generator loss가 급격히 커지는 구간은 Discriminator가 fake를 쉽게 구분하는 시점이고, Discriminator loss가 커지는 구간은 Generator가 상대적으로 성공한 시점으로 해석할 수 있다.
- 반복이 진행되며 생성 이미지는 random noise에서 점차 색과 형태를 가진 샘플로 변했다.

## 자주 헷갈리는 지점
- GAN loss는 일반 supervised loss처럼 단조 감소하지 않는다. 두 모델의 균형과 시각적 샘플을 함께 봐야 한다.
- Discriminator가 너무 강하면 Generator gradient가 약해지고, 너무 약하면 학습 신호가 무의미해진다.
- label shape과 output shape을 맞추지 않으면 BCE가 broadcasting되어 잘못된 loss를 만들 수 있다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md)
- [Lab12 CycleGAN](11-lab12-cyclegan.md)
- [Lab12 사전보고서 Pix2Pix와 CycleGAN](23-prep-lab12-pix2pix-cyclegan.md)


---

이전: [09. Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md) · 다음: [11. Lab12 CycleGAN](11-lab12-cyclegan.md)
