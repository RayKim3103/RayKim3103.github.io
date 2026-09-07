---
layout: page
title: "03. Classification 통합"
permalink: /studies/ai/computer-vision/03-classification-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/03%20Classification%20%ED%86%B5%ED%95%A9.md)

{% raw %}
## 한눈에 보기
Classification 강의의 큰 흐름을 다루는 자료이다. supervised learning의 기본 형식, linear regression, regularization, gradient descent, logistic/softmax classifier, evaluation의 토대를 잡는다.

## 핵심 개념
- supervised learning
- linear regression
- logistic regression
- regularization
- gradient descent
- generalization
- classification metrics

## 체계적 정리
- supervised learning은 input $X$와 label $Y$의 관계를 학습해 unseen data에 일반화하는 문제다.
- linear regression은 continuous output을 예측하지만, classification 학습의 optimization과 regularization 설명에도 기초가 된다.
- 모델이 너무 단순하면 underfitting, 너무 복잡하고 데이터가 부족하면 overfitting이 발생한다.
- regularization은 parameter 크기나 구조에 penalty를 주어 generalization을 높인다.
- classification에서는 score, probability, decision boundary, loss function을 구분해야 한다.

## 중요한 수식과 관점
- Least squares: $\|y-Hw\|^2$.
- Ridge: $\|y-Hw\|^2+\lambda w^Tw$.
- Gradient descent: $\theta_{t+1}=\theta_t-\eta\nabla J(\theta_t)$.

## 구현과 학습 포인트
이 자료는 neural network 이전의 기계학습 기반을 제공한다. CNN도 결국 feature extractor와 classifier head를 학습하는 구조이므로, overfitting과 regularization 개념은 뒤쪽 모든 모델에 반복해서 등장한다.

## 자주 헷갈리는 지점
- training error가 낮아도 test error가 높으면 모델이 문제를 잘 푼 것이 아니라 train set을 외운 것일 수 있다.
- linear model의 한계는 feature transform 또는 deep representation learning으로 보완한다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Classification Regularization](03-2-classification-regularization.md)
- [Classification Evaluation Metrics](03-3-classification-evaluation-metrics.md)
- [Neural Networks](04-neural-networks.md)

{% endraw %}

---

이전: [02. 보충 Resampling과 Gaussian Pyramid](02-2-supplement-resampling-and-gaussian-pyramid.md) · 다음: [03. Classification Regularization](03-2-classification-regularization.md)
