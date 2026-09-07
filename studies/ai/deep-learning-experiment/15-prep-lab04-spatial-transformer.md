---
layout: page
title: "15. Lab04 사전보고서 Spatial Transformer"
permalink: /studies/ai/deep-learning-experiment/15-prep-lab04-spatial-transformer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/15%20Lab04%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20Spatial%20Transformer.md)

{% raw %}
## 한눈에 보기
Spatial Transformer Network 논문과 왜곡된 이미지 분류에서의 공간 정렬 개념을 정리한 사전보고서이다.

## 핵심 개념
- Spatial Transformer
- affine transform
- differentiable sampler
- localization network
- attention for geometry

## 이론 정리
- STN은 localization network가 변환 파라미터를 예측하고, grid generator가 sampling 위치를 만들며, sampler가 입력 feature에서 값을 보간한다.
- bilinear sampling은 주변 네 점의 가중합으로 출력 pixel을 만들기 때문에 gradient를 입력 feature와 transformation parameter로 전달할 수 있다.
- end-to-end 학습이 가능하므로 별도의 bounding box나 landmark annotation 없이 classification loss만으로 정렬 능력을 얻는다.
- distorted MNIST, cluttered MNIST, fine-grained recognition처럼 geometric nuisance가 큰 문제에서 효과가 크다.

## 중요한 수식과 관점
- Affine transform: $[x_s,y_s]^T=A[x_t,y_t,1]^T$.
- Bilinear sampler는 source 좌표 주변 pixel을 거리 기반 weight로 합산한다.

## 실습과의 연결
Lab04에서는 STN의 마지막 affine layer를 identity로 초기화하고, `affine_grid`와 `grid_sample`을 사용해 PyTorch에서 미분 가능한 geometric transform을 구현했다.

## 추가 해석 포인트
- STN은 image 전체뿐 아니라 feature map 중간에도 삽입될 수 있다. 이때 변환은 원본 pixel이 아니라 learned feature의 spatial layout을 정렬한다.
- affine transform은 translation, rotation, scale, shear를 표현하지만 non-rigid deformation에는 한계가 있다. 더 복잡한 변형에는 thin-plate spline 계열이 필요할 수 있다.
- classification loss만으로 geometric transform을 배우므로, network가 task에 도움이 되는 변환만 학습한다는 점이 장점이자 한계다.
- Lab04의 distorted MNIST 결과는 STN이 복잡한 데이터에서 더 큰 의미를 갖고, 단순 데이터에서는 accuracy gain보다 overhead가 부각될 수 있음을 보여준다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab04 Spatial Transformer Network](02-lab04-spatial-transformer-network.md)
- [Lab03 사전보고서 CNN 구조](14-prep-lab03-cnn.md)

{% endraw %}

---

이전: [14. Lab03 사전보고서 CNN 구조](14-prep-lab03-cnn.md) · 다음: [16. Lab05 사전보고서 FSRCNN](16-prep-lab05-fsrcnn.md)
