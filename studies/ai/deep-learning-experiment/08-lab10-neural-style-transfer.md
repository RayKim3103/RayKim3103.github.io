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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Lab10 Neural Style Transfer**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 생성모델은 likelihood, adversarial objective, reconstruction/perceptual loss가 각각 다른 품질 기준을 최적화한다.
- 샘플 이미지는 mode collapse, blur, texture artifact, conditioning failure를 따로 보며 해석한다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.

### 문제 풀이 또는 구현 루틴

- 입력/출력 shape 표를 먼저 만든 뒤 forward pass를 작은 batch로 검증한다.
- 학습이 불안정하면 loss scale, gradient norm, learning rate, normalization, label 형식을 순서대로 확인한다.
- 보고서에는 baseline, ablation, failure case, 재현 가능한 hyperparameter를 함께 남긴다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 논문 그림만 보고 구현하면 padding, stride, channel order 같은 세부에서 쉽게 틀린다.
- validation 성능이 좋아도 data leakage가 있으면 의미가 없다.
- loss가 여러 개인 실험은 각 항의 scale을 따로 확인해야 한다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 모델의 inductive bias는 어떤 데이터 특성에 맞춰져 있는가?
- 성능 향상이 architecture 때문인지 training recipe 때문인지 어떻게 분리할 수 있는가?
- 결과 이미지만 보지 않고 어떤 수치와 failure case를 함께 확인해야 하는가?
- **08. Lab10 Neural Style Transfer**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Lab09 Quantization Interval Learning](07-lab09-quantization-interval-learning.md) · 다음: [09. Lab10-2 Neural Style Transfer 반복 실험](09-lab10-2-neural-style-transfer.md)
