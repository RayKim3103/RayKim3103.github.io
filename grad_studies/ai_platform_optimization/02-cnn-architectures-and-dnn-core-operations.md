---
layout: page
title: "02. CNN 아키텍처와 DNN 핵심 연산"
permalink: /grad_studies/ai_platform_optimization/02-cnn-architectures-and-dnn-core-operations/
sitemap: false
---

- **강의**: 2. Deep Learning Overview (2)

{% raw %}
## 개요

이 장은 두 부분으로 구성된다. 전반부는 **LeNet → AlexNet → GoogLeNet → ResNet**으로 이어지는 CNN 아키텍처의 역사를, 후반부는 **Convolution·Fully-Connected·Pooling·Batch Normalization** 등 DNN을 구성하는 핵심 연산을 시스템(하드웨어) 관점에서 정의한다. 이후 강의 노트에서는 각 아키텍처의 "정확도"보다 **"이 구조가 시스템에 어떤 부담(메모리·연산량)을 주는가"**에 초점을 맞춘다.

## Classic ML vs Deep Learning

```text
Classic ML:  Input → (사람이 설계한 Hand-designed Features) → Mapping → Output
Deep Learning: Input → (Simple Features) → (Abstract Feature Layers, 자동 학습) → Mapping → Output
```

딥러닝의 핵심은 "특징 추출(feature extraction)" 자체를 데이터로부터 학습한다는 것이다 — 이는 정확도 관점에서는 큰 도약이지만, 시스템 관점에서는 **레이어가 깊어질수록 저장해야 할 중간 activation이 폭증**한다는 대가를 동반한다 (→ Ch.6 메모리 최적화에서 다시 다룸).

## Cost Function과 Optimization

### 손실 함수와 정규화

$$
CF = MSE_{train} + \lambda \sum_i w_i^2 \quad (L2)
$$

$$
CF = MSE_{train} + \lambda \sum_i |w_i| \quad (L1)
$$

- **Weight decay(L2/L1 정규화)**: 작은 가중치를 선호하도록 유도해 과적합을 억제한다.
- **Dropout**: 학습 중 일부 노드를 임의로 0으로 만들어, 파라미터를 공유하는 거대한 앙상블 모델을 학습하는 효과를 낸다. (Srivastava et al., JMLR 2014)

### Gradient Descent 계열

- **Gradient Descent**: 목적 함수의 그래디언트 반대 방향으로 파라미터를 갱신해 손실을 최소화한다.
- **SGD (Stochastic Gradient Descent)**: 전체 데이터 대신 미니배치(minibatch)만으로 한 번의 업데이트를 수행 → 연산량 감소, local optimum 회피에도 유리.
- **SGD + Momentum**: 이전 업데이트 방향을 일부 유지(관성)해 수렴을 가속하고 진동(oscillation)을 억제한다.

> **시스템 관점 참고**: Momentum은 정확도를 높이지만 과거 gradient를 별도로 저장해야 하므로 **메모리 사용량이 증가**하고, 매 스텝 추가적인 읽기/쓰기가 필요해 스텝당 속도는 살짝 느려질 수 있다. 다만 전체 수렴에 필요한 epoch 수가 줄면 총 학습 시간은 오히려 짧아질 수 있다 — 이런 **"연산량 vs 메모리 vs 수렴 속도"** 3자 트레이드오프를 따지는 습관이 이 과목 전체에서 반복된다.

## CNN 아키텍처의 역사

| 모델 | 연도 | 핵심 기여 |
|---|---|---|
| **LeNet** (LeCun) | 1998 | Convolution + Max-pooling + Softmax의 기본 구조 확립 |
| **AlexNet** (Krizhevsky et al.) | 2012 | ReLU, Dropout, 대규모 데이터(ImageNet) |
| **GoogLeNet** (Szegedy et al.) | 2014 | 다중 병렬 경로(Inception), sparse weight matrix로 파라미터 절감 |
| **Inception-BN** (Ioffe et al.) | 2015 | Batch Normalization 도입 |
| **ResNet** (He et al.) | 2015 | Residual(skip) connection |

### LeNet 구조

```text
Convolution → Pooling → Flatten → Fully Connected → Softmax
```

### AlexNet: LeNet에서 무엇이 바뀌었나

- **더 많은 데이터**: ImageNet 규모로 확장
- **과적합 방지**: Dropout 정규화 도입
- **학습 안정성**: ReLU로 gradient vanishing/explosion 완화, 신중한 초기화·데이터 정규화 필요

### GoogLeNet: 다중 경로, 적은 파라미터

하나의 굵은 경로 대신 **여러 개의 독립적인 병렬 경로(Inception module)**를 두어 파라미터 수 대비 표현력을 높인다.

### ResNet: Residual Connection

변환 결과를 그대로 출력하는 대신, **입력에 변환 결과를 더한다**.

$$
y = F(x) + x
$$

이 구조는 gradient vanishing/explosion 문제를 부분적으로 해결해 매우 깊은 네트워크의 학습을 가능케 했다. 하지만 시스템 관점에서는 중요한 대가가 있다.

> **시스템 관점**: skip connection은 입력 `x`를 뒤쪽 레이어까지 그대로 들고 가야 하므로, **해당 activation을 backward pass까지 메모리에 유지**해야 한다. 이 메모리 비용은 **UNet(Encoder-Decoder + 모든 스케일의 feature map을 decoder까지 유지)**에서 극단적으로 커진다 — 실무에서 GPU 메모리가 부족해 CPU로 spill되며 속도가 크게 저하되는 대표적 원인이다.

## DNN 핵심 연산

```text
Convolution (+ 비선형 Activation) → Fully-Connected Layer → Pooling Layer → Batch Normalization
```

### 2-D / 3-D Convolution

파라미터 정의:

| 기호 | 의미 |
|---|---|
| `H, W` | 입력의 높이/너비 |
| `R, S` | 필터(weight)의 높이/너비 |
| `P, Q` | 출력의 높이/너비 |
| `C` | 입력 채널 수 |
| `K` | 출력 채널 수 (필터 개수) |
| `N` | 배치 크기 |

스트라이드(stride)와 패딩(padding)을 고려한 출력 크기:

$$
P = \frac{H - R + 2 \cdot pad}{stride} + 1, \qquad Q = \frac{W - S + 2 \cdot pad}{stride} + 1
$$

3차원 Convolution의 프로그램 형태 (7중 중첩 루프):

```c
for (n=0; n<N; n++) {
  for (k=0; k<K; k++) {              // 출력 채널마다
    for (p=0; p<P; p++) {
      for (q=0; q<Q; q++) {
        OA[n][k][p][q] = 0;
        for (r=0; r<R; r++) {
          for (s=0; s<S; s++) {      // convolution window
            for (c=0; c<C; c++) {
              h = p*stride - pad + r;
              w = q*stride - pad + s;
              OA[n][k][p][q] += IA[n][c][h][w] * W[k][c][r][s];
            }
          }
        }
        OA[n][k][p][q] = Activation(OA[n][k][p][q]);
      }
    }
  }
}
```

이 7중 루프의 **순서를 어떻게 두느냐(loop ordering), 어떻게 나누느냐(tiling), 메모리에 어떻게 배치하느냐(layout)**가 이후 Ch.5(컴파일러)·Ch.8(NPU)의 핵심 주제다. **Depth-wise Convolution**은 채널마다 독립적인 필터를 적용해 연산량을 크게 줄이는 변형이다(MobileNet 계열에서 널리 사용).

### Convolution을 실제로 계산하는 네 가지 방법

| 방법 | 원리 | 장점 | 단점 |
|---|---|---|---|
| **1. Direct (Sliding Window)** | 필터를 한 칸씩 이동하며 가중합을 직접 계산 | 직관적, 메모리 추가 사용 없음 | GPU 병렬성 활용이 어려움. CPU·작은 모델에 적합 |
| **2. im2col + GEMM** | 입력을 im2col로 펼쳐 행렬로 변환 후 행렬곱(GEMM)으로 계산 | GPU가 가장 선호하는 형태 → cuBLAS 등으로 최고 속도 | im2col 자체가 메모리 복사(memory copy overhead)이며, 펼쳐진 입력이 원본보다 최대 9배 가까이 커질 수 있음 |
| **3. FFT 기반** | 시간 영역의 convolution = 주파수 영역의 점별 곱셈 | 큰 필터에서 연산량 감소 | 작은(3×3) 필터에는 비효율적, FFT/IFFT 오버헤드 |
| **4. Winograd Transform** | 중간값을 재결합(re-association)해 곱셈 횟수 자체를 감소 | 3×3 필터에서 곱셈 6회→4회+덧셈 12회+시프트 2회로 연산량 실질 감소 | 하드웨어 의존적(곱셈이 느리고 덧셈/시프트가 빠른 하드웨어에서만 이득) |

> **"GEMM이 항상 빠르다"는 착각을 주의하라.** im2col의 메모리 복사(및 메모리 증폭)가 병목이 될 수 있으며, 모델이 커질수록(Llama 등 LLM) 연산량보다 **메모리 대역폭**이 병목이 되는 경우가 많다. Winograd는 진짜로 연산량 자체를 줄이는 고급 기법이지만 하드웨어 스펙에 매우 민감하다. 최신 GPU에서 FlashAttention과 함께 특정 조건에서 자동으로 선택되기도 한다.

### 비선형 Activation: ReLU

$$
y(x) = \max\{0, x\}
$$

- gradient vanishing 문제를 완화
- 계산이 매우 단순 (곱셈 없이 비교/선택만)

### Fully-Connected Layer

FC 레이어는 Convolution의 특수한 경우로 볼 수 있다 (`H=W=R=S=P=Q=1`). 배치 크기 `N`을 고려하면 행렬-벡터 곱이 행렬-행렬 곱(GEMM)이 된다.

```c
for (k=0; k<K; k++) {
  for (n=0; n<N; n++) {            // 출력 activation마다
    O[n,k] = 0;
    for (c=0; c<C; c++) {
      O[n,k] += W[k,c] * I[c,n];
    }
    O[n,k] = Activation(O[n,k]);
  }
}
```

### Pooling Layer

파라미터 개수·연산량을 줄여 과적합을 억제하는 다운샘플링 연산. 종류(MAX/AVG), 커널 크기, 스트라이드로 정의된다.

### Batch Normalization

각 레이어 입력을 평균 0, 분산 1로 정규화한다.

- 평균을 빼고 표준편차로 나눔 → 입력 스케일에 무관하게 출력이 동일 (scale invariance)
- 효과: 학습률(learning rate) 설정이 쉬워지고, 초기화에 덜 민감해짐 (Ioffe & Szegedy, 2015)

## 실무 트레이드오프 종합 (강의 노트 기반)

| 기법 | 정확도 영향 | 속도 영향 | 메모리 영향 | 핵심 트레이드오프 |
|---|---|---|---|---|
| Quantization (선행 예고, Ch.3) | ↓ (outlier 주의) | ↑↑ | ↓↓ | Calibration 필수 |
| Momentum | ↑ | 약간 ↓ | ↑ | 과거 gradient 저장 |
| Skip Connection (ResNet) | ↑↑ | – | ↑↑ | 중간 activation 장기 보관 |
| UNet 구조 | ↑ (segmentation) | – | ↑↑↑ (최악) | CPU로 spill될 위험 |
| im2col + GEMM | 동일 | ↑↑ (GPU) | ↑ | 메모리 vs 속도 |

이 표는 이후 Ch.6(메모리 최적화)에서 "이런 메모리 부담을 어떻게 줄일 것인가"라는 질문으로 이어진다.
{% endraw %}

---

이전: [01. AI와 딥러닝 시스템의 지형도](01-ai-and-deep-learning-landscape.md) · 다음: [03. 역전파·자동미분과 양자화 기초](03-backpropagation-autodiff-and-quantization-basics.md)
