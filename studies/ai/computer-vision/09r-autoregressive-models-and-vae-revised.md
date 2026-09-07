---
layout: page
title: "09. Autoregressive Models and VAE 개정판"
permalink: /studies/ai/computer-vision/09r-autoregressive-models-and-vae-revised/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/09R%20Autoregressive%20Models%20and%20VAE%20%EA%B0%9C%EC%A0%95%ED%8C%90.md)

{% raw %}
## 한눈에 보기
9강 개정판은 원본의 autoregressive/VAE 흐름을 유지하면서 VQ-VAE와 VQGAN의 단계적 생성 과정을 더 정리한 자료이다.

## 핵심 개념
- autoregressive sampling
- variational inference
- VAE implementation
- VQ-VAE
- codebook
- VQGAN
- two-stage generation

## 체계적 정리
- autoregressive model은 이전 pixel 또는 token을 조건으로 다음 값을 예측해 sample을 생성한다.
- VAE는 posterior를 직접 계산하기 어려워 variational distribution으로 근사한다.
- VAE implementation은 encoder가 평균과 분산을 만들고, reparameterization trick으로 sampling을 미분 가능하게 만든다.
- VQ-VAE는 encoder output을 가장 가까운 codebook vector로 양자화한다.
- VQGAN은 encoder-decoder와 adversarial/perceptual objective를 결합하고, 이후 transformer prior가 discrete code sequence를 모델링할 수 있다.

## 중요한 수식과 관점
- Reparameterization: $z=\mu+\sigma\odot\epsilon$, $\epsilon\sim N(0,I)$.
- ELBO는 log-likelihood의 tractable lower bound다.
- Discrete latent prior 학습은 token sequence modeling 문제로 바뀐다.

## 구현과 학습 포인트
개정판을 읽을 때는 원본 9강보다 구현 순서를 더 명확히 잡으면 좋다. encoder, latent sampling 또는 quantization, decoder, prior 학습을 단계별로 분리해서 정리한다.

## 자주 헷갈리는 지점
- VAE의 KL term이 너무 강하면 posterior collapse가 생길 수 있다.
- VQ-VAE codebook은 단순 clustering table이 아니라 학습되는 representation memory다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Autoregressive Models and Variational Autoencoders](09-autoregressive-models-and-vae.md)
- [Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md)
- [Generative Adversarial Networks](10-generative-adversarial-networks.md)

{% endraw %}

---

이전: [09. Autoregressive Models and Variational Autoencoders](09-autoregressive-models-and-vae.md) · 다음: [09. Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md)
