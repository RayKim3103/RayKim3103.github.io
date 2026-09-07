---
layout: page
title: "09. Autoregressive Models and VAE 2차 개정판"
permalink: /studies/ai/computer-vision/09r2-autoregressive-models-and-vae-revised-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/09R2%20Autoregressive%20Models%20and%20VAE%202%EC%B0%A8%20%EA%B0%9C%EC%A0%95%ED%8C%90.md)

{% raw %}
## 한눈에 보기
9강 2차 개정판은 autoregressive objective와 VAE/VQ-VAE/VQGAN의 생성 pipeline을 한 번 더 정돈한 자료이다. 시험 대비용으로 가장 정리된 버전으로 볼 수 있다.

## 핵심 개념
- ELBO
- Image Transformer
- VAE sampling
- VQ-VAE
- commitment loss
- VQGAN
- generation pipeline

## 체계적 정리
- autoregressive likelihood는 training에서 teacher forcing처럼 실제 이전 token을 조건으로 쓰고, generation에서는 model output을 순차적으로 사용한다.
- Image Transformer는 image를 token sequence로 바꾸되 2D local attention을 통해 계산량을 제한한다.
- VAE sampling은 latent Gaussian에서 sample을 뽑아 decoder로 image를 생성한다.
- VQ-VAE는 encoder output을 discrete index로 바꾸고, codebook prior를 따로 학습한다.
- VQGAN은 VQ representation에 GAN loss를 더해 perceptual quality를 높인다.

## 중요한 수식과 관점
- Negative log-likelihood minimization은 autoregressive cross-entropy 학습과 연결된다.
- KL divergence는 approximate posterior가 prior와 너무 멀어지지 않도록 한다.
- Commitment loss는 encoder output이 선택한 codebook vector에 머물도록 유도한다.

## 구현과 학습 포인트
이 노트는 생성 모델 파트를 최종 복습할 때 기준점으로 삼기 좋다. 특히 VAE의 continuous latent와 VQ-VAE의 discrete latent, GAN의 adversarial objective를 서로 비교해 두면 10강으로 자연스럽게 이어진다.

## 자주 헷갈리는 지점
- sampling 속도와 likelihood tractability는 generative model 계열별로 trade-off가 있다.
- VQGAN의 고품질 sample은 autoencoder만이 아니라 adversarial/perceptual loss와 prior model의 조합에서 나온다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Autoregressive Models and Variational Autoencoders](09-autoregressive-models-and-vae.md)
- [Autoregressive Models and VAE 개정판](09r-autoregressive-models-and-vae-revised.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

이전: [09. Autoregressive Models and VAE 개정판](09r-autoregressive-models-and-vae-revised.md) · 다음: [10. Generative Adversarial Networks](10-generative-adversarial-networks.md)
