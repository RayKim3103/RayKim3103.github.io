---
layout: page
title: "13. CNN (합성곱 신경망)"
permalink: /studies/ai/machine-learning/13-convolutional-neural-networks/
sitemap: false
---

- **원본**: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence) · 강의 노트 `20` 정리·보강
- 표준 자료(CS231n) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

이미지는 숫자 행렬. hand-engineered feature(SIFT/HOG/BoW)의 한계 → **feature extractor 자체를 학습**. CNN은 **local connectivity + weight sharing**이라는 두 inductive bias로 이미지에 적합.

## 1. 역사적 배경

| 흐름 | 의미 |
|---|---|
| Visual cortex 연구 | receptive field, 계층적 시각 처리 |
| Neocognitron | simple/complex cell 구조 |
| LeNet | gradient-based learning을 문서 인식에 |
| AlexNet | ImageNet에서 deep CNN 성능 입증 |

## 2. Fully Connected의 문제

`32×32×3` 이미지를 3072차원 vector로 펴면 공간 구조 상실, 파라미터 폭증, 인접 pixel의 지역 구조 활용 불가.

## 3. 두 inductive bias

- **Local connectivity**: 한 neuron이 전체가 아니라 작은 patch(receptive field)만 본다. 가까운 pixel이 의미 있는 local pattern을 이루므로 자연스럽다.
- **Weight sharing**: 같은 filter를 전체 위치에 반복 적용. edge/texture detector는 어느 위치에서나 유용. → 파라미터↓, 위치 이동에 강한 feature, spatial structure 보존.

## 4. Convolution

$$
\text{out}[x,y] = \sum_i \sum_j \text{image}[x+i,\, y+j]\cdot \text{filter}[i,j]
$$
filter를 이미지 위로 sliding하며 weighted sum. filter 여러 개 → activation map 여러 개.

### Multiple channels
filter의 depth = input depth. 예: input `32×32×3`, filter `5×5×3` → 한 spatial 위치 출력은 scalar. **filter 개수 = output depth**.

## 5. Spatial Dimension

$$
W_{\text{out}} = \frac{W_{\text{in}} - F + 2P}{S} + 1
$$
($F$ filter size, $S$ stride, $P$ zero padding.)
padding 없이 반복하면 spatial size가 빠르게 줄어듦. zero padding은 border 보존·출력 크기 조절.

**예**: input `32×32×3`, filter `5×5×3`, $S{=}1$, $P{=}2$ → $W_{\text{out}} = (32-5+4)/1 + 1 = 32$ (유지). filter 10개 → 출력 `32×32×10`.

## 6. 구성 요소

- **1×1 conv**: spatial 유지, 채널 방향 선형결합(차원 축소/확장, feature mixing).
- CNN layer 스택: `CONV → ReLU → CONV → ReLU → … → FC → classifier`. 초기 layer는 edge/color, 깊은 layer는 object part/class feature.
- conv layer도 결국 neuron 배열이나, 각 neuron이 local receptive field만 보고 filter weight를 여러 위치에서 공유한다는 점이 다르다.

## 복습 질문

- CNN이 fully connected network보다 이미지에 적합한 이유(두 inductive bias)는?
- local connectivity와 weight sharing의 차이는?
- convolution 출력 spatial size 공식을 쓰고 예시로 계산하라.
- input channel 수와 filter depth가 같아야 하는 이유, filter 개수와 output depth의 관계는?
{% endraw %}

---

이전: [12. 신경망](12-neural-networks.md)
