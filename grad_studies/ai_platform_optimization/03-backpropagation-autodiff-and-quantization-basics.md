---
layout: page
title: "03. 역전파·자동미분과 양자화 기초"
permalink: /grad_studies/ai_platform_optimization/03-backpropagation-autodiff-and-quantization-basics/
sitemap: false
---

- **강의**: 3. Deep Learning Overview (3)

{% raw %}
## 개요

이 장은 크게 두 부분이다. 전반부는 **역전파(backpropagation)와 자동미분(automatic differentiation)**이 계산 그래프 위에서 어떻게 동작하는지, 그리고 왜 시스템 관점에서 "자동미분이 더 낫다"고 하는지를 다룬다. 후반부는 **부동소수점 표현과 양자화(quantization)**의 기초 — FP32/FP16/BF16부터 PTQ/QAT, 그리고 실무에서 쓰이는 mixed-precision·sensitivity 기반 양자화까지 — 를 다룬다.

## Backpropagation

### Chain Rule과 계산 그래프

역전파는 **연쇄 법칙(chain rule)을 재귀적으로 적용해 그래디언트를 계산**하는 방법이다. 핵심은 "그래디언트 계산이 지역적(local) 연산이 된다"는 점이다.

$$
\frac{\partial J}{\partial x} = \frac{\partial J}{\partial z}\cdot\frac{\partial z}{\partial x}, \qquad z = f(x,y)
$$

각 연산 노드는 자신의 입력에 대한 국소 미분(local gradient)만 알면 되고, 상류(upstream)에서 전달된 그래디언트와 곱하기만 하면 된다 — 이 성질 덕분에 backprop은 임의로 복잡한 계산 그래프에 그대로 적용된다.

### 예제: 로지스틱 함수의 역전파

$$
f(w,x) = \frac{1}{1+e^{-(w_0x_0+w_1x_1+w_2)}}
$$

이 식을 `*`, `+`, `*(-1)`, `exp`, `+1`, `1/x` 등 원시 연산(primitive operation)들의 그래프로 분해하면, forward pass에서 각 노드의 값을 계산해 저장해두고, backward pass에서 노드를 역순으로 순회하며 각 노드의 local gradient를 곱해 나가면 전체 그래디언트를 구할 수 있다. 예를 들어 `f(x) = 1/x` 노드의 local gradient는:

$$
\frac{\partial f}{\partial x} = -\frac{1}{x^2}
$$

이런 식으로 `exp`, `+1`, `*(-1)` 노드를 차례로 거슬러 올라가며 `w_0, w_1, w_2`에 대한 그래디언트를 얻는다.

### Backprop의 시스템적 문제: 메모리

> **forward pass에서 계산한 중간값들은, 이후 backward pass에서 쓰일 수 있기 때문에 메모리에 계속 유지해야 한다.**

이 한 줄이 이후 Ch.6(메모리 최적화, rematerialization/checkpointing)의 출발점이 된다. 네트워크가 깊어질수록, 그리고 배치 크기가 커질수록 저장해야 할 중간 activation의 총량이 선형으로 증가한다.

### 수치미분(Numerical Differentiation) vs Backpropagation vs Automatic Differentiation

| 방식 | 특징 |
|---|---|
| **수치미분** | `(f(x+h)-f(x))/h` 방식으로 직접 근사. 구현 정확성을 검증하는 용도로만 쓰임(느리고 부정확) |
| **Backpropagation** | 이해·구현이 쉽지만, 메모리 사용과 스케줄 최적화 측면에서 비효율적 |
| **자동미분 (Autodiff)** | 전체 계산 그래프에 대한 그래디언트 계산 그래프를 **생성**한다. 시스템 최적화에 훨씬 유리 |

**왜 autodiff가 시스템 최적화에 더 유리한가?** Backprop은 "forward를 계산하고 나서 반대 방향으로 미분을 적용하는 절차"에 가깝지만, autodiff는 **그래디언트 계산 자체를 하나의 계산 그래프로 명시적으로 만들어낸다.** 이렇게 그래프로 표현되면 컴파일러가 이 그래프에 대해 연산 융합(operator fusion), 메모리 재사용(memory planning), 불필요한 연산 제거 같은 최적화를 적용할 수 있다 — 이것이 PyTorch 2.0의 AOTAutograd(Ch.4)가 택한 접근이다.

> 참고 자료: Stanford CS231n backpropagation notes, *Automatic Differentiation in Machine Learning: A Survey* (arXiv:1502.05767)

## Quantization

### 왜 양자화인가

학습된 모델의 가중치·activation은 기본적으로 FP32로 표현되지만, 추론 시에는 정밀도를 낮춰도 정확도 손실이 크지 않은 경우가 많다. 정밀도를 낮추면 **메모리 사용량, 메모리 대역폭, 연산 에너지**가 모두 줄어든다 — 특히 모델이 커질수록(LLM) 이 이득이 절대적으로 커진다.

### 부동소수점 표현 복습

정규화된 형태:

$$
\pm 1.x_1x_2\ldots_2 \times 2^{y_1y_2\ldots_2}
$$

| 포맷 | 부호 | 지수(exponent) | 가수(mantissa) | 비고 |
|---|---|---|---|---|
| FP32 (IEEE 754) | 1 bit | 8 bit (bias 127) | 23 bit | 표준 단정밀도 |
| FP16 (IEEE 754 half) | 1 bit | 5 bit (bias 15) | 10 bit | 표현 범위가 좁음 |
| **BF16 (bfloat16)** | 1 bit | **8 bit** (FP32와 동일 범위) | 7 bit | Google TPU, Intel Xeon/Nervana 등에서 사용 |

$$
value = (-1)^s \times (1+Significand) \times 2^{(Exponent-bias)}
$$

**FP16 vs BF16**: FP16은 가수(mantissa) 비트가 더 많아 같은 지수 범위 안에서 정밀도가 높지만, 지수 비트가 적어 **표현 가능한 값의 범위(dynamic range)가 좁다** — 학습 중 오버플로/언더플로가 발생하기 쉽다. BF16은 지수 비트를 FP32와 동일하게 8비트로 유지해 **범위는 FP32와 같이 넓게 유지하면서 정밀도만 낮춘** 포맷이다. "그래디언트처럼 값의 스케일 차이(dynamic range)가 큰 것이 중요한" 현대 모델의 학습에는 BF16이 더 적합하다고 여겨진다.

### 고정소수점(Fixed-Point) 표현

$$
y = s \cdot x + z
$$

여기서 `s`(scale)와 `z`(zero-point, bias)를 통해 정수 `x`를 실수 `y`로 매핑한다. 이 표현이 바로 아래 양자화 스킴의 기반이 된다.

### 양자화 스킴: Affine Quantization

$$
r = S(q - Z)
$$

- `r`: 실수(real) 값, `q`: 양자화된 정수 값
- `S`: scale, `Z`: zero-point

*"Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference"* (CVPR 2018)에서는 이 스킴을 DNN의 MAC(multiply-accumulate) 연산에 적용하는 방법을 제시한다. `W`(weight), `I`(input), `O`(output) 세 텐서에 대해:

$$
S_3(q_3^{i,k}-Z_3) = \sum_{j=1}^{N}\big(S_1(q_1^{i,j}-Z_1)\big)\big(S_2(q_2^{j,k}-Z_2)\big)
$$

이를 정리하면 결국 **정수 곱셈-누산(integer MAC)만으로 양자화된 GEMM을 계산**할 수 있음을 보인다:

$$
q_3^{i,k} = \frac{S_1 S_2}{S_3}\sum_{j=1}^{N} q_1^{i,j}\, q_2^{j,k}
$$

즉 부동소수점 연산 없이 정수 연산만으로 추론이 가능해진다 — 이것이 "8-bit inference"의 수학적 근거다(TensorRT의 8-bit inference 문서 참고).

### Threshold(Scale Factor) 선택

양자화 시 값의 범위를 어떻게 자를(saturate) 것인지가 정확도를 좌우한다.

| 방법 | 설명 |
|---|---|
| Min/Max 평균 | 샘플링된 배치들의 min/max 평균 사용 |
| 평균·표준편차 | 통계적 분포 기반 threshold |
| Calibration (TensorRT) | 대표 데이터셋으로 분포를 관측해 최적 threshold 탐색 |

### PTQ vs QAT

| 구분 | Post-Training Quantization (PTQ) | Quantization-Aware Training (QAT) |
|---|---|---|
| 적용 시점 | 학습이 끝난 모델에 사후 적용 | 학습 과정에서 "가짜 양자화(fake quant)"를 순전파에 삽입 |
| 정확도 | 손실이 상대적으로 큼 (calibration만으로는 부족한 경우多) | 손실이 최소화됨 — 양자화 오차를 손실 함수에 포함시켜 "양자화 친화적인" 최적점을 학습 |
| 구현 난이도 | 쉬움, 빠른 프로토타이핑에 적합 | 어려움, 재학습 필요 |
| 원리 | weight/activation 분포를 사후 관측해 scale/zero-point만 결정 | forward pass에서 quantize→dequantize를 시뮬레이션하고, backward에서는 float로 가중치를 갱신(straight-through estimator) |

> **직관**: QAT의 손실 함수는 `Loss = Original Loss + Quantization Error`로 볼 수 있다. 학습이 "양자화했을 때도 잘 동작하는" 가중치 공간의 지점을 찾아가도록 유도하는 것이다.

### Weight Quantization vs Activation Quantization

- **Weight**: 학습이 끝나면 고정된 값이므로 미리 양자화해두면 되고, 모델이 GPU/NPU에 상주하는 동안 항상 메모리를 차지하므로 **메모리 절감 효과가 가장 크다.** 실무에서는 보통 Weight quantization을 먼저 적용한다.
- **Activation**: 입력마다 값이 바뀌므로, calibration 데이터셋으로 분포를 분석해야 한다.

### Mixed-Precision Quantization

단일 정밀도(예: 전부 INT8 또는 전부 INT4)는 정확도-효율 트레이드오프가 최적이 아닌 경우가 많다. 레이어·채널·연산자(GEMM vs softmax vs layernorm)별로 양자화 민감도가 크게 다르기 때문이다.

- **Sensitivity Analysis**: 각 레이어가 양자화에 얼마나 민감한지 측정 (예: Hessian 기반 분석)
- 민감한 레이어는 높은 비트(FP16/INT8), 덜 민감한 레이어는 낮은 비트(INT4)를 할당하는 **per-layer / per-channel precision allocation**

#### HAWQ-V3: Dyadic Neural Network Quantization (ICML 2021)

하드웨어 인지형(hardware-aware) 완전 정수 전용(integer-only) 양자화 프레임워크:

- **Dyadic number**: scale factor를 `b / 2^c` 형태의 이진 유리수로 강제 → 나눗셈을 **정수 곱셈 + 비트 시프트**만으로 수행 가능 (FP32/나눗셈 완전 제거)
- **ILP 기반 mixed-precision**: Hessian 기반 민감도 + 모델 크기(BOPS)·지연시간 제약을 정수계획법(Integer Linear Programming)으로 풀어 레이어별 최적 비트폭을 자동 할당
- TVM 백엔드 위에 INT4/INT8 구현을 얹어 실제 하드웨어에서 검증

## 실무 심화: LLM 시대의 양자화 (강의 노트 기반)

강의 후반부(구술 노트)에서는 위 이론을 실무 LLM 서빙 상황에 어떻게 적용하는지를 다음과 같이 요약한다.

### Strength Reduction과 Activation Function 최적화

**Amdahl's Law**의 관점에서, Convolution이 이미 많이 최적화된 지금 시점에는 **다음 병목은 Activation Function**이다. Activation은 연산량(FLOPs) 자체는 적지만, `exp`, `sigmoid` 등은 **Strength**(한 연산이 걸리는 시간)가 매우 높다.

| 기법 | 설명 |
|---|---|
| **Strength Reduction** | 비싼 연산(곱셈, `exp`)을 싼 연산(덧셈, 시프트)으로 근사/치환 |
| **ReLU** | `max(0,x)` 하나로 압도적으로 빠름 — 비교·선택만 필요 |
| **고급 Activation (GELU, Swish)** | 정확도는 높지만 속도가 느림 → 정확도-속도 트레이드오프 |
| **LUT (Lookup Table)** | NPU에서 복잡한 activation을 미리 계산된 테이블 참조로 근사 → 속도↓↓, 메모리(테이블)↑, 정확도 약간↓ |

### KVCache Quantization과 Micro-scaling

LLM 추론에서는 Weight뿐 아니라 **KV Cache**(attention의 key/value 저장소)도 메모리를 크게 차지한다. 시퀀스가 길어질수록 KV Cache 크기가 선형으로 증가하기 때문에, 실무에서는 KV Cache 자체도 양자화(4bit/6bit)하는 것이 표준이 되고 있다.

- **4bit(INT4) 양자화의 문제**: outlier(극단값) 몇 개 때문에 전체 스케일이 왜곡되어 정확도가 급락할 수 있다.
- **Micro-scaling (MX 포맷, MSSP4 등)**: 텐서 전체가 아니라 **작은 블록 단위로 별도의 scale factor**를 두어 outlier의 영향을 국소화. 실질적으로 "4.3bit" 수준의 비정수 평균 비트로 outlier 문제와 압축률 사이의 균형을 맞춘다.

### Trade-off 종합표

| 기법 | 정확도 | 메모리 절감 | Latency | 난이도 | 권장 상황 |
|---|---|---|---|---|---|
| PTQ | ↓↓ | ↑↑ | ↑↑ | 쉬움 | 빠른 프로토타입 |
| QAT | ↓ (최소) | ↑↑ | ↑↑ | 어려움 | 정확도가 중요할 때 |
| Weight-only Quantization | ↓ | ↑↑ | ↑ | 중간 | 메모리 병목 시 (대부분의 경우) |
| Mixed Precision (layer-wise) | ↓ (작음) | ↑↑ | ↑↑ | 어려움 | LLM/대형 모델 |
| Micro-scaling (4bit 계열) | ↓ (최소) | ↑↑↑ | ↑ | 매우 어려움 | 4bit outlier 문제 해결 |

## 시험·복습 체크포인트

- Backprop과 Autodiff의 차이는 무엇이며, 왜 Autodiff가 시스템 최적화에 유리한가?
- FP16과 BF16의 비트 구성 차이는? 각각 어떤 상황에 적합한가?
- Affine quantization 스킴 `r = S(q-Z)`에서 `S`, `Z`의 의미와, 양자화된 GEMM이 왜 정수 연산만으로 가능한지 설명할 수 있는가?
- PTQ와 QAT의 근본적인 차이(적용 시점, loss에 반영되는지 여부)는?
- Mixed-precision quantization에서 Sensitivity Analysis가 하는 역할은?
- LLM에서 KV Cache quantization과 micro-scaling이 왜 필요한가?
{% endraw %}

---

이전: [02. CNN 아키텍처와 DNN 핵심 연산](02-cnn-architectures-and-dnn-core-operations.md) · 다음: [04. PyTorch 내부 구조와 PyTorch 2.x 컴파일 스택](04-pytorch-internals-and-pytorch2-compilation.md)
