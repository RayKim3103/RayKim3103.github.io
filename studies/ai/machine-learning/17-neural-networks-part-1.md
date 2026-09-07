---
layout: page
title: "17. Neural Networks Part 1: 다층신경망 기초"
permalink: /studies/ai/machine-learning/17-neural-networks-part-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Artificial_Intelligence/lecture_notes/17%20Neural%20Networks%20Part%201%20-%20%EB%8B%A4%EC%B8%B5%EC%8B%A0%EA%B2%BD%EB%A7%9D%20%EA%B8%B0%EC%B4%88.md)

{% raw %}
tags: #artificial-intelligence #machine-learning #neural-network #perceptron #activation #backpropagation

관련 노트: [Dimension Reduction: PCA와 LDA](16-dimension-reduction-pca-lda.md), [Neural Networks Part 2: Backpropagation](18-neural-networks-part-2-backpropagation.md)

## 핵심 요약

이 강의는 linear classifier의 한계를 출발점으로 neural network를 도입한다. Perceptron과 sigmoid neuron, hidden layer, activation function, XOR 문제, forward propagation과 backpropagation의 기본 아이디어를 다룬다.

## Linear Classifier의 한계

Linear classifier는 feature 공간에서 직선 또는 hyperplane decision boundary를 만든다.

```text
score(x) = w^T x
y_hat = sign(score(x))
```

데이터가 선형으로 분리되지 않으면 단순 linear classifier만으로는 좋은 boundary를 만들 수 없다. 원형이나 XOR 형태의 결정경계가 대표적인 예이다.

## Polynomial Feature와 Feature Transform

비선형 boundary를 만들기 위해 입력을 변환할 수 있다.

```text
model(x,w) = w0 + w1*x1^2 + w2*x2^2
```

이렇게 feature transform을 사람이 설계하면 linear model도 원형 boundary를 만들 수 있다. Neural network는 이러한 nonlinear feature를 데이터에서 학습하도록 확장한 모델로 볼 수 있다.

## Perceptron

Perceptron은 하나의 neuron으로 볼 수 있다.

```text
z = w0 + sum_j w_j*x[j]
output = g(z)
```

입력 edge는 feature 값을 전달하고, weight는 각 feature의 중요도를 나타낸다. `x[0]=1`로 두면 intercept를 weight로 포함할 수 있다.

## Sigmoid Neuron

Sigmoid neuron은 activation function으로 sigmoid를 사용한다.

```text
g(z) = 1 / (1 + exp(-z))
```

Sigmoid는 출력이 0과 1 사이여서 확률처럼 해석할 수 있고, 미분 가능하므로 gradient 기반 학습에 사용할 수 있다.

## Boolean Function과 XOR 문제

Perceptron은 AND, OR처럼 linearly separable한 Boolean function은 표현할 수 있다. 하지만 XOR는 선형 decision boundary 하나로 분리할 수 없다.

```text
XOR = (x1 AND NOT x2) OR (x2 AND NOT x1)
```

Hidden layer를 추가하면 중간 feature를 만들 수 있고, XOR 같은 비선형 함수를 표현할 수 있다.

## Hidden Layer

Hidden layer는 입력을 새로운 representation으로 바꾼다.

```text
h_k = g(w_k^T x)
output = g(v^T h)
```

각 hidden unit은 입력 공간에서 다른 feature detector처럼 동작한다. 여러 hidden unit을 조합하면 복잡한 decision boundary를 만들 수 있다.

## Activation Function의 중요성

Neural network에서 activation function은 반드시 nonlinear이어야 한다. 모든 layer가 linear activation만 사용하면 여러 linear transform을 곱한 결과도 결국 하나의 linear transform이기 때문이다.

대표 activation:

- Step function
- Sigmoid
- tanh
- ReLU

## General Neural Network

Neural network는 linear transformation과 nonlinear activation을 반복적으로 쌓은 구조이다.

```text
input -> linear -> activation -> linear -> activation -> output
```

Layer가 깊어질수록 입력에서 점점 더 추상적인 feature를 학습할 수 있다.

## Forward Propagation

Forward propagation은 입력에서 출력까지 값을 계산하는 과정이다.

1. Input layer에 feature를 넣는다.
2. 각 layer에서 weighted sum을 계산한다.
3. Activation function을 적용한다.
4. 마지막 layer에서 prediction을 만든다.

## Backpropagation의 기본 아이디어

Backpropagation은 loss에 대한 각 parameter의 gradient를 효율적으로 계산하는 알고리즘이다. 출력 layer에서 시작해 chain rule을 이용해 gradient를 뒤쪽 layer로 전달한다.

```text
forward pass: prediction 계산
backward pass: gradient 계산
gradient descent: weight update
```

## 시험ㆍ복습 체크포인트

- Linear classifier가 XOR를 표현하지 못하는 이유를 설명할 수 있어야 한다.
- Hidden layer가 feature transform 역할을 한다는 점을 이해해야 한다.
- Activation function이 nonlinear이어야 하는 이유를 말할 수 있어야 한다.
- Forward propagation과 backpropagation의 역할을 구분할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **17. Neural Networks Part 1: 다층신경망 기초**를 다루며, 데이터에서 일반화 가능한 예측 규칙을 학습하는 관점으로, 모델 가정-손실함수-최적화-평가가 한 묶음으로 이어진다.
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
- **17. Neural Networks Part 1: 다층신경망 기초**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [16. Dimension Reduction: PCA와 LDA](16-dimension-reduction-pca-lda.md) · 다음: [18. Neural Networks Part 2: Backpropagation](18-neural-networks-part-2-backpropagation.md)
