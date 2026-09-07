---
layout: page
title: "23. Lab12 사전보고서 Pix2Pix와 CycleGAN"
permalink: /studies/ai/deep-learning-experiment/23-prep-lab12-pix2pix-cyclegan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/23%20Lab12%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Pix2Pix%EC%99%80%20CycleGAN.md)

{% raw %}
## 한눈에 보기
paired image-to-image translation인 Pix2Pix와 unpaired translation인 CycleGAN을 비교한 사전보고서이다.

## 핵심 개념
- conditional GAN
- Pix2Pix
- paired data
- CycleGAN
- unpaired data
- cycle consistency
- PatchGAN

## 이론 정리
- Pix2Pix는 input image와 target image가 쌍으로 존재하므로 cGAN loss와 L1 reconstruction loss를 함께 사용한다.
- L1 loss는 L2보다 blur가 덜하고 구조 보존에 유리하다.
- CycleGAN은 paired target이 없기 때문에 adversarial loss만으로는 input content가 보존되지 않는다.
- cycle consistency loss는 X->Y->X, Y->X->Y 복원을 강제하여 content preservation을 제공한다.
- image buffer와 least-squares GAN loss 같은 안정화 기법은 oscillation을 줄이는 데 도움을 준다.

## 중요한 수식과 관점
- Pix2Pix objective: $L_{cGAN}(G,D)+\lambda L_1(G)$.
- Cycle loss: $E_x\|F(G(x))-x\|_1 + E_y\|G(F(y))-y\|_1$.

## 실습과의 연결
Lab12에서는 MNIST와 SVHN을 unpaired domain으로 두고 두 generator, 두 discriminator, GAN loss, cycle L1 loss, linear decay scheduler를 구현했다.

## 추가 해석 포인트
- Pix2Pix는 paired supervision 덕분에 mapping이 명확하지만, paired dataset 구축 비용이 크다.
- CycleGAN은 unpaired data만으로 학습 가능해 적용 범위가 넓지만, cycle consistency가 semantic correctness를 완벽히 보장하지는 않는다.
- PatchGAN discriminator는 전체 이미지 하나의 score보다 local texture realism을 강하게 평가한다.
- MNIST-SVHN 변환에서는 digit identity를 보존하면서 domain style만 바꾸는지가 핵심 qualitative criterion이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab12 CycleGAN](11-lab12-cyclegan.md)
- [Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md)
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)

{% endraw %}

---

이전: [22. Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md) · 다음: [24. Lab13 사전보고서 RNN과 Seq2Seq](24-prep-lab13-rnn-seq2seq.md)
