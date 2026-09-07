---
layout: page
title: "11. Digital Filter Design"
permalink: /studies/signals/dsp/11-digital-filter-design/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/11%20Digital%20Filter%20Design.md)

{% raw %}
﻿---
title: "11. Digital Filter Design"
pages: 18
tags: [DSP, lecture-note, filter-design, FIR, IIR]
---

# 11. Digital Filter Design

> 이전: [Wiener Optimal Filter](10-wiener-optimal-filter.md)
> 다음: [DFT and FFT](12-dft-and-fft.md)

## 학습 목표

이 자료는 디지털 필터 설계의 전체 framework와 FIR/IIR 설계법을 정리한다.

- filter의 목적
- digital filter의 장단점
- FIR와 IIR 비교
- FIR window/frequency sampling/optimal method
- finite wordlength effect
- IIR pole-zero placement, impulse invariant, bilinear transform
- Butterworth 등 classical analog filter 기반 설계

## Filter란 무엇인가

필터는 신호의 amplitude-frequency 또는 phase-frequency characteristic을 원하는 방식으로 바꾸는 시스템이다.

필터를 사용하는 이유:

1. 신호 품질 향상
2. 필요한 정보 추출
3. 여러 신호 성분 분리

## Digital Filter의 장단점

장점:

- analog filter로 만들기 어려운 응답 구현 가능
- 온도/부품 노화 등 환경 변화에 강함
- adaptive filter 구현 가능
- 하나의 필터 구조로 여러 신호 처리 가능
- 높은 정밀도와 반복성
- 소형, 저비용, 저전력 구현 가능

단점:

- sampling/processor speed 제한
- finite precision 문제
- 설계와 개발 시간이 필요

## FIR와 IIR

### FIR

$$
y[n]=\sum_{k=0}^{M}b_k x[n-k]
$$

특징:

- feedback 없음
- impulse response가 유한
- 정확한 linear phase 가능
- 안정성 확보가 쉬움
- sharp cutoff를 위해 많은 계수가 필요할 수 있음

### IIR

$$
y[n]=\sum_{k=0}^{M}b_k x[n-k]
-\sum_{k=1}^{N}a_k y[n-k]
$$

특징:

- feedback 있음
- impulse response가 무한
- 적은 계수로 sharp transition 가능
- analog filter 이론을 활용 가능
- pole 위치에 따라 불안정 가능
- 일반적으로 정확한 linear phase는 어렵다.

## FIR 또는 IIR 선택 기준

| 요구사항 | 적합한 선택 |
|---|---|
| 정확한 linear phase 필요 | FIR |
| 안정성 단순 확보 | FIR |
| 적은 차수로 sharp cutoff 필요 | IIR |
| analog prototype 활용 | IIR |
| finite precision에 강한 구조 | 상황에 따라 FIR 또는 sectioned IIR |

## 필터 설계 단계

1. specification 결정
2. filter coefficient 계산
3. realization structure 선택
4. finite wordlength effect 분석
5. hardware/software 구현 및 test

Specification에는 다음이 포함된다.

- passband/stopband edge
- passband ripple
- stopband attenuation
- phase 조건
- real-time 여부
- 구현 플랫폼과 cost

## FIR 설계: Window Method

이상적 필터의 impulse response $h_d[n]$는 보통 무한 길이이다. 이를 window $w[n]$로 잘라 FIR을 만든다.

$$
h[n]=h_d[n]w[n]
$$

대표 window:

- rectangular
- Bartlett/triangular
- Hanning
- Hamming
- Blackman
- Kaiser

장점:

- 단순하고 직관적
- 가장 널리 쓰이는 기본 방법

단점:

- passband/stopband ripple을 독립적으로 제어하기 어렵다.
- transition bandwidth와 ripple이 window 종류와 길이에 묶인다.
- desired response가 복잡하면 표현이 어려울 수 있다.

## FIR 설계: Optimal Method

최대 weighted error를 최소화한다.

$$
E(e^{j\omega})=W(e^{j\omega})[H_d(e^{j\omega})-H(e^{j\omega})]
$$

목표:

$$
\min \max_\omega |E(e^{j\omega})|
$$

equiripple passband/stopband 개념을 사용한다. 대표적으로 Parks-McClellan/Remez 알고리즘이 이 계열이다.

## FIR 설계: Frequency Sampling Method

원하는 주파수 응답 $H_d(e^{j\omega})$를 등간격 주파수 지점에서 지정하고 IDFT로 impulse response를 얻는다.

장점:

- 임의 모양의 frequency response를 설계하기 쉽다.
- FIR이지만 recursive implementation 형태도 가능하다.

## FIR 구현 구조

### Transversal Structure

tapped delay line 구조이다. 가장 기본적인 FIR 구현이다.

### Linear Phase Structure

impulse response가 대칭이면 곱셈 수를 줄일 수 있다.

even symmetry:

$$
h[n]=h[N-1-n]
$$

odd symmetry:

$$
h[n]=-h[N-1-n]
$$

## Finite Wordlength Effect

실제 구현에서는 무한 정밀도가 아니므로 다음 문제가 생긴다.

- ADC quantization noise
- coefficient quantization
- arithmetic round-off
- overflow
- scaling에 따른 SNR 저하

특히 IIR은 feedback 때문에 coefficient quantization과 pole 이동에 민감하다.

## IIR 설계

IIR 설계 단계도 FIR와 비슷하다.

1. filter specification
2. coefficient approximation
3. realization
4. error analysis
5. implementation

## Pole-Zero Placement

원하는 주파수에서 zero를 배치해 감쇠하고, 원하는 passband 근처에 pole을 배치해 gain을 높인다.

예: sampling frequency 500 Hz, 중심 125 Hz의 narrow bandpass, DC와 250 Hz 제거.

- DC 제거: $z=1$에 zero
- 250 Hz 제거: $z=-1$에 zero
- 125 Hz 중심 passband: unit circle의 해당 angle 근처에 conjugate pole pair

## Impulse Invariant Method

analog filter의 impulse response를 sampling해 digital impulse response를 만든다.

장점:

- 시간 영역 impulse response 모양 보존

단점:

- analog frequency response가 sampling되므로 aliasing 가능
- low-pass/band-limited 상황에서 더 적합

## Bilinear Z-Transform

s-plane과 z-plane을 다음 변환으로 연결한다.

$$
s=\frac{2}{T}\frac{1-z^{-1}}{1+z^{-1}}
$$

특징:

- left-half s-plane을 unit circle 내부로 mapping하므로 안정성이 보존된다.
- frequency warping이 발생한다.
- aliasing은 없다.

prewarping:

$$
\Omega_c = \frac{2}{T}\tan\left(\frac{\omega_c}{2}\right)
$$

## BZT 설계 절차

1. digital filter specification을 정한다.
2. cutoff frequency를 digital frequency로 정리한다.
3. prewarping으로 analog cutoff를 구한다.
4. normalized analog prototype을 frequency scaling한다.
5. bilinear transform을 적용해 $H(z)$를 얻는다.
6. difference equation으로 구현한다.

## Classical Analog Filters

| 필터 | 특징 |
|---|---|
| Butterworth | passband가 maximally flat, ripple 없음 |
| Chebyshev I | passband ripple, sharp transition |
| Chebyshev II | stopband ripple |
| Elliptic | passband/stopband ripple, 가장 sharp한 transition 가능 |

## 체크포인트

- FIR는 linear phase와 안정성이 장점이다.
- IIR는 낮은 차수로 sharp한 응답을 만들 수 있지만 안정성과 양자화가 중요하다.
- window method는 간단하지만 ripple/transition 제어가 제한적이다.
- bilinear transform은 안정성을 보존하지만 frequency warping 때문에 prewarping이 필요하다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. Digital Filter Design**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
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
- **11. Digital Filter Design**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Wiener Optimal Filter](10-wiener-optimal-filter.md) · 다음: [12. DFT and FFT](12-dft-and-fft.md)
