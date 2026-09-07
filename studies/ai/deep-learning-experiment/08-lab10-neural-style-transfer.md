---
layout: page
title: "08. Lab10 Neural Style Transfer"
permalink: /studies/ai/deep-learning-experiment/08-lab10-neural-style-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/08%20Lab10%20Neural%20Style%20Transfer.md)

{% raw %}
## 한눈에 보기
Gatys의 optimization 기반 neural style transfer와 Johnson의 perceptual loss 기반 feed-forward transform network를 구현하는 실습이다. pixel loss 대신 pretrained VGG feature 공간에서 content와 style을 정의한다.

## 핵심 개념
- neural style transfer
- VGG19 loss network
- content loss
- style loss
- Gram matrix
- total variation loss
- image transform network

## 실습 흐름
- content image와 style image를 같은 크기/정규화 체계로 불러온다.
- VGG19 feature extractor를 고정된 loss network로 사용하고 content layer와 style layer를 지정한다.
- style 표현은 feature map의 Gram matrix로 계산하여 texture와 channel correlation을 잡는다.
- optimization 기반 방식은 출력 이미지를 직접 변수로 두고 LBFGS/Adam으로 content+style loss를 줄인다.
- feed-forward 방식은 ImageTransformNet을 학습해 한 번의 forward pass로 stylized image를 생성한다.

## 구현과 이론의 연결
- content loss는 특정 VGG layer feature의 L2 차이로 정의되고, style loss는 여러 layer의 Gram matrix 차이를 합산한다.
- ImageTransformNet은 9x9 initial/final conv, stride-2 downsampling, residual block 5개, upsampling conv로 구성된다.
- total variation loss는 인접 pixel 차이를 줄여 output의 고주파 잡음을 완화한다.

## 결과와 해석
- optimization 기반 방식은 품질은 좋지만 이미지 한 장마다 반복 최적화가 필요하다.
- feed-forward transform network는 학습 후 inference가 빠르며, 실시간 style transfer에 적합하다.
- alpha를 낮추면 content 보존이 약해지고 style texture가 강해진다. beta를 높이면 style pattern이 강해지지만 구조 왜곡과 색 번짐이 커질 수 있다.

## 자주 헷갈리는 지점
- VGG input normalization을 content/style/generated image에 동일하게 적용해야 loss scale이 의미 있다.
- Gram matrix는 spatial size에 따라 scale이 달라질 수 있으므로 normalization을 포함해야 layer별 loss 균형이 맞는다.
- style/content layer 선택이 결과에 큰 영향을 준다. shallow layer는 texture와 edge, deep layer는 semantic layout을 더 많이 반영한다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)
- [Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)
- [Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)

{% endraw %}

---

이전: [07. Lab09 Quantization Interval Learning](07-lab09-quantization-interval-learning.md) · 다음: [09. Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)
