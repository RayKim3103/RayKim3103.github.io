---
layout: page
title: "19. Lab08 사전보고서 CAM과 Grad-CAM"
permalink: /studies/ai/deep-learning-experiment/19-prep-lab08-cam-grad/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/19%20Lab08%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20CAM%EA%B3%BC%20Grad-CAM.md)

## 한눈에 보기
CAM과 Grad-CAM의 차이를 중심으로 CNN 해석 가능성을 정리한 사전보고서이다.

## 핵심 개념
- CAM
- Global Average Pooling
- weakly supervised localization
- Grad-CAM
- visual explanation
- guided backpropagation

## 이론 정리
- CAM은 마지막 convolution feature map 뒤에 GAP와 linear classifier가 있어야 class별 activation map을 만들 수 있다.
- Grad-CAM은 target class score의 gradient를 이용하므로 GAP 구조가 없는 CNN, detection, captioning, VQA 등에도 적용 가능하다.
- Grad-CAM은 coarse localization에 강하고 Guided Backpropagation은 edge/detail에 강하다.
- Guided Grad-CAM은 두 결과를 결합해 class-discriminative location과 세밀한 시각 패턴을 동시에 보여준다.

## 중요한 수식과 관점
- CAM: class weight와 feature map의 weighted sum.
- Grad-CAM: $L^c=ReLU(\sum_k \alpha_k^c A^k)$, $\alpha_k^c$는 gradient의 spatial average.

## 실습과의 연결
Lab08에서는 AlexNet/VGG16 pretrained weight를 load하고 hook을 사용해 forward activation과 backward gradient를 저장하여 heatmap을 생성했다.

## 추가 해석 포인트
- CAM은 구조 제약이 강한 대신 class weight와 activation map의 관계가 직관적이다. Grad-CAM은 범용성이 크지만 gradient 품질에 민감하다.
- heatmap은 causal proof가 아니라 diagnostic clue다. 강조된 영역을 제거하거나 삽입하는 perturbation test가 더 강한 검증이 될 수 있다.
- Guided Backpropagation은 세밀하지만 class-discriminative하지 않을 수 있다. 따라서 단독 결과보다 Grad-CAM과의 결합을 함께 보는 것이 좋다.
- Lab08에서 pretrained weight 매핑이 중요한 이유는 해석 방법이 모델 내부 표현의 품질에 직접 의존하기 때문이다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)
- [Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md)


---

이전: [18. Lab07 사전보고서 Focal Loss와 RetinaNet](18-prep-lab07-focal-loss-retinanet.md) · 다음: [20. Lab09 사전보고서 XNOR-Net과 양자화](20-prep-lab09-xnor-net.md)
