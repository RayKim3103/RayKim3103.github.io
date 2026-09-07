---
layout: page
title: "05. LTI System to Linear Algebra"
permalink: /studies/signals/dsp/05-lti-system-to-linear-algebra/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/05%20LTI%20System%20to%20Linear%20Algebra.md)

{% raw %}
﻿---
title: "05. LTI System to Linear Algebra"
pages: 18
tags: [DSP, lecture-note, linear-algebra, convolution]
---

# 05. LTI System to Linear Algebra

> 이전: [Sampling Rate Change](04-sampling-rate-change.md)
> 다음: [Linear Algebra Inverse](06-linear-algebra-inverse.md)

## 학습 목표

이 강의는 convolution과 LTI 시스템을 선형대수의 행렬-벡터 형태로 바꾸는 방법을 다룬다.

- linear system equation
- lexicographical ordering
- Toeplitz matrix
- circulant matrix
- matrix squarization
- similarity, diagonalization
- DFT 행렬과 circulant matrix의 관계

## 왜 행렬로 바꾸는가

LTI 시스템은 시간 영역에서 convolution으로 표현된다.

$$
y[n] = h[n]*x[n]
$$

이를 벡터와 행렬로 쓰면

$$
\mathbf{y}=H\mathbf{x}
$$

가 된다. 이 표현은 다음 문제들을 같은 수학적 틀에서 다룰 수 있게 한다.

- filtering: $H$와 $\mathbf{x}$가 주어졌을 때 $\mathbf{y}$ 계산
- filter design: $\mathbf{x}$와 $\mathbf{y}$가 주어졌을 때 $H$ 추정
- inverse filtering: $H$와 $\mathbf{y}$가 주어졌을 때 $\mathbf{x}$ 복원
- blind deconvolution: $\mathbf{y}$만 주어지고 $H,\mathbf{x}$를 동시에 추정

## 1D Convolution의 Toeplitz Matrix 표현

FIR impulse response

$$
h[0],h[1],\ldots,h[M-1]
$$

와 입력

$$
\mathbf{x}=[x[0],x[1],\ldots,x[N-1]]^T
$$

가 있을 때 linear convolution은 다음과 같은 Toeplitz matrix로 표현된다.

$$
\begin{bmatrix}
y[0]\\
y[1]\\
y[2]\\
\vdots
\end{bmatrix}
=
\begin{bmatrix}
h[0] & 0 & 0 & \cdots\\
h[1] & h[0] & 0 & \cdots\\
h[2] & h[1] & h[0] & \cdots\\
\vdots & \vdots & \vdots & \ddots
\end{bmatrix}
\begin{bmatrix}
x[0]\\
x[1]\\
x[2]\\
\vdots
\end{bmatrix}
$$

Toeplitz matrix는 각 대각선 성분이 일정한 행렬이다. convolution의 shift-invariant 성질이 행렬의 반복 구조로 나타난 것이다.

## Lexicographical Ordering

2D 신호는 행렬 형태이지만 선형대수 처리를 위해 벡터로 펼쳐야 한다. 이때 일정한 순서로 pixel을 나열하는 방식을 lexicographical ordering이라고 한다.

예:

$$
X =
\begin{bmatrix}
x[0,0] & x[0,1]\\
x[1,0] & x[1,1]
\end{bmatrix}
\quad\rightarrow\quad
\mathbf{x}=[x[0,0],x[0,1],x[1,0],x[1,1]]^T
$$

2D convolution은 이 ordering을 통해 block Toeplitz 또는 block circulant matrix로 표현된다.

## Squarization

linear convolution matrix는 입력 길이와 출력 길이가 달라 non-square가 되는 경우가 많다. 역행렬이나 고유분해를 사용하려면 square matrix 형태가 필요할 수 있다.

Squarization은 zero padding 또는 boundary condition 설정을 통해 행렬을 정방행렬로 만드는 과정이다.

대표적 선택:

- zero boundary: 바깥 값을 0으로 가정
- periodic boundary: 신호가 주기적으로 반복된다고 가정

periodic boundary를 쓰면 convolution matrix가 circulant matrix가 된다.

## Circulant Matrix

Circulant matrix는 각 행이 이전 행의 circular shift로 구성되는 행렬이다.

예:

$$
C =
\begin{bmatrix}
c_0 & c_{N-1} & \cdots & c_1\\
c_1 & c_0 & \cdots & c_2\\
\vdots & \vdots & \ddots & \vdots\\
c_{N-1} & c_{N-2} & \cdots & c_0
\end{bmatrix}
$$

circulant matrix는 DFT 행렬로 대각화된다.

$$
C = W^{-1}\Lambda W
$$

여기서 $W$는 DFT matrix이고, $\Lambda$의 대각 성분은 impulse response의 DFT 값이다.

## Similarity와 Diagonalization

두 행렬 $A,B$가

$$
B = P^{-1}AP
$$

를 만족하면 similar하다고 한다. $A$가 충분한 eigenvector를 가지면

$$
A=PDP^{-1}
$$

로 대각화할 수 있다.

LTI/circulant 시스템에서 Fourier basis가 eigenvector 역할을 하므로, 주파수 영역에서는 convolution이 대각 행렬 곱으로 단순해진다.

## 핵심 연결

시간 영역:

$$
y[n]=h[n]*x[n]
$$

행렬 영역:

$$
\mathbf{y}=H\mathbf{x}
$$

주파수 영역:

$$
Y[k]=H[k]X[k]
$$

세 식은 같은 현상을 다른 표현으로 쓴 것이다.

## 체크포인트

- Toeplitz: linear convolution
- Circulant: circular convolution, periodic boundary
- DFT matrix: circulant matrix의 eigenvector 행렬
- convolution을 행렬로 쓰면 inverse problem과 least squares가 자연스럽게 등장한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. LTI System to Linear Algebra**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신호 주제에서는 시간영역 연산이 주파수/z-domain에서 어떻게 단순해지는지 변환쌍으로 연결한다.
- sampling과 DFT에서는 주기화, aliasing, zero padding을 분리해 생각해야 한다.
- 시간영역 convolution, 주파수영역 곱셈, z-domain pole-zero는 같은 LTI 시스템을 보는 세 가지 창이다.
- filter 설계에서는 magnitude response뿐 아니라 phase, stability, structure, coefficient quantization도 중요하다.
- sampling rate change와 FFT는 aliasing, circular convolution, zero padding 개념을 정확히 구분해야 한다.

### 문제 풀이 또는 구현 루틴

- impulse response, difference equation, transfer function, frequency response를 서로 변환한다.
- pole-zero plot으로 stability와 대략적인 frequency response를 먼저 예측한다.
- DFT/FFT 계산에서는 주기적 해석인지 linear convolution 해석인지 길이 조건을 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- DFT의 circular convolution을 linear convolution과 혼동하면 wrap-around aliasing이 생긴다.
- z-transform의 ROC를 빼면 causality와 stability 판단이 불완전하다.
- sampling rate를 바꿀 때 anti-aliasing/anti-imaging filter를 놓치기 쉽다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 시스템은 causal, stable, LTI 조건을 만족하는가?
- pole과 zero가 magnitude response의 어느 부분을 키우거나 줄이는가?
- finite-length 계산에서 boundary와 padding을 어떻게 처리했는가?
- **05. LTI System to Linear Algebra**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Sampling Rate Change](04-sampling-rate-change.md) · 다음: [06. Linear Algebra Inverse](06-linear-algebra-inverse.md)
