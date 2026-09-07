---
layout: page
title: "19. Neural Networks Part 3: 실전 학습과 정규화"
permalink: /studies/ai/machine-learning/19-neural-networks-part-3/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/19%20Neural%20Networks%20Part%203%20-%20%EC%8B%A4%EC%A0%84%20%ED%95%99%EC%8A%B5%EA%B3%BC%20%EC%A0%95%EA%B7%9C%ED%99%94.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #neural-network #optimization #mini-batch #dropout #regularization

관련 노트: [Neural Networks Part 2: Backpropagation](18-neural-networks-part-2-backpropagation.md), [Convolutional Neural Networks](20-convolutional-neural-networks.md)

## 핵심 요약

이 강의는 neural network를 실제로 학습할 때 만나는 optimization 문제와 regularization 기법을 다룬다. Learning rate, momentum, adaptive optimizer, mini-batch SGD, activation function 선택, data augmentation, L2 regularization, dropout, early stopping이 핵심이다.

## Neural Network Optimization의 어려움

Perceptron이나 linear model의 일부 목적함수는 convex라 global optimum으로 수렴하기 쉽다. 반면 다층 neural network의 loss surface는 non-convex이며 local minima, saddle point, plateau가 존재한다.

```text
training neural networks = large nonconvex optimization
```

## Learning Rate

Learning rate는 gradient update의 step size이다.

| Learning rate | 결과 |
|---:|---|
| 너무 작음 | 수렴이 느리고 plateau에 오래 머묾 |
| 너무 큼 | optimum을 지나쳐 불안정하거나 발산 |
| 적절함 | loss가 안정적으로 감소 |

실무에서는 여러 learning rate를 시도하고 validation 성능으로 선택한다.

## Adaptive Learning Rate와 Momentum

### Momentum

Momentum은 이전 update 방향을 누적해 현재 gradient와 함께 사용한다.

```text
v <- rho*v - eta*gradient
w <- w + v
```

일관된 방향으로는 빠르게 진행하고, 지그재그 진동은 줄이는 효과가 있다.

### Adaptive Optimizer

Adagrad, RMSProp, Adam은 parameter별 gradient 통계를 사용해 effective learning rate를 조정한다. 이 알고리즘들도 기본 learning rate hyperparameter를 가지므로 tuning이 필요하다.

## Learning Rate Decay

초기에는 큰 learning rate로 빠르게 이동하고, 후반에는 learning rate를 줄여 optimum 근처의 진동을 줄인다.

```text
eta_t decreases over epochs
```

## Mini-Batch SGD

Gradient descent는 전체 dataset을 사용하고, SGD는 sample 하나를 사용한다. Mini-batch SGD는 그 중간이다.

| 방식 | Gradient 계산 |
|---|---|
| Full batch | 전체 데이터 |
| SGD | sample 1개 |
| Mini-batch SGD | 작은 batch |

Mini-batch는 gradient noise와 계산 효율의 균형이 좋아 neural network 학습의 표준이다.

## Activation Functions

### Sigmoid

```text
sigma(x) = 1 / (1 + exp(-x))
```

출력이 `[0,1]`로 제한된다. 입력 절댓값이 크면 gradient가 거의 0이 되어 saturation 문제가 생긴다. 출력이 zero-centered가 아니라 optimization이 느려질 수 있다.

### tanh

출력이 `[-1,1]`이고 zero-centered라 sigmoid보다 나은 경우가 많다. 하지만 큰 입력에서 saturation되는 문제는 남아 있다.

### ReLU

```text
ReLU(x) = max(0,x)
```

양수 영역에서 gradient가 사라지지 않고 계산이 단순하다. 현대 neural network에서 널리 사용된다. 다만 음수 영역에서는 gradient가 0이라 neuron이 죽는 문제가 생길 수 있다.

## Overfitting과 Regularization

Neural network는 매우 복잡한 함수를 표현할 수 있으므로 overfitting 가능성이 크다. Regularization은 학습 문제에 제약을 추가해 일반화 성능을 높이는 기법이다.

## Data Augmentation

원본 데이터를 변형해 학습 sample을 늘린다.

예:

- translation
- rotation
- crop
- color jitter
- random combination

이미지 분류에서는 같은 label을 유지하는 변형을 사용한다.

## L2 Regularization

Loss에 weight magnitude penalty를 더한다.

```text
total loss = data loss + lambda * ||W||_2^2
```

Weight가 지나치게 커지는 것을 막아 모델의 variance를 줄인다.

## Dropout

Dropout은 training 중 각 forward pass에서 일부 neuron 출력을 확률적으로 0으로 만든다.

```text
drop probability = p
```

효과:

- 특정 neuron 조합에 과도하게 의존하는 것을 막는다.
- 여러 subnetworks를 평균내는 ensemble과 비슷한 효과가 있다.
- network가 redundant representation을 배우도록 유도한다.

Test time에는 모든 neuron을 사용하므로 activation scale을 맞춰야 한다.

## Early Stopping

Validation accuracy가 더 이상 좋아지지 않거나 감소하기 시작하면 학습을 멈춘다. 이는 추가 학습으로 training loss는 줄어도 generalization이 나빠지는 시점을 피하기 위한 regularization 방법이다.

## 시험ㆍ복습 체크포인트

- Non-convex loss에서 saddle point와 local minima가 학습을 어렵게 하는 이유를 설명할 수 있어야 한다.
- Learning rate가 너무 크거나 작을 때의 현상을 구분해야 한다.
- Momentum과 adaptive optimizer의 직관을 이해해야 한다.
- Sigmoid, tanh, ReLU의 장단점을 비교할 수 있어야 한다.
- Dropout과 early stopping이 overfitting을 줄이는 원리를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **19. Neural Networks Part 3: 실전 학습과 정규화**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
- 모델의 수식은 외울 식이 아니라 어떤 확률적 가정이나 기하학적 가정에서 나왔는지 함께 보아야 한다.
- training error와 validation/test 성능이 달라지는 이유를 bias, variance, regularization으로 연결한다.
- 분류와 회귀는 모두 feature representation, objective, optimization, evaluation의 선택 문제로 정리할 수 있다.

### 문제 풀이 또는 구현 루틴

- 입력/출력, label의 의미, loss가 벌주는 오류의 종류를 먼저 적는다.
- 수식 전개에서는 차원을 확인하고 scalar objective와 vector/matrix gradient가 맞는지 본다.
- 결과는 accuracy 하나로 끝내지 말고 confusion matrix, precision/recall, calibration, overfitting을 함께 해석한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 모델이 복잡하면 항상 좋은 것이 아니라 데이터 수, noise, regularization과 함께 봐야 한다.
- test set으로 hyperparameter를 고르면 일반화 성능을 과대평가한다.
- loss를 최소화하는 것과 실제 의사결정 비용을 최소화하는 것은 다를 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제의 가정이 깨지는 데이터는 어떤 모습인가?
- regularization이나 prior가 모델의 해를 어느 방향으로 움직이는가?
- 성능이 나빠졌을 때 데이터, feature, loss, optimization 중 무엇부터 의심할 것인가?
- **19. Neural Networks Part 3: 실전 학습과 정규화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [18. Neural Networks Part 2: Backpropagation](18-neural-networks-part-2-backpropagation.md) · 다음: [20. Convolutional Neural Networks](20-convolutional-neural-networks.md)
