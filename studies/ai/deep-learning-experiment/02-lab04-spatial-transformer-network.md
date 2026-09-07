---
layout: page
title: "02. Lab04 Spatial Transformer Network"
permalink: /studies/ai/deep-learning-experiment/02-lab04-spatial-transformer-network/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/02%20Lab04%20Spatial%20Transformer%20Network.md)

{% raw %}
## 한눈에 보기
왜곡된 MNIST 분류 문제에서 CNN 앞단에 Spatial Transformer Network(STN)를 붙여 입력을 학습 가능한 방식으로 정렬하는 실습이다. STN은 별도 annotation 없이 task loss만으로 변환 파라미터를 학습하며, CNN의 공간 불변성을 보완한다.

## 핵심 개념
- distorted MNIST
- localization network
- affine transform
- grid generator
- bilinear sampler
- ReduceLROnPlateau

## 실습 흐름
- MNIST 이미지를 80x80으로 키우고 회전, 이동, 크기 변화가 포함된 distorted setting을 만든다.
- STN_CNN은 CNN backbone 앞에 localization network를 두고 affine 변환 파라미터 $\theta$를 예측한다.
- `affine_grid`로 sampling grid를 만들고 `grid_sample`의 bilinear interpolation으로 transformed input을 만든다.
- localization network의 마지막 FC layer는 identity transform이 되도록 weight는 0, bias는 `[1,0,0,0,1,0]`으로 초기화한다.
- STN이 있는 모델과 없는 일반 CNN을 각각 학습하여 accuracy와 시각화 결과를 비교한다.

## 구현과 이론의 연결
- STN은 localization network, grid generator, sampler 세 부분으로 구성된다. 핵심은 sampler가 미분 가능해야 전체 구조를 end-to-end로 학습할 수 있다는 점이다.
- 이번 구현은 STN 뒤에 average pooling을 적용하여 distorted input 크기를 줄인 뒤 CNN backbone으로 전달한다.
- learning rate는 validation/test accuracy가 개선되지 않을 때 줄어드는 `ReduceLROnPlateau` 방식으로 조절했다.

## 결과와 해석
- STN 모델의 checkpoint 기준 best accuracy는 약 99.34%였다.
- STN이 없는 CNN도 약 98.42%까지 도달했다. MNIST 자체가 비교적 단순하기 때문에 STN의 이득은 약 0.9%p 정도로 나타났다.
- 시각화에서는 STN이 입력 숫자를 중앙에 더 잘 정렬하려는 경향을 보였지만, 숫자 분류 task가 쉬워 회전 파라미터가 강하게 학습되지 않는 경우도 있었다.

## 자주 헷갈리는 지점
- identity 초기화를 하지 않으면 학습 초기에 입력이 심하게 왜곡되어 classifier가 불안정해질 수 있다.
- `grid_sample`의 좌표계는 -1~1 정규화 좌표를 사용하므로 pixel 좌표와 혼동하지 않아야 한다.
- STN의 계산 overhead가 있으므로, 데이터가 단순할 때는 성능 이득 대비 비용을 함께 평가해야 한다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md)
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab08 Grad-CAM과 Guided Backpropagation](06-lab08-grad-cam-guided-backpropagation.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. Lab04 Spatial Transformer Network**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **02. Lab04 Spatial Transformer Network**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md) · 다음: [03. Lab05 FSRCNN 초해상도](03-lab05-fsrcnn.md)
