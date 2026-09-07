---
layout: page
title: "04. Lab06 Semantic Segmentation"
permalink: /studies/ai/deep-learning-experiment/04-lab06-semantic-segmentation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/04%20Lab06%20Semantic%20Segmentation.md)

{% raw %}
## 한눈에 보기
이미지 전체를 하나의 label로 분류하는 classification을 넘어, 각 pixel에 class label을 부여하는 semantic segmentation 실습이다. VGG 기반 sliding-window 접근의 비효율을 확인한 뒤 FCN8s와 dilated convolution 기반 개선 모델을 구현한다.

## 핵심 개념
- semantic segmentation
- sliding window
- fully convolutional network
- FCN8s
- skip connection
- transposed convolution
- dilated convolution
- mIoU

## 실습 흐름
- VGG16 image classifier를 patch마다 적용하는 sliding-window segmentation을 먼저 살펴본다.
- fully connected layer를 convolution layer로 변환하여 dense score map을 만드는 fully convolutional 구조를 구현한다.
- PASCAL VOC 2012의 21개 class(background 포함)를 대상으로 bilinear/deconvolution upsampling을 적용한다.
- FCN8s는 pool3, pool4, final score map을 skip connection으로 결합하여 coarse feature와 fine spatial detail을 함께 사용한다.
- MySegModel은 fc6/fc7를 dilated convolution으로 바꾸고 context module을 추가해 receptive field를 확장한다.

## 구현과 이론의 연결
- FCN의 중요한 아이디어는 classification network를 공간 위치별 classifier로 재해석하는 것이다. FC layer는 1x1 또는 7x7 convolution으로 바꿀 수 있고, 이렇게 하면 arbitrary input size에 대해 score map을 얻는다.
- Transposed convolution은 learnable upsampling을 제공한다. FCN8s에서는 2배, 2배, 8배 upsampling과 crop을 통해 skip feature의 alignment를 맞춘다.
- Dilated convolution은 resolution을 줄이지 않고 receptive field를 키운다. padding은 보통 `dilation * (kernel_size-1) / 2`로 잡아 feature map 크기를 유지한다.

## 결과와 해석
- FCN8s baseline의 mIoU는 약 0.35 수준으로 기록되었다.
- 제안한 MySegModel은 dilated context를 더해 약 0.39 mIoU를 기록하여 baseline보다 개선되었다.
- 자료의 요구 기준에는 0.5 mIoU 이상이 언급되었지만, 제한된 iteration과 pretrained weight 호환성 문제 때문에 실험 결과는 그보다 낮았다. 그래도 multi-scale context가 성능 향상 방향임은 확인했다.

## 자주 헷갈리는 지점
- skip connection crop boundary가 맞지 않으면 tensor addition에서 shape mismatch가 발생한다.
- pretrained VGG weight를 재사용하려면 fc6/fc7의 weight shape과 dilation 변경의 호환성을 신중히 맞춰야 한다.
- mIoU는 pixel accuracy보다 class imbalance에 민감하므로 background가 많은 데이터에서 더 의미 있는 지표다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab06 사전보고서 FCN](17-prep-lab06-fcn.md)
- [Lab03 VGGNet과 ResNet 구현](01-lab03-vggnet-resnet.md)
- [Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Lab06 Semantic Segmentation**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **04. Lab06 Semantic Segmentation**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. Lab05 FSRCNN 초해상도](03-lab05-fsrcnn.md) · 다음: [05. Lab07 RetinaNet과 Focal Loss](05-lab07-retinanet-focal-loss.md)
