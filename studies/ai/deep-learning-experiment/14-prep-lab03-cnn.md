---
layout: page
title: "14. Lab03 사전보고서 CNN 구조"
permalink: /studies/ai/deep-learning-experiment/14-prep-lab03-cnn/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/14%20Lab03%20%EC%82%AC%EC%A0%84%EB%B3%B4%EA%B3%A0%EC%84%9C%20CNN%20%EA%B5%AC%EC%A1%B0.md)

{% raw %}
## 한눈에 보기
CNN 발전 흐름을 LeNet에서 ResNet까지 정리하고, 깊은 convolutional network가 classification accuracy를 높이는 방식과 그 한계를 설명한 사전보고서이다.

## 핵심 개념
- LeNet
- AlexNet
- VGGNet
- GoogLeNet
- ResNet
- ImageNet
- depth degradation

## 이론 정리
- LeNet은 convolution, pooling, fully connected layer의 기본 조합을 제시한 초기 CNN 구조다.
- AlexNet은 ReLU, dropout, GPU 학습, data augmentation을 통해 대규모 ImageNet classification에서 CNN의 우수성을 보였다.
- VGGNet은 작은 3x3 convolution을 깊게 쌓는 단순한 규칙으로 receptive field를 키우고 구조 분석을 쉽게 만들었다.
- GoogLeNet은 inception module로 여러 kernel scale을 병렬 사용하고 1x1 convolution으로 계산량을 줄였다.
- ResNet은 residual connection으로 degradation 문제를 해결해 매우 깊은 network 학습을 가능하게 했다.

## 중요한 수식과 관점
- Convolution output size: $H_{out}=\lfloor (H+2P-K)/S \rfloor + 1$.
- Residual block: $y=F(x)+x$. 해상도나 채널 수가 바뀌면 projection shortcut을 사용한다.

## 실습과의 연결
Lab03 구현에서는 VGG16과 ResNet50을 CIFAR-10 3-class 문제에 적용했다. 사전보고서의 구조 비교는 왜 VGG에는 BatchNorm이 도움이 되고, 왜 ResNet에는 shortcut projection이 필요한지 이해하는 배경이 된다.

## 추가 해석 포인트
- LeNet에서 ResNet까지의 흐름은 단순히 layer 수 증가가 아니라, nonlinearity, normalization, shortcut, multi-scale branch처럼 optimization을 가능하게 만드는 장치의 발전으로 읽어야 한다.
- VGG의 3x3 stack은 같은 receptive field를 더 많은 nonlinearity로 표현한다는 장점이 있지만, parameter와 memory cost가 커진다.
- ResNet의 shortcut은 feature reuse이면서 동시에 gradient highway다. 따라서 깊은 모델을 비교할 때 parameter 수보다 gradient path 길이를 함께 봐야 한다.
- Lab03 결과에서 ResNet이 항상 VGG보다 좋지 않았던 것은 구조 우열이 아니라 데이터 규모, 학습 epoch, augmentation, hyperparameter가 함께 작용한 결과로 해석해야 한다.

## 복습 질문
- 이 방법이 기존 방법의 어떤 병목을 해결하는가?
- 논문/이론의 가정과 실습 구현의 단순화된 설정은 무엇이 다른가?
- 실제 결과를 해석할 때 어떤 metric 또는 시각화를 함께 보아야 하는가?

## 연결 노트
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14. Lab03 사전보고서 CNN 구조**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **14. Lab03 사전보고서 CNN 구조**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. Project CARE 장기꼬리 CIFAR-100 구현](13-project-care-cifar-100.md) · 다음: [15. Lab04 사전보고서 Spatial Transformer](15-prep-lab04-spatial-transformer.md)
