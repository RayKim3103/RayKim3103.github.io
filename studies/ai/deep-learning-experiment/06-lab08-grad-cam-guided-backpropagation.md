---
layout: page
title: "06. Lab08 Grad-CAM과 Guided Backpropagation"
permalink: /studies/ai/deep-learning-experiment/06-lab08-grad-cam-guided-backpropagation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/06%20Lab08%20Grad-CAM%EA%B3%BC%20Guided%20Backpropagation.md)

## 한눈에 보기
pretrained AlexNet과 VGG16의 예측 근거를 시각화하는 실습이다. Grad-CAM은 마지막 convolution feature map의 gradient를 사용해 class-discriminative region을 찾고, Guided Backpropagation은 입력 gradient의 fine detail을 제공한다.

## 핵심 개념
- model interpretability
- AlexNet
- VGG16
- CAM
- Grad-CAM
- Guided Backpropagation
- Guided Grad-CAM
- hook

## 실습 흐름
- AlexNet과 VGG16 구조를 직접 정의하고 ImageNet pretrained weight를 정확히 매핑한다.
- 관심 class score에 대해 backward를 수행하여 마지막 convolution feature와 gradient를 hook으로 저장한다.
- gradient를 global average pooling해 feature map별 중요도 $\alpha_k^c$를 계산한다.
- weighted feature map 합에 ReLU를 적용해 Grad-CAM heatmap을 만들고 원본 이미지 위에 overlay한다.
- Guided Backpropagation의 input gradient와 Grad-CAM을 결합하여 Guided Grad-CAM을 만든다.

## 구현과 이론의 연결
- Grad-CAM 수식은 $\alpha_k^c = \frac{1}{Z}\sum_i\sum_j \partial y^c/\partial A_{ij}^k$와 $L^c=ReLU(\sum_k \alpha_k^c A^k)$로 요약된다.
- CAM은 GAP 구조가 필요하지만 Grad-CAM은 architecture 변경과 retraining 없이 기존 CNN에 적용할 수 있다.
- Guided Backpropagation은 ReLU backward에서 음수 gradient를 차단해 edge/detail을 강조한다. 단독으로는 class localization이 약하고, Grad-CAM과 결합하면 coarse location과 fine detail을 함께 얻는다.

## 결과와 해석
- classifier weight를 정확히 load하지 않으면 heatmap 품질이 크게 나빠졌다. pretrained key/value 매핑이 해석 결과에 직접적인 영향을 준다.
- AlexNet보다 VGG16에서 더 안정적인 localization 결과를 얻었다. 더 깊고 규칙적인 convolution hierarchy가 class-specific feature를 잘 형성했기 때문으로 해석된다.
- Guided Grad-CAM은 Guided Backpropagation 단독보다 class label과 더 잘 맞는 시각화 결과를 보였다.

## 자주 헷갈리는 지점
- model을 `eval()`로 두지 않으면 dropout/batchnorm 동작 때문에 재현성이 흔들릴 수 있다.
- hook을 잘못 건 layer가 classifier 뒤쪽이면 공간 정보를 잃어 heatmap이 의미 없어질 수 있다.
- random classifier weight로 만든 Grad-CAM은 그럴듯하게 보이더라도 신뢰하면 안 된다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md)
- [Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)
- [Lab10 Neural Style Transfer](08-lab10-neural-style-transfer.md)


---

이전: [05. Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md) · 다음: [07. Lab09 Quantization Interval Learning](07-lab09-quantization-interval-learning.md)
