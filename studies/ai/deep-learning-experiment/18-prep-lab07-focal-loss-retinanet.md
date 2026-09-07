---
layout: page
title: "18. Lab07 사전보고서 Focal Loss와 RetinaNet"
permalink: /studies/ai/deep-learning-experiment/18-prep-lab07-focal-loss-retinanet/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/18%20Lab07%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Focal%20Loss%EC%99%80%20RetinaNet.md)

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


---

이전: [17. Lab06 사전보고서 FCN](17-prep-lab06-fcn.md) · 다음: [19. Lab08 사전보고서 CAM과 Grad-CAM](19-prep-lab08-cam-grad.md)
