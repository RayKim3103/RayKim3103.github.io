---
layout: page
title: "10. Generative Adversarial Networks"
permalink: /studies/ai/computer-vision/10-generative-adversarial-networks/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/10%20Generative%20Adversarial%20Networks.md)

{% raw %}
## 한눈에 보기
GAN 강의는 adversarial learning의 기본 framework에서 DCGAN, image-to-image translation, PGGAN/StyleGAN 계열까지 발전 흐름을 다룬다.

## 핵심 개념
- GAN
- DCGAN
- latent space
- Pix2Pix
- CycleGAN
- PGGAN
- StyleGAN
- style mixing
- adversarial loss

## 체계적 정리
- GAN은 Generator와 Discriminator가 서로 경쟁하는 구조다. Generator는 fake sample을 만들고, Discriminator는 real/fake를 구분한다.
- DCGAN은 convolutional architecture와 normalization을 사용해 이미지 생성 안정성을 높였다.
- latent space 조작은 생성 이미지의 attribute와 interpolation을 분석하는 도구다.
- Pix2Pix는 paired image-to-image translation을 cGAN과 reconstruction loss로 학습한다.
- CycleGAN은 unpaired domain translation을 adversarial loss와 cycle consistency loss로 학습한다.
- PGGAN은 낮은 해상도에서 시작해 점진적으로 고해상도 generation을 학습한다.
- StyleGAN은 style vector, adaptive normalization, stochastic variation을 통해 identity와 texture를 더 세밀하게 제어한다.

## 중요한 수식과 관점
- GAN minimax: $\min_G\max_D E_x\log D(x)+E_z\log(1-D(G(z)))$.
- Cycle consistency: $F(G(x))\approx x$, $G(F(y))\approx y$.
- Style mixing은 서로 다른 latent style을 layer별로 주입해 attribute를 분리한다.

## 구현과 학습 포인트
GAN을 읽을 때는 loss가 낮아지는지보다 generator와 discriminator의 균형, sample diversity, mode collapse 여부를 함께 봐야 한다. image-to-image translation에서는 paired/unpaired 여부가 objective를 결정한다.

## 자주 헷갈리는 지점
- GAN loss curve는 supervised learning loss처럼 단조로운 성능 지표가 아니다.
- Discriminator가 너무 강하거나 약하면 Generator가 유용한 gradient를 받지 못한다.
- StyleGAN의 style은 단순 색상 필터가 아니라 layer별 feature modulation이다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Generative Models](08-generative-models.md)
- [Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

이전: [09. Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md) · 다음: [11. Geometry 1](11-3d-geometry-1.md)
