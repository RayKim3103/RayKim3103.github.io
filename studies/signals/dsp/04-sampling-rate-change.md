---
layout: page
title: "04. Sampling Rate Change"
permalink: /studies/signals/dsp/04-sampling-rate-change/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/04%20Sampling%20Rate%20Change.md)

{% raw %}
﻿---
title: "04. Sampling Rate Change"
pages: 14
tags: [DSP, lecture-note, sampling, multirate]
---

# 04. Sampling Rate Change

> 이전: [Review of Signals and Systems](03-review-of-signals-and-systems.md)
> 다음: [LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md)

## 학습 목표

이 자료는 discrete-time processing으로 sampling rate를 바꾸는 방법을 다룬다.

- down-sampling/decimation
- anti-aliasing filter
- up-sampling/interpolation
- reconstruction/interpolation filter
- 비정수 배율 sampling rate 변환

## Sampling Rate Reduction

### Down-sampling

정수 $M$만큼 샘플링률을 낮추는 decimator는 다음과 같이 정의된다.

$$
y[n] = x[nM]
$$

즉 원래 수열에서 $M$번째 샘플만 남긴다.

### 주파수 영역 효과

down-sampling은 주파수축에서 spectrum을 $M$배 확장하고, $2\pi$ 주기 때문에 겹쳐 더해지는 효과를 만든다.

$$
Y(e^{j\omega})
= \frac{1}{M}\sum_{k=0}^{M-1}
X\left(e^{j(\omega-2\pi k)/M}\right)
$$

따라서 원 신호가 충분히 bandlimited가 아니면 aliasing이 발생한다.

## Anti-Aliasing Filter

decimation 전에 반드시 low-pass filtering을 해야 한다.

이상적인 조건:

$$
H_{aa}(e^{j\omega}) =
\begin{cases}
1, & |\omega|\le \pi/M \\
0, & \pi/M < |\omega| \le \pi
\end{cases}
$$

절차:

1. 원 신호 $x[n]$을 low-pass filter에 통과시킨다.
2. cutoff를 $\pi/M$ 이하로 제한한다.
3. 그 결과를 $M$배 down-sampling한다.

핵심은 decimation 자체가 aliasing을 없애지 못한다는 점이다. aliasing을 막는 역할은 앞단의 anti-aliasing filter가 담당한다.

## Increasing Sampling Rate

### Up-sampling

정수 $L$배 sampling rate를 높일 때는 샘플 사이에 $L-1$개의 0을 삽입한다.

$$
x_u[n] =
\begin{cases}
x[n/L], & n = 0,\pm L,\pm 2L,\ldots \\
0, & \text{otherwise}
\end{cases}
$$

### 주파수 영역 효과

zero insertion은 시간축을 늘리므로 주파수 영역에서는 spectrum image가 생긴다.

$$
X_u(e^{j\omega}) = X(e^{j\omega L})
$$

원 spectrum이 $L$개 image로 반복되어 보이므로, interpolation filter가 필요하다.

## Interpolation Filter

up-sampling 후 low-pass filter로 image를 제거한다.

이상적 interpolation filter:

$$
H_i(e^{j\omega}) =
\begin{cases}
L, & |\omega|\le \pi/L \\
0, & \pi/L < |\omega| \le \pi
\end{cases}
$$

gain이 $L$인 이유는 zero insertion으로 평균 에너지가 희석된 것을 보상하기 위해서이다.

## 비정수 배율 Sampling Rate 변환

샘플링률을 $L/M$배로 바꾸려면 다음 순서를 사용한다.

$$
x[n] \xrightarrow{\uparrow L}
x_u[n] \xrightarrow{\text{LPF}}
v[n] \xrightarrow{\downarrow M}
y[n]
$$

low-pass filter cutoff는 두 조건을 동시에 만족해야 한다.

$$
\omega_c = \min\left(\frac{\pi}{L}, \frac{\pi}{M}\right)
$$

실제 구현에서는 interpolation filter와 anti-aliasing filter를 하나의 low-pass filter로 합쳐 계산량을 줄인다.

## Simulation에서 관찰할 점

강의자료의 simulation 그림들은 다음을 보여준다.

- filtering 없이 down-sampling하면 spectrum이 겹쳐 aliasing이 생긴다.
- anti-aliasing filter를 먼저 적용하면 down-sampling 후에도 원래 저주파 성분이 보존된다.
- up-sampling 직후에는 샘플 사이에 0이 들어간 형태라 실제 interpolation된 부드러운 신호가 아니다.
- interpolation low-pass filter를 거쳐야 image spectrum이 제거되고 자연스러운 고샘플률 신호가 된다.

## 체크포인트

- decimation 전: anti-aliasing filter
- interpolation 후: anti-imaging filter
- down-sampling은 spectrum folding/aliasing 위험을 만든다.
- up-sampling은 spectrum image를 만든다.
- 비정수 변환은 항상 “먼저 up-sample, filter, 나중에 down-sample” 순서로 생각한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Sampling Rate Change**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
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
- **04. Sampling Rate Change**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. Review of Signals and Systems](03-review-of-signals-and-systems.md) · 다음: [05. LTI System to Linear Algebra](05-lti-system-to-linear-algebra.md)
