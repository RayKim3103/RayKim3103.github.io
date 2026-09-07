---
layout: page
title: "01. Lab03 VGGNet과 ResNet 구현"
permalink: /studies/ai/deep-learning-experiment/01-lab03-vggnet-resnet/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Deep_Learning_Experiment/lecture_notes/01%20Lab03%20VGGNet%EA%B3%BC%20ResNet%20%EA%B5%AC%ED%98%84.md)

{% raw %}
## 한눈에 보기
CIFAR-10 중 plane, car, bird 세 클래스만 사용하여 VGG16과 ResNet50을 직접 구현하고 분류 성능을 비교한 실습이다. 같은 데이터 전처리와 cross-entropy 목적함수 아래에서 단순히 깊은 네트워크와 residual shortcut을 가진 네트워크가 어떻게 다르게 학습되는지 확인한다.

## 핵심 개념
- CIFAR-10 3-class classification
- VGG16 Type-D
- Batch Normalization
- ResNet50 Bottleneck
- Residual connection
- Cross Entropy
- train/test accuracy

## 실습 흐름
- CIFAR-10 train/test set에서 세 클래스만 필터링하여 train 15,000장, test 3,000장으로 축소한다.
- train transform은 random crop과 horizontal flip으로 일반화를 보강하고, test transform은 tensor 변환 및 정규화 중심으로 둔다.
- VGG16은 3x3 convolution, BatchNorm, ReLU, MaxPool을 반복하고 마지막 classifier를 3-class 출력으로 바꾼다.
- ResNet50은 1x1-3x3-1x1 bottleneck block을 만들고, downsampling 시 shortcut에도 1x1 projection을 둔다.
- 각 epoch마다 train loss, test loss, test accuracy를 출력해 수렴과 과적합 신호를 비교한다.

## 구현과 이론의 연결
- VGG의 장점은 구조가 직관적이고 구현이 간단하다는 점이지만, 깊이가 늘어날수록 gradient 흐름이 불안정해질 수 있다. 실습에서는 convolution 뒤 BatchNorm을 넣어 최적화를 안정화했다.
- ResNet은 $F(x)+x$ 형태의 residual learning을 통해 identity mapping을 보존한다. shortcut 경로는 깊은 네트워크가 최소한 입력 정보를 잃지 않도록 해주며, 채널 수나 해상도가 바뀔 때 projection shortcut이 필요하다.
- `nn.CrossEntropyLoss`는 softmax 이전 logits와 정수 class index를 입력받는다. 따라서 모델의 마지막에 softmax를 붙이지 않는 것이 올바른 구현이다.

## 결과와 해석
- VGG16은 20 epoch 동안 학습했으며 최고 test accuracy가 약 92.5% 수준까지 올라갔다. 초반 7~13 epoch 부근에서 이미 90% 이상으로 포화되는 경향을 보였다.
- ResNet50은 15 epoch 동안 학습했고 최고 test accuracy가 약 85.7% 수준이었다. 이 실험 설정에서는 VGG보다 낮았는데, 세 클래스만 쓰는 작은 입력과 짧은 학습 조건에서는 더 복잡한 모델이 항상 유리하지 않다는 점을 보여준다.
- VGG에서 BatchNorm을 제거하면 정확도가 낮은 값에 머무를 수 있어, normalization이 깊은 CNN의 학습 안정성에 결정적임을 확인했다.

## 자주 헷갈리는 지점
- CIFAR-10 원본 10개 클래스를 그대로 쓰지 않고 세 클래스만 필터링했다는 점을 metric 해석에 반영해야 한다.
- shortcut projection의 stride와 channel 수를 잘못 맞추면 residual addition에서 shape mismatch가 발생한다.
- train accuracy가 계속 좋아져도 test loss가 증가하면 과적합 신호로 읽어야 한다.

## 복습 질문
- 이 실습에서 모델이 해결하려는 입력/출력 문제는 무엇인가?
- loss function이 실제 평가 지표와 어떤 관계를 갖는가?
- shape, normalization, train/eval mode 중 어디가 틀리면 결과가 가장 크게 흔들리는가?

## 연결 노트
- [Lab03 사전보고서 CNN 구조](14-prep-lab03-cnn.md)
- [Lab04 Spatial Transformer Network](02-lab04-spatial-transformer-network.md)
- [Lab06 Semantic Segmentation](04-lab06-semantic-segmentation.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Lab03 VGGNet과 ResNet 구현**를 다루며, 딥러닝 논문/모델을 코드로 재현하며 architecture, loss, training loop, evaluation을 실험적으로 익히는 과목이다.
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
- **01. Lab03 VGGNet과 ResNet 구현**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [02. Lab04 Spatial Transformer Network](02-lab04-spatial-transformer-network.md)
