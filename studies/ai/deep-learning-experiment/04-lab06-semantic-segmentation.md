---
layout: page
title: "04. Lab06 Semantic Segmentation"
permalink: /studies/ai/deep-learning-experiment/04-lab06-semantic-segmentation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/04%20Lab06%20Semantic%20Segmentation.md)

## 한눈에 보기
이미지 전체를 하나의 label로 분류하는 classification을 넘어, 각 pixel에 class label을 부여하는 semantic segmentation 실습이다. VGG 기반 sliding-window 접근의 비효율을 확인한 뒤 FCN8s와 dilated convolution 기반 개선 모델을 구현한다.

## 핵심 개념
- semantic segmentation
- sliding window
- fully convolutional network
- FCN8s
- skip connection
- transposed convolution
- dilated convolution
- mIoU

## 실습 흐름
- VGG16 image classifier를 patch마다 적용하는 sliding-window segmentation을 먼저 살펴본다.
- fully connected layer를 convolution layer로 변환하여 dense score map을 만드는 fully convolutional 구조를 구현한다.
- PASCAL VOC 2012의 21개 class(background 포함)를 대상으로 bilinear/deconvolution upsampling을 적용한다.
- FCN8s는 pool3, pool4, final score map을 skip connection으로 결합하여 coarse feature와 fine spatial detail을 함께 사용한다.
- MySegModel은 fc6/fc7를 dilated convolution으로 바꾸고 context module을 추가해 receptive field를 확장한다.

## 구현과 이론의 연결
- FCN의 중요한 아이디어는 classification network를 공간 위치별 classifier로 재해석하는 것이다. FC layer는 1x1 또는 7x7 convolution으로 바꿀 수 있고, 이렇게 하면 arbitrary input size에 대해 score map을 얻는다.
- Transposed convolution은 learnable upsampling을 제공한다. FCN8s에서는 2배, 2배, 8배 upsampling과 crop을 통해 skip feature의 alignment를 맞춘다.
- Dilated convolution은 resolution을 줄이지 않고 receptive field를 키운다. padding은 보통 `dilation * (kernel_size-1) / 2`로 잡아 feature map 크기를 유지한다.

## 결과와 해석
- FCN8s baseline의 mIoU는 약 0.35 수준으로 기록되었다.
- 제안한 MySegModel은 dilated context를 더해 약 0.39 mIoU를 기록하여 baseline보다 개선되었다.
- 자료의 요구 기준에는 0.5 mIoU 이상이 언급되었지만, 제한된 iteration과 pretrained weight 호환성 문제 때문에 실험 결과는 그보다 낮았다. 그래도 multi-scale context가 성능 향상 방향임은 확인했다.

## 자주 헷갈리는 지점
- skip connection crop boundary가 맞지 않으면 tensor addition에서 shape mismatch가 발생한다.
- pretrained VGG weight를 재사용하려면 fc6/fc7의 weight shape과 dilation 변경의 호환성을 신중히 맞춰야 한다.
- mIoU는 pixel accuracy보다 class imbalance에 민감하므로 background가 많은 데이터에서 더 의미 있는 지표다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab06 사전보고서 FCN](17-prep-lab06-fcn.md)
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)


---

이전: [03. Lab05 FSRCNN 초해상도](03-lab05-fsrcnn.md) · 다음: [05. Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)
