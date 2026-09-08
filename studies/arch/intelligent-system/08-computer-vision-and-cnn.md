---
layout: page
title: "08. Computer Vision과 CNN"
permalink: /studies/arch/intelligent-system/08-computer-vision-and-cnn/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `10` 정리·보강
- 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

이미지 필터링 → AI/ML/DL → perceptron/MLP → **CNN**의 기본 구조. 다음 강의(AI hardware)의 이론 배경.

## 1. Image와 Filtering

2D digital image = 공간 sampling + quantization 결과. grayscale(pixel = intensity 1개), RGB(pixel = `[R,G,B]`), intensity 보통 1 byte(0–255). 수학적으로 $I(x,y)$.

이미지 필터링: kernel(mask)을 image 위로 이동하며 주변 pixel과 가중합. **딥러닝은 엄밀한 convolution 대신 cross-correlation**을 쓴다(kernel weight가 학습되므로 뒤집을 필요 없음):
$$
Y[i,j] = \sum_m \sum_n X[i+m,\, j+n]\, K[m,n]
$$

| 필터 | 효과 |
|---|---|
| identity | 원본 유지 |
| mean/blur | smoothing (low-pass) |
| sharpening | high-frequency 강조 |
| **Gaussian** | 부드러운 low-pass, $G_\sigma(x,y) = \dfrac{1}{2\pi\sigma^2} e^{-(x^2+y^2)/2\sigma^2}$ ($\sigma$↑ → 더 넓게 blur) |

## 2. Edge & Gradient

edge = intensity 급변 위치. digital 미분 = finite difference:
$$
\frac{\partial I}{\partial x} \approx I[x+1,y] - I[x,y],\qquad
\nabla I = \Big[\tfrac{\partial I}{\partial x},\ \tfrac{\partial I}{\partial y}\Big]
$$
$$
\lVert \nabla I \rVert = \sqrt{(\partial I/\partial x)^2 + (\partial I/\partial y)^2},\qquad
\theta = \tan^{-1}\!\Big(\frac{\partial I/\partial y}{\partial I/\partial x}\Big)
$$

## 3. AI / ML / DL, Perceptron

| | 의미 | 예 |
|---|---|---|
| AI | 인간의 지능적 행동 수행 | knowledge representation, fuzzy logic |
| ML | data에서 pattern 학습해 예측 | decision tree, clustering |
| DL | neural network 기반 ML | deep NN |

**Artificial neuron**: input → weight → weighted sum → bias → activation.
$$
u = \sum_i w_i x_i,\qquad y = \phi(u + b)
$$
bias는 affine transformation을 가능하게.

- **Single-layer perceptron**: linearly separable만 (AND/OR ✓, **XOR ✗**).
- **MLP**: hidden layer가 입력 공간을 변환 → XOR 같은 nonlinear 문제 해결. hidden layer = feature extractor.
- **Deep NN**: `raw pattern → learned feature extractor + classifier → label`. 초기 layer = edge/corner, 깊은 layer = object part/semantic.

## 4. MLP for Images와 그 한계

이미지를 MLP에 넣으려면 2D → 1D **flatten** (MNIST: 28×28 → 784 input, 0–255 → 0–1 normalize).

**한계**:
1. flatten에서 **spatial relationship 손실**
2. high-res에서 fully-connected weight 폭증 (MNIST 784 vs Full HD 2,073,600 input neurons)
3. 같은 pattern이 위치만 바뀌어도 별도 weight 필요

## 5. CNN

**locally-connected layer + weight sharing**: 작은 local receptive field만 봄, 동일 kernel을 모든 위치 공유, 공간 구조 보존.

```text
Convolution → Activation → Pooling → ... → Fully Connected → Output
```
- **Convolution**: learnable kernel로 local pattern 검출, multi input/output channel.
- **Activation**: 비선형성 (ReLU).
- **Pooling**: spatial dim 축소 (max / average) → 위치 변화 민감도↓.

### Equivariance vs Invariance
- **equivariance**: 입력 shift → 출력 feature도 같은 방식 shift (주로 convolution).
- **invariance**: object 위치가 달라도 같은 class (pooling·deeper layer).

### Padding / Stride
- **padding**: 입력 주변에 0 추가 → output size 조절, edge 정보 손실 완화.
- **stride**: kernel 이동 간격. stride↑ → output spatial size↓, 계산량↓, down-sampling.

### Multi-channel Convolution
input channel $C$개 → kernel도 $C$ channel:
$$
Y_o[i,j] = \sum_{c=0}^{C-1} \sum_m \sum_n X_c[i+m,\, j+n]\, K_{o,c}[m,n]
$$
output channel이 여러 개면 서로 다른 filter set이 여러 feature map을 만든다.

## 복습 질문

- 딥러닝 convolution layer가 실제로는 cross-correlation인 이유는?
- edge를 gradient로 측정하는 식과, edge strength·direction은?
- MLP로 이미지를 처리할 때의 세 가지 한계와, CNN이 이를 어떻게 해결하는가?
- translation equivariance와 invariance의 차이, 그리고 각각 어느 layer가 제공하는가?
{% endraw %}

---

이전: [07. PS/PL · AXI · PYNQ · ILA](07-ps-pl-axi-pynq-and-ila.md) · 다음: [09. 딥러닝 하드웨어와 Quantization](09-deep-learning-hardware-and-quantization.md)
