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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. Classification**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 분류 주제에서는 decision boundary, score/probability 변환, threshold 선택, class imbalance를 함께 보아야 한다.
- 정확도만 보지 말고 false positive와 false negative의 실제 비용을 문제 맥락에서 비교한다.
- 이미지 처리 연산은 필터 모양뿐 아니라 경계 조건, sampling, aliasing, scale 변화까지 함께 봐야 한다.
- 분류, 검출, 분할 모델은 architecture보다 입력 표현, loss, annotation 형식, metric의 차이가 중요하다.
- 3D vision에서는 좌표계, 카메라 모델, homogeneous coordinate, rank/scale ambiguity를 계속 추적해야 한다.

### 문제 풀이 또는 구현 루틴

- 문제를 2D appearance, 3D geometry, learning 문제 중 어디에 가까운지 먼저 분류한다.
- 수식이 나오면 coordinate frame과 tensor shape을 함께 적어 projection과 feature map 흐름을 놓치지 않는다.
- 실험 보고서는 qualitative image와 quantitative metric을 나란히 두고 failure case를 설명한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 시각적으로 좋아 보이는 결과가 metric상 좋은 결과와 항상 일치하지 않는다.
- resize, padding, normalization 같은 preprocessing 차이가 모델 성능을 크게 바꿀 수 있다.
- calibration과 two-view geometry에서는 scale이 정해지지 않는 값이 많아 절대 크기로 해석하면 안 된다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 방법이 조명, viewpoint, scale 변화에 어떻게 반응하는가?
- loss와 metric이 실제 원하는 시각 품질 또는 인식 성능을 잘 대변하는가?
- 실패 사례를 데이터 부족, 모델 capacity, 최적화, annotation noise 중 무엇으로 설명할 수 있는가?
- **03. Classification**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. Image Processing](02-image-processing.md) · 다음: [04. Neural Networks & CNNs](04-neural-networks-and-cnns.md)
