---
layout: page
title: "09. Autoregressive Models and Variational Autoencoders"
permalink: /studies/ai/computer-vision/09-autoregressive-models-and-vae/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/09%20Autoregressive%20Models%20and%20Variational%20Autoencoders.md)

{% raw %}
## 한눈에 보기
Autoregressive model과 VAE 계열을 설명하는 생성 모델 강의이다. data likelihood를 순차적으로 factorization하는 방식과 latent variable을 통해 data를 생성하는 방식을 비교한다.

## 핵심 개념
- autoregressive model
- PixelRNN
- Image Transformer
- latent variable model
- variational inference
- ELBO
- VAE
- VQ-VAE
- VQGAN

## 체계적 정리
- autoregressive model은 joint distribution을 조건부 확률들의 곱으로 분해한다.
- image generation에서는 pixel 또는 token을 정해진 순서로 하나씩 예측할 수 있다.
- Image Transformer는 2D local attention으로 이미지의 공간 구조와 transformer를 결합한다.
- VAE는 probabilistic encoder와 decoder를 사용해 latent variable distribution을 학습한다.
- ELBO는 reconstruction term과 KL regularization term으로 구성된다.
- VQ-VAE는 continuous latent 대신 codebook의 discrete latent를 사용한다.
- VQGAN은 perceptual/adversarial loss와 codebook representation을 결합해 고품질 생성을 목표로 한다.

## 중요한 수식과 관점
- Autoregressive factorization: $p(x)=\prod_i p(x_i|x_{<i})$.
- VAE objective: reconstruction loss + KL divergence regularization.
- VQ-VAE loss는 reconstruction, codebook, commitment term으로 구성된다.

## 구현과 학습 포인트
autoregressive model은 likelihood가 명확하지만 sampling이 순차적이라 느릴 수 있다. VAE는 latent space sampling이 빠르지만 blurry sample 문제가 생길 수 있고, VQ 계열은 discrete token representation으로 transformer prior와 연결된다.

## 자주 헷갈리는 지점
- autoregressive ordering은 모델이 볼 수 있는 context를 결정한다.
- VAE의 encoder output은 하나의 point가 아니라 보통 distribution parameter다.
- VQ-VAE의 stop-gradient 처리를 잘못 이해하면 codebook update와 encoder commitment의 역할이 섞인다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Autoregressive Models and VAE 개정판](09r-autoregressive-models-and-vae-revised.md)
- [Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md)
- [Generative Models](08-generative-models.md)

{% endraw %}

---

이전: [08. Generative Models](08-generative-models.md) · 다음: [09. Autoregressive Models and VAE 개정판](09r-autoregressive-models-and-vae-revised.md)
