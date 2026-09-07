---
layout: page
title: "17. Lab06 사전보고서 FCN"
permalink: /studies/ai/deep-learning-experiment/17-prep-lab06-fcn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/17%20Lab06%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20FCN.md)

{% raw %}
## 한눈에 보기
Fully Convolutional Network와 DilatedNet을 중심으로 semantic segmentation의 배경을 정리한 사전보고서이다.

## 핵심 개념
- FCN
- dense prediction
- semantic segmentation
- skip architecture
- dilated convolution
- context module

## 이론 정리
- patchwise/sliding-window 방식은 각 pixel 주변 patch를 반복 계산하므로 매우 비효율적이다.
- FCN은 fully connected layer를 convolution으로 바꿔 한 번의 forward pass로 class score map을 출력한다.
- FCN-32s, FCN-16s, FCN-8s는 skip connection의 사용 정도에 따라 spatial detail 복원력이 달라진다.
- Dilated convolution은 pooling 없이 receptive field를 키워 dense prediction에서 context 정보를 유지한다.
- class imbalance와 boundary ambiguity 때문에 pixel accuracy보다 mIoU가 중요한 평가 지표가 된다.

## 중요한 수식과 관점
- mIoU: 각 class에 대해 $TP/(TP+FP+FN)$을 계산한 뒤 평균한다.
- Dilated convolution은 kernel element 사이에 간격을 두어 effective receptive field를 확장한다.

## 실습과의 연결
Lab06에서는 VGG 기반 FCN8s를 구현하고, fc6/fc7 및 context module에 dilation을 사용한 MySegModel로 개선을 시도했다.

## 추가 해석 포인트
- segmentation에서는 local texture와 global context가 모두 필요하다. FCN의 skip connection은 이 두 정보를 서로 다른 해상도에서 결합하는 장치다.
- deconvolution으로 해상도를 복원하더라도 pooling에서 사라진 boundary detail을 완전히 되살리기는 어렵다.
- dilated convolution은 receptive field를 키우지만 gridding artifact가 생길 수 있어 dilation rate 조합이 중요하다.
- mIoU는 rare class의 성능 저하를 더 잘 드러낸다. background pixel이 압도적인 데이터에서는 pixel accuracy만 보면 모델이 과대평가될 수 있다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md)
- [Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)

{% endraw %}

---

이전: [16. Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md) · 다음: [18. Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)
