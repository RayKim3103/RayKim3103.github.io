---
layout: page
title: "14. Lab03 사전보고서 CNN 구조"
permalink: /studies/ai/deep-learning-experiment/14-prep-lab03-cnn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/14%20Lab03%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20CNN%20%EA%B5%AC%EC%A1%B0.md)

## 한눈에 보기
CNN 발전 흐름을 LeNet에서 ResNet까지 정리하고, 깊은 convolutional network가 classification accuracy를 높이는 방식과 그 한계를 설명한 사전보고서이다.

## 핵심 개념
- LeNet
- AlexNet
- VGGNet
- GoogLeNet
- ResNet
- ImageNet
- depth degradation

## 이론 정리
- LeNet은 convolution, pooling, fully connected layer의 기본 조합을 제시한 초기 CNN 구조다.
- AlexNet은 ReLU, dropout, GPU 학습, data augmentation을 통해 대규모 ImageNet classification에서 CNN의 우수성을 보였다.
- VGGNet은 작은 3x3 convolution을 깊게 쌓는 단순한 규칙으로 receptive field를 키우고 구조 분석을 쉽게 만들었다.
- GoogLeNet은 inception module로 여러 kernel scale을 병렬 사용하고 1x1 convolution으로 계산량을 줄였다.
- ResNet은 residual connection으로 degradation 문제를 해결해 매우 깊은 network 학습을 가능하게 했다.

## 중요한 수식과 관점
- Convolution output size: $H_{out}=\lfloor (H+2P-K)/S \rfloor + 1$.
- Residual block: $y=F(x)+x$. 해상도나 채널 수가 바뀌면 projection shortcut을 사용한다.

## 실습과의 연결
Lab03 구현에서는 VGG16과 ResNet50을 CIFAR-10 3-class 문제에 적용했다. 사전보고서의 구조 비교는 왜 VGG에는 BatchNorm이 도움이 되고, 왜 ResNet에는 shortcut projection이 필요한지 이해하는 배경이 된다.

## 추가 해석 포인트
- LeNet에서 ResNet까지의 흐름은 단순히 layer 수 증가가 아니라, nonlinearity, normalization, shortcut, multi-scale branch처럼 optimization을 가능하게 만드는 장치의 발전으로 읽어야 한다.
- VGG의 3x3 stack은 같은 receptive field를 더 많은 nonlinearity로 표현한다는 장점이 있지만, parameter와 memory cost가 커진다.
- ResNet의 shortcut은 feature reuse이면서 동시에 gradient highway다. 따라서 깊은 모델을 비교할 때 parameter 수보다 gradient path 길이를 함께 봐야 한다.
- Lab03 결과에서 ResNet이 항상 VGG보다 좋지 않았던 것은 구조 우열이 아니라 데이터 규모, 학습 epoch, augmentation, hyperparameter가 함께 작용한 결과로 해석해야 한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md)


---

이전: [13. Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md) · 다음: [15. Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md)
