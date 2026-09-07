---
layout: page
title: "04. Neural Networks & CNNs"
permalink: /studies/ai/computer-vision/04-neural-networks-and-cnns/
sitemap: false
---

- **원본**: [GitHub — Computer Vision](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Vision) · 강의 노트 `04(Neural Networks)` + `05(Neural Networks 2)` 통합·보강
- 표준 CV 교재(CS231n) 수준으로 재작성. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

전반부는 로지스틱 회귀 → 비선형 뉴런 → 다층 퍼셉트론(MLP)과 **역전파**, 후반부는 **convolution layer**, **normalization**, 대표 architecture(**VGG, ResNet**)를 다룬다. 핵심 질문은 "깊은 네트워크를 어떻게 **안정적으로** 학습시키는가".

## 1. 뉴런과 다층 신경망

로지스틱 회귀는 1층 신경망($\sigma(w^\top x)$). 층을 쌓고 비선형 활성화를 끼우면 표현력이 커진다.

$$
h^{(l)} = \phi\!\big(W^{(l)} h^{(l-1)} + b^{(l)}\big),\qquad h^{(0)}=x
$$

### 활성화 함수
| | 식 | 특징 |
|---|---|---|
| sigmoid | $1/(1+e^{-z})$ | 포화 시 gradient 소실 |
| tanh | $(e^z-e^{-z})/(e^z+e^{-z})$ | 0 중심, 여전히 포화 |
| **ReLU** | $\max(0,z)$ | 계산 싸고 gradient 소실 완화, "dead ReLU" 주의 |
| Leaky/GELU | — | dead 문제 완화 |

비선형이 없으면 층을 쌓아도 하나의 선형변환과 같다.

## 2. 역전파 (backpropagation)

연쇄법칙으로 손실의 gradient를 출력층에서 입력층으로 전파한다.

$$
\frac{\partial L}{\partial W^{(l)}}
= \delta^{(l)} \, {h^{(l-1)}}^{\top},\qquad
\delta^{(l)} = \big({W^{(l+1)}}^{\top}\delta^{(l+1)}\big)\odot \phi'\!\big(z^{(l)}\big)
$$

- forward에서 중간값을 저장 → backward에서 재사용(메모리 ↔ 재계산 trade-off).
- SGD + momentum / Adam으로 파라미터 갱신. loss landscape는 비볼록이라 초기화·learning rate·정규화가 중요.

## 3. Convolution layer

이미지에 **완전연결**을 쓰면 파라미터가 폭발하고 위치 정보가 깨진다. CNN은 두 가지 inductive bias를 넣는다.

- **local connectivity** — 뉴런이 작은 receptive field만 본다.
- **weight sharing** — 같은 커널을 전체 위치에 슬라이드 → translation equivariance, 파라미터 급감.

### 출력 크기
입력 $W$, 커널 $K$, padding $P$, stride $S$:
$$
W_{\text{out}} = \left\lfloor \frac{W - K + 2P}{S} \right\rfloor + 1
$$
채널: 커널은 $C_{\text{in}}\times K\times K$, 출력 채널 수만큼 커널이 있다.

### 파라미터 수
$$
\#\text{params} = C_{\text{out}}\,(C_{\text{in}} K_h K_w) \;(+\,C_{\text{out}}\ \text{bias})
$$

- **pooling**(max/avg): 공간 해상도를 줄이고 국소 이동에 둔감하게. 최근에는 stride conv로 대체하기도.
- **1×1 conv**: 채널 방향 선형결합(차원 축소/확장).
- receptive field는 층이 깊어질수록 커진다.

## 4. Normalization

내부 activation 분포를 정규화해 학습을 쉽게 만든다.

$$
\hat x = \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}},\qquad y = \gamma\,\hat x + \beta
$$
$\gamma,\beta$는 채널마다 학습되는 scale/shift.

| | 통계 계산 축 | 배치 의존 |
|---|---|---|
| **BatchNorm** | (N,H,W) per-channel | O — 작은 배치에 취약 |
| **LayerNorm** | (C,H,W) per-sample | X — Transformer 표준 |
| **InstanceNorm** | (H,W) per-sample per-channel | X — style transfer |
| GroupNorm | 채널 그룹 | X |

- **BN의 이점**: 큰 learning rate 허용, 초기화 민감도 감소, 약한 정규화 효과.
- **train/eval 차이**: train은 미니배치 통계, eval은 학습 중 누적한 **running mean/var**. running stat은 파라미터가 아니지만 eval 성능에 직접 영향 → `model.eval()` 필수.

## 5. 대표 architecture

- **VGG**: 3×3 conv만 쌓아 단순·규칙적. 파라미터·연산량이 크다(FC층이 무겁다).
- **ResNet**: **residual block** $y = x + F(x)$. skip connection이 gradient를 직통으로 흘려 **아주 깊은 네트워크**(50/101/152층)를 학습 가능하게 함. 채널/해상도가 바뀌는 지점은 1×1 conv로 shortcut을 맞춘다.
  - "identity를 배우기 쉽게" 만들어 degradation 문제(깊을수록 train 성능도 나빠지던 현상)를 해결.

→ 실제 파라미터 계산·학습 설정은 [27. ResNet (project)](27-resnet-project.md).

## 자주 틀리는 지점

- 활성화 없이 층만 쌓으면 = 선형 모델. 비선형이 표현력의 원천.
- conv 출력 크기 공식에서 stride·padding·dilation을 빠뜨리기 쉽다.
- BatchNorm을 `eval()`로 안 바꾸면 추론이 배치 구성에 따라 흔들린다.
- residual은 "정보를 더한다"가 아니라 "잔차 $F(x)$만 학습하게 해 최적화를 쉽게" 하는 것.

## 복습 질문

- 역전파에서 $\delta^{(l)}$의 재귀식을 유도하고, forward 중간값을 왜 저장하는지 설명하라.
- CNN의 두 inductive bias와 그 효과(파라미터 수, equivariance)는?
- BatchNorm의 train/eval 동작 차이와 running statistics의 역할은?
- ResNet의 residual connection이 해결한 문제와 그 메커니즘은?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Neural Networks & CNNs**를 다루며, 픽셀 배열을 기하, 확률, 최적화, 딥러닝 표현으로 바꾸어 장면과 객체를 이해하는 과목이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신경망 주제에서는 activation shape, parameter count, gradient path, normalization 위치를 함께 추적한다.
- 깊은 모델의 성능은 architecture뿐 아니라 initialization, optimizer, learning rate schedule, augmentation에 민감하다.
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
- **04. Neural Networks & CNNs**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. Classification](03-classification.md) · 다음: [06. Detection & Segmentation](06-detection-and-segmentation.md)
