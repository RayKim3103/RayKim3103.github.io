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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **16. Lab05 사전보고서 FSRCNN**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 모델 구조를 읽을 때는 layer 이름보다 tensor shape, receptive field, skip connection, normalization 위치를 먼저 추적한다.

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
- **16. Lab05 사전보고서 FSRCNN**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [15. Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md) · 다음: [17. Lab06 사전보고서 FCN](17-prep-lab06-fcn.md)
