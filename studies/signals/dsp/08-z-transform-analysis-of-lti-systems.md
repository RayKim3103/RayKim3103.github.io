---
layout: page
title: "08. Z-Transform Analysis of LTI Systems"
permalink: /studies/signals/dsp/08-z-transform-analysis-of-lti-systems/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Digital_Signal_Processing/lecture_notes/08%20Z-Transform%20Analysis%20of%20LTI%20Systems.md)

{% raw %}
﻿---
title: "08. Z-Transform Analysis of LTI Systems"
pages: 15
tags: [DSP, lecture-note, z-transform, LTI, minimum-phase]
---

# 08. Z-Transform Analysis of LTI Systems

> 이전: [Z-Transform Introduction](07-z-transform-introduction.md)
> 다음: [Structures and Parametric Modeling](09-structures-and-parametric-modeling.md)

## 학습 목표

이 자료는 z-transform을 이용해 LTI 시스템을 분석한다.

- inverse system
- ROC와 inverse system의 안정성/인과성
- $H(z)$와 frequency response
- magnitude와 phase의 관계
- minimum phase system
- all-pass system

## Inverse System

LTI 시스템 $H(z)$에 inverse system $H_i(z)$를 cascade로 연결했을 때 전체 응답이 identity가 되려면

$$
H(z)H_i(z)=1
$$

따라서

$$
H_i(z)=\frac{1}{H(z)}
$$

이다.

시간 영역에서는

$$
h[n]*h_i[n]=\delta[n]
$$

을 만족해야 한다.

## ROC의 중요성

inverse system에서도 ROC 선택이 중요하다. convolution theorem을 적용하려면 원 시스템의 ROC와 inverse system의 ROC가 겹쳐야 한다.

또한 같은 $1/H(z)$라도 ROC 선택에 따라 inverse impulse response가 causal/stable/noncausal/unstable로 달라질 수 있다.

## Zero와 Inverse Pole

$H(z)$의 zero는 $H_i(z)$의 pole이 된다.

따라서 원 시스템의 zero가 unit circle 밖에 있으면 causal inverse를 만들 때 inverse pole도 unit circle 밖에 생긴다. 이 경우 causal inverse는 unstable할 수 있다.

핵심:

- 원 시스템 zero가 unit circle 내부: causal stable inverse 가능
- 원 시스템 zero가 unit circle 외부: causal inverse는 불안정, stable inverse는 noncausal이 될 수 있음

## Frequency Response와 Pole-Zero Plot

frequency response는 unit circle 위의 시스템 함수 값이다.

$$
H(e^{j\omega}) = H(z)\big|_{z=e^{j\omega}}
$$

pole-zero plot에서 unit circle 위의 점 $e^{j\omega}$와 zero/pole 사이의 거리와 각도를 보면 magnitude와 phase를 해석할 수 있다.

유리 시스템:

$$
H(z)=C
\frac{\prod_k(1-z_k z^{-1})}
{\prod_m(1-p_m z^{-1})}
$$

unit circle에서 magnitude는 대략

$$
|H(e^{j\omega})|
= |C|\frac{\prod_k|e^{j\omega}-z_k|}
{\prod_m|e^{j\omega}-p_m|}
$$

로 볼 수 있다.

- zero 근처 주파수: magnitude 감소
- pole 근처 주파수: magnitude 증가

## Magnitude와 Phase

일반적으로 magnitude만 알면 phase가 유일하게 정해지지 않는다. pole/zero 개수가 주어져도 가능한 phase 선택은 여러 개일 수 있다.

하지만 minimum phase system이면 magnitude와 phase가 서로 유일하게 연결된다.

## Minimum Phase System

discrete-time minimum phase system은 보통 다음 조건을 만족한다.

- causal
- stable
- 모든 zero가 unit circle 내부
- inverse system도 causal and stable

minimum phase system은 같은 magnitude response를 갖는 stable causal 시스템 중 phase delay가 가장 작다.

## All-Pass System

all-pass system은 모든 주파수에서 magnitude가 1인 시스템이다.

$$
|H_{ap}(e^{j\omega})|=1
$$

1차 all-pass의 전형적 형태:

$$
H_{ap}(z)=\frac{z^{-1}-a^*}{1-az^{-1}},
\qquad |a|<1
$$

특징:

- magnitude는 변하지 않는다.
- phase만 바꾼다.
- pole과 zero가 unit circle에 대해 reciprocal conjugate 위치에 놓인다.

## Nonminimum Phase를 Minimum Phase로 만들기

unit circle 밖 zero를 안쪽 reciprocal conjugate 위치로 반사시키면 magnitude response는 all-pass factor 때문에 유지할 수 있고 phase 특성은 minimum phase 쪽으로 바뀐다.

즉 같은 magnitude를 갖는 시스템을

$$
H(z)=H_{\min}(z)H_{ap}(z)
$$

처럼 minimum phase part와 all-pass part로 분해해 이해할 수 있다.

## 체크포인트

- inverse system은 단순히 $1/H(z)$가 아니라 ROC까지 포함해 판단해야 한다.
- $H(z)$의 zero는 inverse system의 pole이 된다.
- causal stable inverse가 필요하면 zero가 unit circle 안에 있어야 한다.
- all-pass는 magnitude를 바꾸지 않고 phase만 바꾼다.
- minimum phase system은 inverse도 stable/causal인 가장 다루기 좋은 시스템이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Z-Transform Analysis of LTI Systems**를 다루며, 이산시간 신호를 z-domain, frequency domain, filter structure, FFT 관점에서 분석하고 설계한다.
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
- **08. Z-Transform Analysis of LTI Systems**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Z-Transform Introduction](07-z-transform-introduction.md) · 다음: [09. Structures and Parametric Modeling](09-structures-and-parametric-modeling.md)
