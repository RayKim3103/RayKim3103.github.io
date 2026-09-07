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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Lab07 RetinaNet과 Focal Loss**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.
- loss가 직접 최적화하는 것과 metric이 평가하는 것을 분리해야 결과를 제대로 해석할 수 있다.
- 재현 실험은 seed, data split, augmentation, learning rate schedule, checkpoint 기준이 모두 결과에 영향을 준다.

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
- **05. Lab07 RetinaNet과 Focal Loss**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md) · 다음: [06. Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)
