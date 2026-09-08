---
layout: page
title: "01. Lab03 — VGGNet & ResNet"
permalink: /studies/ai/deep-learning-experiment/01-lab03-vggnet-resnet/
sitemap: false
---

- **원본**: [GitHub — Deep Learning Experiment](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Deep_Learning_Experiment) · 사전보고서(14) + 본보고서(01) 통합·보강
- 이론 배경은 표준 자료(CS231n) 수준으로 보강. **실험 설정·결과 수치는 원 보고서 그대로**입니다.

{% raw %}
## 개요

CIFAR-10에서 plane / car / bird **3개 클래스**만 골라 VGG16과 ResNet50을 직접 구현·비교한다. 같은 전처리·같은 cross-entropy 아래에서 "그냥 깊은 망"과 "residual shortcut이 있는 망"이 어떻게 다르게 학습되는지 관찰하는 것이 목적.

## 1. 이론 배경 — CNN 발전사

| 모델 | 핵심 기여 | 한계 |
|---|---|---|
| **LeNet** | conv–pool–FC 기본 조합 | 소규모 문제용 |
| **AlexNet** | ReLU, dropout, GPU 학습, augmentation → ImageNet에서 CNN 우위 입증 | 큰 커널, 많은 파라미터 |
| **VGGNet** | 3×3 conv만 깊게 쌓는 단순 규칙(작은 커널 여러 개 = 큰 receptive field + 더 많은 비선형) | 파라미터·메모리 큼(FC층 무거움) |
| **GoogLeNet** | inception module(멀티 스케일 병렬) + 1×1 conv로 연산량 절감 | 구조 복잡 |
| **ResNet** | **residual connection**으로 depth **degradation**(깊을수록 train 성능도 악화) 해결 → 초深 네트워크 학습 가능 | — |

핵심: LeNet→ResNet은 단순한 "층 수 증가"가 아니라 **비선형·정규화·shortcut·멀티스케일 branch** 같은 *최적화를 가능하게 하는 장치*의 발전으로 읽어야 한다.

### 주요 식
$$
H_{\text{out}} = \left\lfloor \frac{H + 2P - K}{S} \right\rfloor + 1
$$
$$
\text{residual block: } \; y = F(x) + x
\quad(\text{채널·해상도 변화 시 shortcut에 } 1\times1 \text{ projection})
$$
ResNet의 shortcut = feature reuse + **gradient highway**. 깊은 모델 비교 시 파라미터 수보다 **gradient path 길이**를 함께 봐야 한다.

## 2. 실습 설계

- CIFAR-10에서 3개 클래스만 필터링 → **train 15,000 / test 3,000장**.
- train transform: `RandomCrop` + `RandomHorizontalFlip`(일반화 보강). test: `ToTensor` + 정규화만.
- **VGG16 (Type-D)**: 3×3 conv – BatchNorm – ReLU – MaxPool 반복, classifier를 3-class 출력으로 교체.
- **ResNet50**: 1×1–3×3–1×1 **bottleneck** block, downsampling 지점의 shortcut에도 1×1 projection.
- epoch마다 train loss / test loss / test accuracy 출력 → 수렴·과적합 신호 비교.

## 3. 구현 핵심

- VGG는 구현이 직관적이지만 깊어질수록 gradient 흐름이 불안정 → **conv 뒤 BatchNorm**으로 안정화.
- ResNet은 $F(x)+x$로 identity mapping 보존. 채널/해상도 변화 지점에서만 projection shortcut.
- `nn.CrossEntropyLoss`는 **softmax 이전 logits** + 정수 class index를 받는다 → 모델 마지막에 softmax를 붙이지 않는 것이 올바른 구현.

## 4. 결과

| 모델 | epoch | best test accuracy |
|---|---|---|
| VGG16 | 20 | **≈ 92.5 %** (7~13 epoch에 이미 90 %+ 포화) |
| ResNet50 | 15 | **≈ 85.7 %** |

- 이 설정(3-class, 작은 입력 32×32, 짧은 학습)에서는 **VGG가 더 높았다** — 더 복잡한 모델이 항상 유리한 건 아님.
- VGG에서 **BatchNorm을 제거하면** 정확도가 낮은 값에 정체 → 깊은 CNN 학습 안정성에 normalization이 결정적.

## 5. 해석 · 자주 틀리는 지점

- ResNet이 VGG보다 낮았던 건 **구조 우열이 아니라** 데이터 규모·epoch·augmentation·hyperparameter의 합작. 큰 데이터·긴 학습에서는 결론이 뒤집힐 수 있다.
- 원본 10-class가 아닌 **3-class**임을 metric 해석에 반영.
- shortcut projection의 stride·채널을 잘못 맞추면 residual addition에서 **shape mismatch**.
- train accuracy가 계속 올라도 **test loss가 증가**하면 과적합 신호.

## 복습 질문

- VGG의 3×3 stack이 큰 커널 하나보다 나은 두 가지 이유는?
- ResNet의 residual connection이 해결한 문제(degradation)와 그 메커니즘은?
- 이 실습에서 ResNet50이 VGG16보다 낮게 나온 이유를 구조가 아닌 요인으로 설명하라.
- `nn.CrossEntropyLoss` 사용 시 모델 출력에 softmax를 붙이면 안 되는 이유는?
{% endraw %}

---

다음: [02. Lab04 — Spatial Transformer Network](02-lab04-spatial-transformer-network.md)
