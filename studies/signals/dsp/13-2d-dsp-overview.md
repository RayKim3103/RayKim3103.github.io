---
layout: page
title: "13. 2D DSP Overview"
permalink: /studies/signals/dsp/13-2d-dsp-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/13%202D%20DSP%20Overview.md)

{% raw %}
﻿---
title: "13. 2D Digital Signal Processing Overview"
pages: 13
tags: [DSP, lecture-note, 2D-DSP, image-processing]
---

# 13. 2D DSP Overview

> 이전: [DFT and FFT](12-dft-and-fft.md)

## 학습 목표

이 자료는 1차원 DSP 개념을 2차원 디지털 신호, 특히 영상 신호로 확장한다.

- 2D signal의 종류
- 2D digital image
- 2D system
- LSI system과 2D convolution
- DSFT
- 2D sampling/reconstruction
- image processing의 네 영역

## 2D Signals

### Analog 2D Signal

공간과 진폭이 모두 연속인 신호이다.

예:

- 필름 영상
- 실제 장면의 광 intensity
- 지진파 공간 분포
- radar/medical analog image

### Discrete-Space Signal

공간은 샘플링되어 이산이지만 진폭은 연속인 신호이다.

$$
x[n_1,n_2]
$$

analog image를 공간 sampling하면 얻어진다.

### Digital Signal

공간과 진폭이 모두 이산인 신호이다. 일반적인 디지털 이미지는 여기에 해당한다.

8-bit grayscale image:

$$
x[n_1,n_2]\in\{0,1,\ldots,255\}
$$

0은 가장 어두운 값, 255는 가장 밝은 값이다.

## 기본 2D 수열

### 2D Impulse

$$
\delta[n_1,n_2]=
\begin{cases}
1, & n_1=0,\ n_2=0\\
0, & \text{otherwise}
\end{cases}
$$

### Line Impulse

특정 행 또는 열 전체가 impulse처럼 활성화된 형태이다. 영상에서는 edge, scan line, projection을 설명할 때 유용하다.

### 2D Step

$$
u[n_1,n_2]=
\begin{cases}
1, & n_1\ge0,\ n_2\ge0\\
0, & \text{otherwise}
\end{cases}
$$

### Separable Sequence

2D 신호가 두 1D 신호의 곱으로 분해되면 separable이다.

$$
x[n_1,n_2]=x_1[n_1]x_2[n_2]
$$

separable filter는 계산량을 크게 줄일 수 있다.

## Digital Image와 Quantization

디지털 이미지는 pixel 또는 pel의 격자이다.

예:

- 512 x 512 pixels, 8 bits/pixel
- 256 gray levels

자료의 예시는 bit depth와 spatial resolution 변화가 영상 품질에 어떤 영향을 주는지 보여준다.

- bit 수 감소: intensity quantization artifact 증가
- pixel 수 감소: spatial detail 손실

## 2D System

2D 시스템은 입력 영상 $x[n_1,n_2]$를 출력 영상 $y[n_1,n_2]$로 mapping한다.

$$
y[n_1,n_2]=T\{x[n_1,n_2]\}
$$

## Linear and Shift-Invariant System

2D에서 LTI에 해당하는 개념을 LSI(linear shift-invariant) 또는 space-invariant system이라고 한다.

### Linearity

$$
T\{a x_1 + b x_2\}=aT\{x_1\}+bT\{x_2\}
$$

### Shift Invariance

입력이 공간적으로 shift되면 출력도 같은 양만큼 shift된다.

$$
x[n_1-m_1,n_2-m_2]
\rightarrow
y[n_1-m_1,n_2-m_2]
$$

## 2D Convolution

LSI 시스템은 impulse response 또는 PSF(point-spread function) $h[n_1,n_2]$로 완전히 결정된다.

$$
y[n_1,n_2]
=
\sum_{k_1=-\infty}^{\infty}
\sum_{k_2=-\infty}^{\infty}
x[k_1,k_2]h[n_1-k_1,n_2-k_2]
$$

축약:

$$
y=x*h
$$

영상 blur, sharpening, denoising filter는 모두 이 관점으로 표현할 수 있다.

## DSFT

2D discrete-space Fourier transform:

$$
X(e^{j\omega_1},e^{j\omega_2})
=
\sum_{n_1=-\infty}^{\infty}
\sum_{n_2=-\infty}^{\infty}
x[n_1,n_2]
e^{-j(\omega_1 n_1+\omega_2 n_2)}
$$

역변환:

$$
x[n_1,n_2]
=
\frac{1}{(2\pi)^2}
\int_{-\pi}^{\pi}\int_{-\pi}^{\pi}
X(e^{j\omega_1},e^{j\omega_2})
e^{j(\omega_1 n_1+\omega_2 n_2)}
d\omega_1d\omega_2
$$

2D LSI 시스템에서는

$$
Y(e^{j\omega_1},e^{j\omega_2})
=
H(e^{j\omega_1},e^{j\omega_2})X(e^{j\omega_1},e^{j\omega_2})
$$

가 된다.

## 2D Sampling과 Reconstruction

analog 2D signal $x_c(t_1,t_2)$를 sampling period $T_1,T_2$로 샘플링하면

$$
x[n_1,n_2]=x_c(n_1T_1,n_2T_2)
$$

각 축에서 Nyquist 조건을 만족해야 aliasing 없이 복원할 수 있다.

$$
\Omega_{s1}>2\Omega_{N1},
\qquad
\Omega_{s2}>2\Omega_{N2}
$$

2D에서는 한 축이라도 sampling rate가 부족하면 해당 방향으로 aliasing이 생긴다.

## Image Processing의 네 영역

| 영역 | 목적 | 예 |
|---|---|---|
| Enhancement | 사람이 보기 좋게 개선 | contrast enhancement, TV |
| Restoration | degradation 제거/감소 | deblurring, superresolution |
| Coding | 적은 bit로 표현 | JPEG, MPEG |
| Understanding | 의미/기호 추출 | computer vision, robotics, target identification |

## 체크포인트

- 1D의 time index가 2D에서는 spatial index $(n_1,n_2)$로 확장된다.
- LTI는 2D에서 LSI/space-invariant system이 된다.
- impulse response는 영상처리에서 PSF로 불린다.
- 2D convolution은 blur/filtering의 기본 모델이다.
- DSFT는 2D frequency 성분을 분석하는 도구이다.
- sampling/reconstruction과 aliasing 개념은 축별로 적용된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. 2D DSP Overview**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **13. 2D DSP Overview**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. DFT and FFT](12-dft-and-fft.md)
