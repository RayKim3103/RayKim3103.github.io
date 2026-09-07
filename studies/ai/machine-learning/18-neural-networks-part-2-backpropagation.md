---
layout: page
title: "18. Neural Networks Part 2: Backpropagation"
permalink: /studies/ai/machine-learning/18-neural-networks-part-2-backpropagation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/18%20Neural%20Networks%20Part%202%20-%20Backpropagation.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #neural-network #backpropagation #chain-rule #vectorization

관련 노트: [Neural Networks Part 1: 다층신경망 기초](17-neural-networks-part-1.md), [Neural Networks Part 3: 실전 학습과 정규화](19-neural-networks-part-3.md)

## 핵심 요약

이 강의는 computational graph에서 chain rule을 적용해 gradient를 계산하는 backpropagation을 자세히 설명한다. Scalar 예제에서 시작해 gate별 local gradient, sigmoid layer, vector/matrix 연산, modular forward/backward API로 확장한다.

## Computational Graph

복잡한 함수는 작은 연산 노드들의 graph로 표현할 수 있다.

예:

```text
f(x,y,z) = (x + y) * z
```

Graph는 덧셈 노드와 곱셈 노드로 나뉜다. Forward pass에서는 각 노드의 출력값을 계산하고, backward pass에서는 최종 loss가 각 입력에 얼마나 민감한지 계산한다.

## Chain Rule

Backpropagation의 핵심은 chain rule이다.

```text
dL/dx = dL/dz * dz/dx
```

각 노드는 upstream gradient `dL/dout`을 받아 local gradient와 곱해 downstream gradient를 만든다.

```text
downstream gradient = upstream gradient * local gradient
```

## Gate별 Gradient Pattern

### Add Gate

덧셈은 gradient를 그대로 분배한다.

```text
z = x + y
dL/dx = dL/dz
dL/dy = dL/dz
```

### Multiply Gate

곱셈은 상대 입력값을 곱해 gradient를 전달한다.

```text
z = x*y
dL/dx = dL/dz * y
dL/dy = dL/dz * x
```

### Max Gate

Max gate는 forward에서 선택된 입력으로만 gradient를 보낸다.

```text
z = max(x,y)
gradient flows to argmax input
```

## Sigmoid Layer

Sigmoid 함수의 미분은 출력값으로 간단히 표현된다.

```text
sigmoid(x) = 1 / (1 + exp(-x))
d sigmoid / dx = sigmoid(x) * (1 - sigmoid(x))
```

Forward pass에서 sigmoid output을 저장해 두면 backward pass에서 효율적으로 gradient를 계산할 수 있다.

## Flat Code와 Modular Code

초기 예제는 모든 연산과 미분을 한 코드에 직접 쓸 수 있다. 그러나 neural network는 layer와 parameter가 많아지므로 각 operation을 module로 만들고 `forward()`와 `backward()` API를 제공하는 구조가 필요하다.

```text
forward(input) -> output, cache
backward(upstream_gradient, cache) -> input_gradient, parameter_gradient
```

Cache에는 backward 계산에 필요한 forward 중간값을 저장한다.

## Vector Derivatives

Neural network 구현에서는 scalar보다 vector와 matrix가 기본이다.

중요한 원칙:

```text
어떤 변수에 대한 loss gradient는 그 변수와 같은 shape를 가진다.
```

예를 들어 `W`가 `N x M` matrix이면 `dL/dW`도 `N x M`이다.

## ReLU Backpropagation

ReLU는 elementwise로 적용된다.

```text
f(x) = max(0, x)
```

Backward:

```text
x > 0이면 gradient 통과
x <= 0이면 gradient 0
```

## Matrix Multiplication Backpropagation

Matrix multiplication에서도 chain rule을 shape에 맞게 적용한다.

```text
Y = XW
dL/dX = dL/dY * W^T
dL/dW = X^T * dL/dY
```

이 식은 fully connected layer의 backward pass에서 핵심이다.

## Vectorization

여러 data point를 한 번에 matrix로 처리하면 반복문보다 효율적이다.

```text
X: batch input
W: weight matrix
Y = XW
```

Batch 단위 backpropagation은 모든 sample의 gradient contribution을 matrix 연산으로 모아 계산한다.

## 시험ㆍ복습 체크포인트

- Upstream gradient, local gradient, downstream gradient의 관계를 설명할 수 있어야 한다.
- Add, multiply, max gate의 backward rule을 이해해야 한다.
- Sigmoid와 ReLU의 derivative를 쓸 수 있어야 한다.
- Matrix multiplication backward 식의 shape를 검산할 수 있어야 한다.
- Forward/backward API에서 cache가 필요한 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **18. Neural Networks Part 2: Backpropagation**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **18. Neural Networks Part 2: Backpropagation**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [17. Neural Networks Part 1: 다층신경망 기초](17-neural-networks-part-1.md) · 다음: [19. Neural Networks Part 3: 실전 학습과 정규화](19-neural-networks-part-3.md)
