---
layout: page
title: "18. Lab07 사전보고서 Focal Loss와 RetinaNet"
permalink: /studies/ai/deep-learning-experiment/18-prep-lab07-focal-loss-retinanet/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/18%20Lab07%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Focal%20Loss%EC%99%80%20RetinaNet.md)

{% raw %}
## 한눈에 보기
Focal Loss와 RetinaNet 논문을 중심으로 one-stage object detector의 class imbalance 문제를 정리한 사전보고서이다.

## 핵심 개념
- class imbalance
- one-stage detector
- two-stage detector
- RetinaNet
- FPN
- Focal Loss
- anchor matching

## 이론 정리
- two-stage detector는 proposal 단계가 있어 foreground 후보를 어느 정도 선별하지만, one-stage detector는 dense anchor 전체를 바로 분류한다.
- Focal Loss는 easy negative의 loss 기여를 줄여 rare foreground와 hard negative에 학습 신호를 집중시킨다.
- RetinaNet은 ResNet backbone, FPN, classification subnet, box regression subnet으로 구성된다.
- classification subnet bias를 small prior로 초기화하면 초기 학습에서 background/foreground imbalance에 의한 불안정을 줄인다.

## 중요한 수식과 관점
- $CE(p_t)=-\log(p_t)$.
- $FL(p_t)=-(1-p_t)^\gamma\log(p_t)$, alpha-balanced 형태는 $-\alpha_t(1-p_t)^\gamma\log(p_t)$.

## 실습과의 연결
Lab07 구현은 PASCAL VOC annotation parsing, FPN feature construction, anchor assignment, Focal Loss, bbox regression, inference filtering/NMS로 연결된다.

## 추가 해석 포인트
- Focal Loss는 sample selection을 loss function 안으로 넣은 방식으로 볼 수 있다. hard negative mining을 명시적으로 하지 않아도 비슷한 효과를 낸다.
- gamma가 커질수록 easy example 억제가 강해지지만, 너무 크면 충분한 학습 신호까지 줄어들 수 있다.
- FPN은 semantic이 강한 deep feature와 resolution이 높은 shallow feature를 top-down pathway로 결합한다.
- RetinaNet 구현에서는 anchor assignment 기준 IoU threshold가 positive/negative balance를 결정하므로 Focal Loss만큼 중요하다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)
- [Lab06 사전보고서 FCN](17-prep-lab06-fcn.md)
- [Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **18. Lab07 사전보고서 Focal Loss와 RetinaNet**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **18. Lab07 사전보고서 Focal Loss와 RetinaNet**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [17. Lab06 사전보고서 FCN](17-prep-lab06-fcn.md) · 다음: [19. Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md)
