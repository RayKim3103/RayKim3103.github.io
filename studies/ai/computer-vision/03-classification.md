---
layout: page
title: "03. Classification"
permalink: /studies/ai/computer-vision/03-classification/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `03` + `03-2(Regularization)` + `03-3(Evaluation Metrics)` 통합·보강
- 표준 CV/ML 교재 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

이미지 분류는 지도학습의 표준형이다. 이 노트는 (1) 선형 회귀→로지스틱/소프트맥스 분류로 이어지는 모델, (2) 일반화(generalization)와 정규화(regularization), bias–variance, (3) 불균형 데이터에서의 평가 지표를 다룬다. 여기서 잡은 언어가 신경망 파트로 그대로 이어진다.

## 1. 선형 모델에서 분류로

### Linear regression (기초)
$$
\hat y = w^\top x + b, \qquad
L(w) = \tfrac{1}{N}\lVert Xw - y \rVert_2^2
$$
닫힌 해 $w=(X^\top X)^{-1}X^\top y$, 또는 경사하강.

### Logistic regression (이진 분류)
$$
p(y{=}1\mid x) = \sigma(w^\top x + b),\qquad \sigma(z)=\frac{1}{1+e^{-z}}
$$
손실 = 음의 로그가능도(= binary cross-entropy):
$$
L = -\frac{1}{N}\sum_i \big[y_i\log p_i + (1-y_i)\log(1-p_i)\big]
$$

### Softmax classifier (다중 클래스)
$$
p_k = \frac{e^{s_k}}{\sum_j e^{s_j}},\quad s = Wx+b,\qquad
L = -\frac{1}{N}\sum_i \log p_{i,\,y_i}
$$
cross-entropy는 예측 분포 $p$와 정답 원-핫 $q$의 KL divergence를 최소화하는 것과 같다.

### 경사하강 (SGD)
$$
\theta \leftarrow \theta - \eta\,\nabla_\theta L_{\text{minibatch}}
$$
learning rate $\eta$, 미니배치 크기, epoch 수가 핵심 하이퍼파라미터.

## 2. 일반화와 정규화

### Underfitting vs Overfitting
- **underfitting**: 모델이 데이터의 기본 패턴조차 못 맞춤(train/test 모두 나쁨).
- **overfitting**: train은 잘 맞지만 test에서 나빠짐. train loss와 validation loss의 **간격**이 클수록 의심.

### Bias–Variance
$$
\mathbb{E}[(\hat f - y)^2] = \underbrace{\text{Bias}^2}_{\text{모델이 너무 단순}} + \underbrace{\text{Variance}}_{\text{데이터에 과민}} + \sigma^2_{\text{noise}}
$$
모델 복잡도를 키우면 bias↓ variance↑.

### Regularization
| 방법 | 목적함수 | 효과 |
|---|---|---|
| **L2 (ridge)** | $\lVert y - Hw\rVert^2 + \lambda\lVert w\rVert_2^2$ | 큰 계수에 penalty, weight를 **부드럽게** 축소(0으로는 잘 안 감) |
| **L1 (lasso)** | $\lVert y - Hw\rVert^2 + \lambda\lVert w\rVert_1$ | 일부 weight를 **정확히 0** → feature selection |

- $\lambda$↑ → variance↓, bias↑. $\lambda$는 validation으로 고른다.
- regularization은 표현력을 없애는 게 아니라, 적은 데이터에서 불필요하게 큰 weight·불안정한 결정경계가 생기는 것을 막는 장치.
- 딥러닝에서는 **weight decay(=L2), dropout, data augmentation, early stopping**이 같은 역할.
- 데이터를 더 모으는 것이 가장 직접적인 해법이지만 항상 불가능.

## 3. 평가 지표 (불균형 데이터)

정확도만 보면 위험하다(예: 99% 음성인 데이터에서 "전부 음성" 예측 → accuracy 99%).

**Confusion matrix** (이진):

|  | 예측 P | 예측 N |
|---|---|---|
| 실제 P | TP | FN |
| 실제 N | FP | TN |

$$
\text{Precision}=\frac{TP}{TP+FP},\quad
\text{Recall(=TPR)}=\frac{TP}{TP+FN},\quad
\text{Specificity}=\frac{TN}{TN+FP}
$$
$$
F_1 = \frac{2\,\text{P}\cdot\text{R}}{\text{P}+\text{R}}
$$

- **ROC curve**: 임계값을 쓸면서 (FPR, TPR) 궤적. AUC로 요약.
- **Precision–Recall curve**: 양성이 희소할 때 ROC보다 정보량이 크다.
- 다중 클래스는 macro/micro/weighted 평균으로 확장.

## 자주 틀리는 지점

- L2는 weight를 **작게** 만들지만 보통 정확히 0으로 만들지 않는다(sparse가 필요하면 L1).
- regularization이 **항상** 성능을 올리진 않는다 — underfitting 상황에서는 penalty가 학습을 방해.
- accuracy는 클래스 불균형에서 오해를 부른다 → precision/recall/F1/PR-curve로 본다.
- softmax의 로짓에 상수를 더해도 확률은 불변(shift-invariance) — 수치 안정화를 위해 max를 빼고 계산한다.

## 복습 질문

- logistic regression의 손실이 왜 MSE가 아니라 cross-entropy인가?
- $\lambda$를 키우면 bias/variance는 각각 어떻게 되는가? L1과 L2의 결과 차이는?
- 양성 클래스가 1%인 데이터에서 accuracy 대신 무엇을 봐야 하며 왜인가?
- cross-entropy와 KL divergence의 관계를 설명하라.

{% endraw %}

---

이전: [02. Image Processing](02-image-processing.md) · 다음: [04. Neural Networks & CNNs](04-neural-networks-and-cnns.md)
