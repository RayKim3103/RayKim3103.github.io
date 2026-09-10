---
layout: page
title: "05. Lab07 — RetinaNet & Focal Loss"
permalink: /studies/ai/deep-learning-experiment/05-lab07-retinanet-focal-loss/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(18) + 본보고서(05) 통합·보강
- 이론 배경은 RetinaNet / Focal Loss 논문 수준으로 보강. **실험 설정은 원 보고서 그대로**입니다.

{% raw %}
## 개요

Object detection: 이미지에서 (클래스, bounding box)를 동시에 예측한다. **one-stage detector RetinaNet**을 구현하고, dense anchor의 극심한 **foreground/background 불균형**을 **Focal Loss**로 완화. 데이터는 PASCAL VOC.

## 1. 이론 배경

- **two-stage**(Faster R-CNN 등): region proposal 단계가 foreground 후보를 선별 → 불균형이 완화됨. 정확하지만 느림.
- **one-stage**(YOLO/SSD/RetinaNet): dense anchor 전체를 바로 분류 → 빠르지만 **대부분 anchor가 배경** → class imbalance 심각. 쉬운 배경 예제가 loss를 지배해 학습이 안 됨.

### Focal Loss
$$
\text{CE}(p_t) = -\log p_t,\qquad
\text{FL}(p_t) = -(1-p_t)^{\gamma}\log p_t
$$
alpha-balanced: $$-\alpha_t(1-p_t)^{\gamma}\log p_t$$.

- $$(1-p_t)^\gamma$$: 잘 맞춘(easy) 예제의 loss 기여를 낮추고 **hard example에 집중**.
- $$\gamma$$↑ → easy 억제 강해짐. 너무 크면 학습 신호까지 줄어듦.
- **hard negative mining을 loss 함수 안에 넣은** 방식으로 볼 수 있다.

### RetinaNet 구조
`ResNet backbone → FPN → (classification subnet, box regression subnet)`
- **FPN**: semantic이 강한 deep feature + 해상도 높은 shallow feature를 top-down pathway로 결합 → 작은 객체·큰 객체 동시 처리.
- classification head bias를 **prior 0.01**로 초기화 → 초기에 모든 anchor를 positive로 보는 불안정 완화.

## 2. 실습 설계

- PASCAL VOC 형식의 image / annotation / bbox / label 로더 + 시각화.
- backbone = **ImageNet pretrained ResNet-18**, C3·C4·C5 feature 추출.
- FPN으로 멀티 스케일 pyramid feature 구성.
- pyramid level마다 anchor 생성 → classification subnet + regression subnet.
- **Focal Loss + box regression loss** 합산 학습. inference: confidence threshold → top-k → **NMS**. test는 COCO evaluator 스타일 bbox 평가.

## 3. 구현 핵심

- 구현 모듈: backbone / FPN / anchor assignment / head / loss / train·test 루프.
- Focal Loss는 **sigmoid 기반 multi-label** 형태 → softmax CE와 target shape이 다르다.
- **positive anchor 수로 normalization** 필요(이미지별 객체 수 차이가 loss scale을 흔듦).
- anchor 좌표 형식 `(x1,y1,x2,y2)`과 image transform 후 크기 보정이 어긋나면 학습 전체가 무너진다.

## 4. 결과 · 관찰

- 핵심 관찰: **Focal Loss가 단순 CE보다 배경 anchor의 지배를 줄여** one-stage detector 학습을 가능하게 한다.
- 성능은 **anchor matching 품질, FPN feature 해상도, NMS threshold, class prior 초기화**에 크게 좌우 — anchor assignment IoU threshold가 Focal Loss만큼 중요.

## 5. 자주 틀리는 지점

- anchor 좌표 형식 ↔ transform 후 크기 보정 불일치.
- Focal Loss(sigmoid multi-label) ↔ softmax CE target shape 혼동.
- positive anchor 수 normalization 누락.
- $$\gamma$$를 너무 키우면 학습 신호까지 억제.

## 복습 질문

- one-stage detector의 class imbalance 문제와 Focal Loss가 이를 다루는 방식은?
- $$\text{FL}(p_t) = -(1-p_t)^\gamma\log p_t$$에서 $$(1-p_t)^\gamma$$ 항의 역할과 $$\gamma$$의 효과는?
- FPN이 작은 객체 검출에 도움이 되는 이유는?
- classification head bias를 prior 0.01로 초기화하는 이유는?
{% endraw %}

---

이전: [04. Lab06 — Semantic Segmentation](04-lab06-semantic-segmentation.md) · 다음: [06. Lab08 — CAM & Grad-CAM](06-lab08-cam-grad-cam.md)
