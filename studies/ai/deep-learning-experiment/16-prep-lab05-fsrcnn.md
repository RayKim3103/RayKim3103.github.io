---
layout: page
title: "16. Lab05 사전보고서 FSRCNN"
permalink: /studies/ai/deep-learning-experiment/16-prep-lab05-fsrcnn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/16%20Lab05%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20FSRCNN.md)

{% raw %}
## 한눈에 보기
SRCNN과 FSRCNN의 차이를 중심으로 single image super-resolution의 이론 배경을 정리한 사전보고서이다.

## 핵심 개념
- super-resolution
- SRCNN
- FSRCNN
- deconvolution
- shrinking
- expanding
- PSNR

## 이론 정리
- SRCNN은 bicubic으로 이미지를 먼저 HR 크기로 키운 뒤 CNN을 적용해 계산량이 크다.
- FSRCNN은 LR image를 그대로 입력받고 마지막 deconvolution에서만 upsampling을 수행한다.
- shrinking layer는 feature channel을 줄여 nonlinear mapping의 비용을 줄이고, expanding layer는 deconvolution 전 표현력을 회복한다.
- PSNR은 pixel-level reconstruction 성능을 수치화하는 기본 지표이며, 초해상도에서는 Y-channel 평가가 자주 사용된다.

## 중요한 수식과 관점
- MSE: $\frac{1}{N}\sum_i (\hat y_i-y_i)^2$.
- PSNR: $10\log_{10}(MAX_I^2/MSE)$.

## 실습과의 연결
Lab05 구현은 T91 patch 학습, Set5 평가, Adam optimizer, layer별 learning rate, border shaving, YCbCr 복원을 포함한다.

## 추가 해석 포인트
- super-resolution은 가능한 고주파 정보를 새로 추정하는 문제이므로, 단순 interpolation보다 data prior를 학습하는 CNN이 유리하다.
- FSRCNN은 LR feature space에서 대부분의 mapping을 수행하므로 속도 면에서 SRCNN보다 구조적으로 유리하다.
- deconvolution은 learnable upsampling이지만 checkerboard artifact 위험이 있어 kernel, stride, padding 선택을 신중히 해야 한다.
- PSNR이 높다고 항상 사람이 보기에 가장 자연스러운 이미지는 아니다. perceptual quality를 보려면 SSIM이나 perceptual metric도 함께 고려할 수 있다.
- FSRCNN 실험을 읽을 때는 PSNR 수치뿐 아니라 LR 공간에서 연산을 줄인 구조적 효율성과 HR 복원 품질 사이의 균형을 함께 보아야 한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab05 FSRCNN 초해상도](03-lab05-fsrcnn.md)
- [Lab10 사전보고서 Style Transfer](21-prep-lab10-style-transfer.md)

{% endraw %}

---

이전: [15. Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md) · 다음: [17. Lab06 사전보고서 FCN](17-prep-lab06-fcn.md)
