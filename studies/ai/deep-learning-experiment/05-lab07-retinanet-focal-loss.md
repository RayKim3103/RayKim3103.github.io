---
layout: page
title: "05. Lab07 RetinaNet과 Focal Loss"
permalink: /studies/ai/deep-learning-experiment/05-lab07-retinanet-focal-loss/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/05%20Lab07%20RetinaNet%EA%B3%BC%20Focal%20Loss.md)

{% raw %}
## 한눈에 보기
Object detection에서 bounding box와 class를 동시에 예측하는 RetinaNet을 구현하고, one-stage detector의 foreground/background class imbalance를 Focal Loss로 완화하는 실습이다.

## 핵심 개념
- object detection
- RetinaNet
- one-stage detector
- anchor
- FPN
- classification subnet
- box regression
- focal loss
- PASCAL VOC

## 실습 흐름
- PASCAL VOC 형식의 image, annotation, bounding box, label을 loader로 읽고 시각화한다.
- ImageNet pretrained ResNet-18을 backbone으로 사용하고 C3, C4, C5 feature를 추출한다.
- FPN으로 여러 scale의 pyramid feature를 구성해 작은 객체와 큰 객체를 동시에 다룬다.
- 각 pyramid level마다 anchor를 만들고 classification subnet과 regression subnet을 적용한다.
- Focal Loss와 box regression loss를 합쳐 학습하고, inference에서는 confidence threshold, top-k filtering, NMS를 거친다.

## 구현과 이론의 연결
- RetinaNet은 two-stage detector처럼 region proposal을 따로 만들지 않는 one-stage 구조다. 빠르지만 대부분 anchor가 background라 class imbalance가 심하다.
- Focal Loss는 $FL(p_t)=-(1-p_t)^\gamma \log(p_t)$ 형태로 easy example의 loss를 줄이고 hard example에 더 큰 가중을 둔다. alpha balancing까지 쓰면 rare positive class의 영향도 보정할 수 있다.
- prior probability 0.01로 classification head bias를 초기화하면 학습 초기에 모든 anchor를 positive로 보는 불안정을 줄인다.

## 결과와 해석
- 구현은 backbone, FPN, anchor assignment, head, loss, train/test 루프로 나뉘었다. test 단계에서는 COCO evaluator 스타일의 bbox 평가 절차를 사용했다.
- 실습의 핵심 관찰은 Focal Loss가 단순 cross-entropy보다 background anchor의 지배를 줄여 one-stage detector 학습을 가능하게 한다는 점이다.
- RetinaNet의 성능은 anchor matching 품질, FPN feature resolution, NMS threshold, class prior 초기화에 크게 좌우된다.

## 자주 헷갈리는 지점
- anchor 좌표 형식(x1,y1,x2,y2)과 image transform 후 크기 보정이 어긋나면 학습 전체가 무너진다.
- Focal Loss는 sigmoid 기반 multi-label 형태로 구현되므로 softmax CE와 target shape이 다르다.
- positive anchor 수로 normalization하지 않으면 이미지별 객체 수 차이가 loss scale을 흔들 수 있다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)
- [Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md)
- [Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)

{% endraw %}

---

이전: [04. Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md) · 다음: [06. Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)
