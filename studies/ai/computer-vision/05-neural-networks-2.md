---
layout: page
title: "05. Neural Networks 2"
permalink: /studies/ai/computer-vision/05-neural-networks-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/05%20Neural%20Networks%202.md)

{% raw %}
## 한눈에 보기
Neural Networks 2는 CNN layer와 normalization, 대표 architecture인 VGG와 ResNet을 다룬다. image recognition에서 deep network를 안정적으로 깊게 만드는 장치들이 중심이다.

## 핵심 개념
- convolutional layer
- Batch Normalization
- Layer Normalization
- Instance Normalization
- VGG
- ResNet
- residual learning
- ImageNet architecture

## 체계적 정리
- convolutional layer는 local connectivity와 weight sharing으로 이미지의 공간 구조를 활용한다.
- BatchNorm은 batch statistics로 activation을 정규화하고 per-channel scale과 shift를 학습한다.
- LayerNorm은 sample 내부 feature dimension을 기준으로 정규화하며 sequence model에 자주 쓰인다.
- InstanceNorm은 image style transfer 등에서 instance별 channel statistics를 정규화한다.
- VGG는 작은 3x3 convolution을 깊게 쌓는 단순 구조로 성능을 높였다.
- ResNet은 residual connection으로 깊은 network의 optimization 문제를 줄였다.

## 중요한 수식과 관점
- Convolution parameter count: $K_hK_wC_{in}C_{out}+C_{out}$.
- BatchNorm: $\hat x=(x-\mu_B)/\sqrt{\sigma_B^2+\epsilon}$, $y=\gamma\hat x+\beta$.
- Residual block: $y=F(x)+x$.

## 구현과 학습 포인트
ResNet 과제 보고서와 직접 연결된다. BatchNorm의 train/eval mode 차이, residual shortcut의 channel matching, scheduler와 optimizer 선택은 실제 학습 성능을 크게 좌우한다.

## 자주 헷갈리는 지점
- BatchNorm은 train mode에서는 batch statistics, eval mode에서는 running statistics를 사용한다.
- LayerNorm과 BatchNorm은 정규화 축이 다르므로 작은 batch나 sequence model에서 동작 차이가 크다.
- ResNet shortcut에서 spatial size가 바뀌면 projection 또는 stride 처리가 필요하다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Neural Networks](04-neural-networks.md)
- [ResNet 보고서](27-resnet-report.md)
- [Detection and Segmentation](06-detection-and-segmentation.md)

{% endraw %}

---

이전: [04. Neural Networks](04-neural-networks.md) · 다음: [06. Detection and Segmentation](06-detection-and-segmentation.md)
