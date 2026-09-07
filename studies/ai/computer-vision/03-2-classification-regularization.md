---
layout: page
title: "03. Classification Regularization"
permalink: /studies/ai/computer-vision/03-2-classification-regularization/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/03-2%20Classification%20Regularization.md)

{% raw %}
## 한눈에 보기
Classification 2는 모델 복잡도와 regularization을 중심으로 일반화 문제를 설명한다. feature가 많을 때 train data에 과도하게 맞는 현상과 이를 줄이는 방법을 다룬다.

## 핵심 개념
- underfitting
- overfitting
- L2 regularization
- L1 regularization
- ridge regression
- lasso
- bias variance

## 체계적 정리
- underfitting은 모델이 데이터의 기본 패턴도 설명하지 못하는 상태다.
- overfitting은 train set에는 잘 맞지만 test set에서 성능이 떨어지는 상태다.
- 더 많은 training data는 overfitting을 줄이는 가장 직접적인 방법이지만 항상 가능하지 않다.
- L2 regularization은 weight 크기를 부드럽게 줄여 큰 계수에 penalty를 준다.
- L1 regularization은 일부 weight를 0으로 만들어 feature selection 효과를 낼 수 있다.

## 중요한 수식과 관점
- Ridge objective: $\|y-Hw\|^2+\lambda\|w\|_2^2$.
- Lasso objective: $\|y-Hw\|^2+\lambda\|w\|_1$.
- $\lambda$가 커질수록 model variance는 줄고 bias는 커질 수 있다.

## 구현과 학습 포인트
딥러닝에서는 weight decay, dropout, data augmentation, early stopping이 같은 일반화 문제에 대응한다. 이 강의의 regularization 직관은 Neural Networks 1, 2의 학습 안정화 기법으로 이어진다.

## 추가 해석 포인트
- regularization은 모델의 표현력을 없애는 것이 아니라, 제한된 데이터에서 불필요하게 큰 weight나 불안정한 decision boundary가 생기는 것을 막는 장치다.
- train loss와 validation loss의 간격이 커질수록 overfitting 가능성이 크다. 이때 penalty를 키우거나 feature 수를 줄이거나 data augmentation을 늘리는 선택지를 비교할 수 있다.
- L1과 L2는 모두 penalty지만 효과가 다르다. L1은 sparse solution을 만들기 쉬워 feature selection에 가깝고, L2는 여러 feature의 weight를 부드럽게 줄이는 쪽에 가깝다.

## 자주 헷갈리는 지점
- L2는 weight를 작게 만들지만 보통 정확히 0으로 만들지는 않는다.
- regularization이 항상 성능을 올리는 것은 아니다. underfitting 상황에서는 penalty가 오히려 학습을 방해할 수 있다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Classification 통합](03-classification-overview.md)
- [Neural Networks](04-neural-networks.md)
- [Neural Networks 2](05-neural-networks-2.md)

{% endraw %}

---

이전: [03. Classification 통합](03-classification-overview.md) · 다음: [03. Classification Evaluation Metrics](03-3-classification-evaluation-metrics.md)
