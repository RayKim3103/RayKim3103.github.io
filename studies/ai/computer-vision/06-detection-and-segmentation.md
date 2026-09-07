---
layout: page
title: "06. Detection and Segmentation"
permalink: /studies/ai/computer-vision/06-detection-and-segmentation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/06%20Detection%20and%20Segmentation.md)

{% raw %}
## 한눈에 보기
Detection and Segmentation 강의는 image-level classification을 넘어 pixel-level 또는 object-level prediction으로 확장하는 방법을 다룬다.

## 핵심 개념
- semantic segmentation
- object detection
- instance segmentation
- FCN
- upsampling
- R-CNN
- YOLO
- IoU
- mAP

## 체계적 정리
- semantic segmentation은 각 pixel에 class label을 부여한다.
- object detection은 이미지 안의 여러 객체에 대해 bounding box와 class를 예측한다.
- instance segmentation은 같은 class의 객체라도 서로 다른 instance mask를 구분한다.
- FCN은 fully connected layer를 convolution으로 바꾸어 dense score map을 만든다.
- R-CNN 계열은 region proposal 또는 후보 영역을 사용하고, YOLO/SSD 계열은 one-stage dense prediction을 수행한다.
- IoU와 mAP는 box/mask localization 품질을 평가하는 핵심 지표다.

## 중요한 수식과 관점
- IoU: intersection area / union area.
- mAP는 class별 precision-recall curve 아래 면적을 평균한 지표다.
- Transposed convolution은 learnable upsampling으로 score map을 원본 해상도에 맞춘다.

## 구현과 학습 포인트
segmentation은 CNN feature의 spatial resolution 손실을 어떻게 복구할지가 중요하다. detection은 classification과 localization을 동시에 학습하므로 box regression loss와 classification loss의 균형을 봐야 한다.

## 자주 헷갈리는 지점
- classification accuracy와 detection mAP는 전혀 다른 지표다.
- box 좌표 형식과 image resizing 보정을 틀리면 IoU가 크게 깨진다.
- segmentation에서 background가 많으면 pixel accuracy가 높아도 객체 class 성능이 낮을 수 있다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Classification Evaluation Metrics](03-3-classification-evaluation-metrics.md)
- [Neural Networks 2](05-neural-networks-2.md)
- [Transformers](07-transformers.md)

{% endraw %}

---

이전: [05. Neural Networks 2](05-neural-networks-2.md) · 다음: [07. Transformers](07-transformers.md)
