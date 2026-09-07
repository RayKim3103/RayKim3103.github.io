---
layout: page
title: "10. Wiener Optimal Filter"
permalink: /studies/signals/dsp/10-wiener-optimal-filter/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/10%20Wiener%20Optimal%20Filter.md)

{% raw %}
﻿---
title: "10. Wiener Optimal Filter"
pages: 6
tags: [DSP, lecture-note, Wiener-filter, least-squares]
---

# 10. Wiener Optimal Filter

> 이전: [Structures and Parametric Modeling](09-structures-and-parametric-modeling.md)
> 다음: [Digital Filter Design](11-digital-filter-design.md)

## 학습 목표

이 자료는 noisy observation으로부터 원 신호를 추정하는 Wiener least squares filter를 다룬다.

- noisy observation model
- prediction/inverse filter
- mean square error 최소화
- orthogonality principle
- autocorrelation, cross-correlation, power spectrum
- Wiener filter의 주파수 영역 형태

## Observation Model

원 신호 $x[n]$가 시스템 $h[n]$를 거치고 잡음 $v[n]$가 더해져 관측 신호 $y[n]$가 된다고 하자.

$$
y[n]=h[n]*x[n]+v[n]
$$

주파수 영역:

$$
Y(e^{j\omega})=H(e^{j\omega})X(e^{j\omega})+V(e^{j\omega})
$$

목표는 $y[n]$로부터 $x[n]$를 추정하는 필터 $\hat{h}[n]$를 찾는 것이다.

$$
\hat{x}[n]=\hat{h}[n]*y[n]
$$

## Prediction Error

추정 오차:

$$
e[n]=x[n]-\hat{x}[n]
$$

Wiener filter는 평균제곱오차를 최소화한다.

$$
J=E\{|e[n]|^2\}
=E\{|x[n]-\hat{h}[n]*y[n]|^2\}
$$

## Orthogonality Principle

최적 추정에서는 오차가 관측 데이터의 모든 사용된 성분과 직교한다.

$$
E\{e[n]y^*[n-k]\}=0
$$

따라서

$$
E\{(x[n]-\hat{x}[n])y^*[n-k]\}=0
$$

이고, 이를 상관함수로 쓰면 Wiener-Hopf 방정식이 된다.

## Correlation 형태

cross-correlation:

$$
r_{xy}[k]=E\{x[n]y^*[n-k]\}
$$

autocorrelation:

$$
r_{yy}[k]=E\{y[n]y^*[n-k]\}
$$

최적 필터는

$$
r_{xy}[k]=\hat{h}[k]*r_{yy}[k]
$$

형태의 방정식을 만족한다.

## Frequency-Domain Wiener Filter

상관함수의 Fourier transform은 power spectrum이다.

$$
S_{xx}(e^{j\omega})=\mathcal{F}\{r_{xx}[k]\}
$$

일반적인 Wiener filter:

$$
\hat{H}(e^{j\omega})
=\frac{S_{xy}(e^{j\omega})}{S_{yy}(e^{j\omega})}
$$

관측 모델 $y=h*x+v$이고 $x$와 $v$가 uncorrelated이면

$$
S_{yy}=|H|^2S_{xx}+S_{vv}
$$

$$
S_{xy}=S_{xx}H^*
$$

따라서

$$
\hat{H}(e^{j\omega})
=
\frac{H^*(e^{j\omega})S_{xx}(e^{j\omega})}
{|H(e^{j\omega})|^2S_{xx}(e^{j\omega})+S_{vv}(e^{j\omega})}
$$

이는 inverse filter와 noise suppression이 결합된 형태이다.

## Least Squares Inverse와 비교

단순 inverse filter:

$$
\hat{X}=\frac{Y}{H}
$$

문제:

- $H$가 작아지는 주파수에서 noise가 크게 증폭된다.
- zero 또는 near-zero가 있으면 불안정하다.

Wiener filter:

$$
\hat{X}
=
\frac{H^*S_{xx}}{|H|^2S_{xx}+S_{vv}}Y
$$

잡음 전력이 큰 주파수에서는 gain을 낮춰 noise amplification을 억제한다.

## 구현 이슈

강의자료는 prediction inverse filter의 구현 문제를 강조한다.

- 실제로 $S_{xx}$와 $S_{vv}$를 정확히 모르는 경우가 많다.
- power spectrum을 추정해야 한다.
- filter가 causal/stable하게 구현 가능한지 확인해야 한다.
- frequency-domain 식을 시간 영역 FIR/IIR 구조로 근사해야 할 수 있다.

## 체크포인트

- Wiener filter는 MSE를 최소화하는 optimal linear filter이다.
- 핵심 원리는 “오차가 관측 공간과 직교한다”는 orthogonality principle이다.
- 단순 inverse와 달리 noise spectrum을 고려한다.
- $S_{vv}$가 0이면 inverse filter에 가까워지고, $S_{vv}$가 크면 해당 주파수 성분을 억제한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Wiener Optimal Filter**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
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
- **10. Wiener Optimal Filter**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. Structures and Parametric Modeling](09-structures-and-parametric-modeling.md) · 다음: [11. Digital Filter Design](11-digital-filter-design.md)
