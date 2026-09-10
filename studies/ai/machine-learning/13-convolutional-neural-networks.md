---
layout: page
title: "13. CNN (합성곱 신경망)"
permalink: /studies/ai/machine-learning/13-convolutional-neural-networks/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `20` 정리·보강

{% raw %}
## 개요

이미지는 숫자 행렬. hand-engineered feature(SIFT/HOG/BoW)의 한계 → **feature extractor 자체를 학습**. CNN은 **local connectivity + weight sharing**이라는 두 inductive bias로 이미지에 적합.

## 1. 역사적 배경

| 흐름 | 의미 |
|---|---|
| Hubel & Wiesel — visual cortex | receptive field, simple/complex cell, 계층적 시각 처리 |
| Neocognitron (Fukushima, 1980) | simple/complex cell 구조 |
| LeNet-5 (LeCun, 1998) | gradient-based CNN을 우편번호 인식에 |
| AlexNet (2012) | ImageNet top-5 error 26% → 16%, ReLU·dropout·GPU |
| VGG / GoogLeNet / **ResNet(2015)** | 깊이 경쟁, residual connection으로 100+층 |

## 2. Fully Connected의 문제

`32×32×3` 이미지를 3072차원 vector로 펴면: **공간 구조 상실**, 첫 layer가 `3072 × H` 파라미터로 폭증, 인접 pixel의 지역 구조·평행이동 불변성 활용 불가.

## 3. 두 inductive bias

- **Local connectivity**: 한 neuron이 전체가 아니라 작은 patch(**receptive field**)만 본다. 가까운 pixel이 의미 있는 local pattern(edge, corner, texture)을 이룸. 깊은 층일수록 (여러 conv·pooling 누적으로) **effective receptive field**가 커져 큰 구조를 포착.
- **Weight sharing**: 같은 filter를 모든 위치에 반복 적용. edge/texture detector는 어느 위치에서나 유용. → 파라미터↓, **translation equivariance**(입력이 평행이동하면 activation map도 같이 이동).

## 4. Convolution

$$
\text{out}[x,y] = \sum_i \sum_j \text{image}[x+i,\, y+j]\cdot \text{filter}[i,j] \;(+\,b)
$$
(엄밀히는 cross-correlation.) filter를 이미지 위로 sliding하며 weighted sum → 한 filter가 하나의 **activation map(feature map)**.

### Multiple channels
filter의 depth = **input depth**. 예: input `H×W×3`, filter `5×5×3` → 한 spatial 위치에서 $$5\cdot5\cdot3 = 75$$개 곱의 합 = scalar. **filter 개수 $$K$$ = output depth**. 한 conv layer 파라미터 수 = $$(F\cdot F\cdot C_{in} + 1)\cdot K$$ — 입력 크기 $$H, W$$와 무관(FC와의 결정적 차이).

## 5. Spatial Dimension

$$
W_{\text{out}} = \left\lfloor\frac{W_{\text{in}} - F + 2P}{S}\right\rfloor + 1
$$
($$F$$ filter size, $$S$$ stride, $$P$$ zero padding.)
padding 없이 반복하면 spatial size가 빠르게 줄어듦(테두리 정보 손실). **"same" padding** $$P = (F-1)/2$$ ($$S=1$$)이면 크기 유지.

**예**: input `32×32×3`, filter `5×5×3`, $$S{=}1$$, $$P{=}2$$ → $$W_{\text{out}} = (32-5+4)/1 + 1 = 32$$ (유지). filter 10개 → 출력 `32×32×10`, 파라미터 $$(5\cdot5\cdot3+1)\cdot10 = 760$$개.

## 6. 구성 요소

- **Pooling** (max / average): 보통 $$2\times2$$, $$S{=}2$$ → spatial 1/2, 파라미터 없음. 국소 평행이동 **불변성** 부여, 연산량↓. (max pooling backward: argmax 위치로만 gradient.)
- **1×1 conv**: spatial 유지, 채널 방향 선형결합 → 차원 축소/확장, feature mixing, 비선형(ReLU와 함께) 추가. 파라미터 $$(1\cdot1\cdot C_{in} + 1)\cdot K$$.
- **Stride $$>1$$ conv**: pooling 대신 다운샘플링.
- CNN 스택: `[CONV → ReLU → (CONV → ReLU) → POOL] × n → FC → softmax`. 초기 layer는 edge/color, 깊은 layer는 object part/class-level feature (feature hierarchy).
- **im2col**: conv를 큰 행렬곱으로 펼쳐 BLAS로 가속.
- **equivariance vs invariance**: conv는 평행이동 **equivariant**(map이 같이 이동), pooling·global average pooling·augmentation이 **invariance**를 만든다.

## 7. FC와의 비교

| | Fully Connected | Convolution |
|---|---|---|
| 연결 | 모든 입력 ↔ 모든 출력 | local patch만 |
| weight | 위치마다 독립 | 위치 간 공유 |
| 파라미터 | 입력 크기에 비례 | filter 크기·개수에만 의존 |
| 구조 활용 | 없음 | 지역성·평행이동 |

## 복습 질문

- CNN이 fully connected network보다 이미지에 적합한 이유(두 inductive bias)와, 파라미터 수가 입력 크기와 무관한 이유는?
- local connectivity와 weight sharing의 차이, effective receptive field가 깊이에 따라 커지는 이유는?
- convolution 출력 spatial size 공식을 쓰고 `32×32×3`, `5×5`, $$S{=}1$$, $$P{=}2$$, 10 filter 예를 계산(출력 shape + 파라미터 수)하라.
- input channel 수와 filter depth가 같아야 하는 이유, filter 개수와 output depth의 관계는?
- pooling과 1×1 conv 각각의 목적, "equivariance vs invariance"의 차이는?
{% endraw %}

---

이전: [12. 신경망](12-neural-networks.md)
