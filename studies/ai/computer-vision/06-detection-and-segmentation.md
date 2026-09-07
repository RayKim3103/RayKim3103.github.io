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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Detection & Segmentation**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.
- 분류, 검출, 분할 모델은 architecture보다 입력 표현, loss, annotation 형식, metric의 차이가 중요하다.
- 3D vision에서는 좌표계, 카메라 모델, homogeneous coordinate, rank/scale ambiguity를 계속 추적해야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **06. Detection & Segmentation**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Neural Networks & CNNs](04-neural-networks-and-cnns.md) · 다음: [07. Transformers](07-transformers.md)
