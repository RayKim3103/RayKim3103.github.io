---
layout: page
title: "99. 기말고사 리뷰"
permalink: /studies/ai/computer-vision/99-final-exam-review/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/99%20%EA%B8%B0%EB%A7%90%EA%B3%A0%EC%82%AC%20%EB%A6%AC%EB%B7%B0.md)

{% raw %}
## 한눈에 보기
기말고사 리뷰 자료는 후반부 핵심 주제를 시험 대비 관점으로 압축한 노트이다. transformer, generative model, GAN, 3D computer vision의 연결 관계를 빠르게 점검한다.

## 핵심 개념
- transformers
- generative models
- GAN
- 3D geometry
- camera calibration
- two-view geometry
- exam review

## 체계적 정리
- Transformer 파트는 input embedding, positional encoding, encoder/decoder 구조, scaled dot-product attention, multi-head attention, temperature를 중심으로 정리된다.
- CNN과 transformer의 차이는 local connectivity와 translation invariance라는 inductive bias의 유무에서 비교된다.
- generative model 파트는 autoregressive model, VAE, GAN의 objective와 sampling 방식을 비교한다.
- GAN 파트는 DCGAN, Pix2Pix, CycleGAN, PGGAN/StyleGAN의 핵심 차이를 묻기 좋다.
- 3D vision 파트는 projection, homogeneous coordinate, calibration, epipolar geometry, fundamental/essential matrix, triangulation이 연결된다.

## 중요한 수식과 관점
- Attention: $softmax(QK^T/\sqrt{d_k})V$.
- Projection: $s x = K[R|t]X$.
- Epipolar constraint: $x'^T F x=0$.
- VAE는 reconstruction과 regularization, GAN은 adversarial objective를 핵심으로 한다.

## 구현과 학습 포인트
시험 전에는 이 노트를 목차처럼 사용하고, 막히는 항목을 해당 강의 노트로 들어가 복습하면 된다. 특히 후반부는 수식 자체보다 어떤 matrix가 무엇을 mapping하는지 말로 설명할 수 있어야 한다.

## 자주 헷갈리는 지점
- F와 E의 차이, homography와 fundamental matrix의 차이를 분명히 해야 한다.
- Transformer attention의 Q/K/V 역할을 행렬 크기와 함께 설명할 수 있어야 한다.
- generative model의 학습 objective와 sampling 방식은 별도로 비교해야 한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Transformers](07-transformers.md)
- [Autoregressive Models and VAE 2차 개정판](09r2-autoregressive-models-and-vae-revised-2.md)
- [Two-view Geometry 2](13-2-two-view-geometry-2.md)

{% endraw %}

---

이전: [27. ResNet 보고서](27-resnet-report.md)
