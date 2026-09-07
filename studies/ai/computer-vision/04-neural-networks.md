---
layout: page
title: "04. Neural Networks"
permalink: /studies/ai/computer-vision/04-neural-networks/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/04%20Neural%20Networks.md)

{% raw %}
## 한눈에 보기
Neural Networks 강의는 logistic regression에서 시작해 nonlinear neuron, feed-forward network, stochastic gradient descent, backpropagation의 기본 원리를 설명한다.

## 핵심 개념
- logistic regression
- sigmoid neuron
- activation function
- SGD
- backpropagation
- chain rule
- loss landscape

## 체계적 정리
- logistic regression은 linear score를 sigmoid 또는 softmax로 확률처럼 해석하는 기본 classifier다.
- neural network는 여러 layer와 activation function을 쌓아 nonlinear feature를 학습한다.
- activation function은 모델 표현력을 만든다. sigmoid, tanh, ReLU, SoftPlus, GELU 등이 쓰인다.
- SGD는 전체 데이터 대신 mini-batch gradient로 parameter를 반복 갱신한다.
- backpropagation은 chain rule을 이용해 loss gradient를 각 layer parameter로 효율적으로 전달한다.

## 중요한 수식과 관점
- Sigmoid: $\sigma(z)=1/(1+e^{-z})$.
- SGD update: $\theta\leftarrow\theta-\eta\nabla_\theta L$.
- Backpropagation 핵심: upstream gradient와 local gradient의 곱.

## 구현과 학습 포인트
CNN, transformer, generative model의 학습은 모두 이 기본 원리 위에서 돌아간다. 구현에서는 forward pass에서 필요한 중간 값을 저장하고, backward pass에서 gradient shape이 parameter shape과 맞는지 확인해야 한다.

## 자주 헷갈리는 지점
- activation 없이 linear layer만 여러 번 쌓으면 전체는 하나의 linear transform과 같다.
- learning rate가 너무 크면 발산하고, 너무 작으면 수렴이 느리다.
- backpropagation은 수식적으로 chain rule이지만 구현에서는 tensor shape과 broadcasting이 실수의 핵심 지점이다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Neural Networks 2](05-neural-networks-2.md)
- [Classification 통합](03-classification-overview.md)
- [ResNet 보고서](27-resnet-report.md)

{% endraw %}

---

이전: [03. Classification Evaluation Metrics](03-3-classification-evaluation-metrics.md) · 다음: [05. Neural Networks 2](05-neural-networks-2.md)
