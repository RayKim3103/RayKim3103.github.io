---
layout: page
title: "21. Lab10 사전보고서 Style Transfer"
permalink: /studies/ai/deep-learning-experiment/21-prep-lab10-style-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/21%20Lab10%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Style%20Transfer.md)

## 한눈에 보기
Neural Style Transfer와 perceptual loss 기반 image transformation의 배경을 정리한 사전보고서이다.

## 핵심 개념
- Gatys style transfer
- perceptual loss
- VGG feature
- Gram matrix
- image transform network
- total variation

## 이론 정리
- Gatys 방식은 output image 자체를 최적화하여 content feature와 style Gram matrix를 동시에 맞춘다.
- style은 특정 layer feature channel 간 상관관계인 Gram matrix로 표현된다.
- Johnson 방식은 transform network를 미리 학습하여 test time에는 한 번의 forward pass로 style transfer를 수행한다.
- feature reconstruction loss는 semantic content를, style reconstruction loss는 texture와 color statistics를 반영한다.
- total variation regularization은 output image의 spatial smoothness를 높인다.

## 중요한 수식과 관점
- Total loss: $\alpha L_{content}+\beta L_{style}+\gamma L_{tv}$.
- Gram matrix: $G_{ij}=\sum_k F_{ik}F_{jk}$.

## 실습과의 연결
Lab10에서는 VGG loss network를 고정하고 optimization 기반 방법과 feed-forward ImageTransformNet을 모두 구현해 속도와 품질 차이를 비교했다.

## 추가 해석 포인트
- content와 style은 엄밀한 물리량이 아니라 pretrained recognition network가 학습한 representation에서 정의된 개념이다.
- Gram matrix는 spatial 위치를 지우고 channel correlation을 남기므로 texture 통계에는 강하지만 구조 정보는 약하다.
- perceptual loss는 pixel-wise 정답이 하나로 정해지기 어려운 image generation 문제에서 blur를 줄이는 데 유리하다.
- Lab10의 alpha, beta 조정은 content 보존과 style 강도 사이의 직접적인 trade-off를 관찰하는 실험이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)
- [Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)
- [Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md)


---

이전: [20. Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md) · 다음: [22. Lab11 사전보고서 GAN과 DCGAN](22-prep-lab11-gan-dcgan.md)
