---
layout: page
title: "06. Detection & Segmentation"
permalink: /studies/ai/computer-vision/06-detection-and-segmentation/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `06` 보강
- 표준 CV 교재(CS231n) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

image-level classification을 **pixel-level**(segmentation) 또는 **object-level**(detection) 예측으로 확장한다. 출력 형식이 바뀌므로 네트워크 구조와 loss, 평가지표가 모두 달라진다.

## 1. Semantic Segmentation

목표: 모든 pixel에 클래스 라벨. 같은 클래스의 서로 다른 객체는 구분하지 않음.

- **FCN (Fully Convolutional Network)**: FC층을 conv로 바꿔 임의 크기 입력 → 공간 해상도를 가진 출력. downsampling으로 잃은 해상도를 **upsampling(transposed conv / bilinear + conv)**으로 복원.
- **encoder–decoder + skip**(U-Net류): encoder의 고해상도 feature를 decoder에 이어붙여 경계 디테일 복원.
- **dilated/atrous conv**: 해상도를 덜 줄이면서 receptive field 확대.
- loss: pixel-wise cross-entropy(클래스 불균형 시 weighted / focal).
- 평가: **mIoU** = 클래스별 $\dfrac{\lvert P\cap G\rvert}{\lvert P\cup G\rvert}$ 의 평균, pixel accuracy.

## 2. Object Detection

목표: (클래스, bounding box) 여러 개. 출력 개수가 가변.

### Two-stage (R-CNN 계열)
- **R-CNN**: region proposal(예: selective search) → 각 영역 CNN 분류 + box 회귀. 느림.
- **Fast R-CNN**: 이미지 전체를 한 번 conv → RoI pooling으로 영역 feature 추출.
- **Faster R-CNN**: proposal도 학습(**RPN**, anchor 기반). 정확도 높음, 상대적으로 느림.

### One-stage
- **YOLO / SSD**: 격자 위 anchor마다 클래스+box를 한 번에 회귀. 빠름(실시간). 초기엔 작은 객체에 약함.
- **RetinaNet**: one-stage인데 **focal loss** $-(1-p_t)^\gamma \log p_t$ 로 극심한 foreground/background 불균형(쉬운 배경 예제가 loss를 지배하는 문제)을 해결.

### 공통 요소
- **anchor box**: 다양한 크기/비율의 기준 박스. 예측은 anchor에 대한 offset.
- **IoU**(Intersection over Union): box 겹침 정도. 학습 시 positive/negative 배정, 평가 시 매칭 기준.
- **NMS**(Non-Maximum Suppression): 같은 객체에 중복된 box 중 점수 최고만 남기고 IoU가 큰 나머지 제거.
- 평가: **mAP** = 여러 IoU 임계값·클래스에서의 average precision 평균(precision–recall 곡선 아래 넓이).

## 3. Instance Segmentation

객체별로 pixel mask까지. **Mask R-CNN** = Faster R-CNN + 각 RoI에 작은 mask head 추가(**RoIAlign**으로 정렬 오차 제거). Panoptic segmentation은 semantic(배경) + instance(전경)를 통합.

## 자주 틀리는 지점

- **semantic vs instance**: semantic은 "픽셀의 클래스", instance는 "픽셀의 클래스 + 객체 id".
- IoU는 학습(anchor 배정)과 평가(mAP) 양쪽에서 쓰이지만 임계값·용도가 다르다.
- NMS를 빼면 한 객체에 박스가 여러 개 남아 precision이 급락.
- one-stage가 항상 나쁜 게 아니라 불균형 문제(focal loss)와 multi-scale(FPN) 해결 후 정확도가 크게 올랐다.

## 복습 질문

- FCN이 classification CNN과 구조적으로 다른 점과, 해상도를 복원하는 방법은?
- two-stage와 one-stage detector의 파이프라인과 속도/정확도 trade-off는?
- IoU, NMS, mAP 각각이 detection의 어느 단계에서 쓰이는가?
- semantic / instance / panoptic segmentation의 출력 정의를 구분하라.

{% endraw %}

---

이전: [04. Neural Networks & CNNs](04-neural-networks-and-cnns.md) · 다음: [07. Transformers](07-transformers.md)
