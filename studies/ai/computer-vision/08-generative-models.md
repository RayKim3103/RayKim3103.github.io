---
layout: page
title: "08. Generative Models"
permalink: /studies/ai/computer-vision/08-generative-models/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/08%20Generative%20Models.md)

{% raw %}
## 한눈에 보기
Generative Models 강의는 discriminative model과 generative model의 차이를 설명하고, deep generative model의 주요 계열을 큰 흐름으로 정리한다.

## 핵심 개념
- generative learning
- discriminative model
- Bayes rule
- likelihood
- prior
- VAE
- GAN
- diffusion
- foundation model

## 체계적 정리
- discriminative model은 주로 $P(Y|X)$ 또는 decision boundary를 학습한다.
- generative model은 data distribution 자체 또는 $P(X)$, $P(X|Y)$를 모델링해 새로운 sample을 생성할 수 있다.
- Bayes rule은 posterior, likelihood, prior의 관계를 설명하는 기본 틀이다.
- 전통적 generative model에는 Naive Bayes, LDA, Gaussian Mixture Model이 포함된다.
- deep generative model은 VAE, GAN, autoregressive model, diffusion model, VQ-VAE, VQ-GAN 등으로 발전했다.
- foundation model과 multimodal generation은 generative learning의 응용 범위를 크게 넓혔다.

## 중요한 수식과 관점
- Bayes rule: $P(Y|X)=P(X|Y)P(Y)/P(X)$.
- Maximum likelihood는 관측 data의 확률을 크게 만드는 parameter를 찾는 방식이다.
- Latent variable model은 관측되지 않는 $z$를 통해 복잡한 data distribution을 표현한다.

## 구현과 학습 포인트
이 노트는 9강과 10강의 준비 단계다. VAE는 latent variable과 variational inference, GAN은 adversarial objective, autoregressive model은 순차적 likelihood factorization을 중심으로 읽으면 좋다.

## 자주 헷갈리는 지점
- generative model이 반드시 label 없이만 학습하는 것은 아니다. conditional generation처럼 label 또는 text condition을 사용할 수 있다.
- sample quality와 likelihood는 항상 같은 방향으로 움직이지 않는다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Autoregressive Models and Variational Autoencoders](09-autoregressive-models-and-vae.md)
- [Generative Adversarial Networks](10-generative-adversarial-networks.md)
- [Transformers](07-transformers.md)

{% endraw %}

---

이전: [07. Transformers](07-transformers.md) · 다음: [09. Autoregressive Models and Variational Autoencoders](09-autoregressive-models-and-vae.md)
