---
layout: page
title: "02. LTI 시스템과 컨볼루션"
permalink: /studies/signals/signals-and-systems/02-lti-systems-and-convolution/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/02%20LTI%20%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B3%BC%20%EC%BB%A8%EB%B3%BC%EB%A3%A8%EC%85%98.md)

{% raw %}
## 핵심 요약

LTI 시스템은 impulse response만 알면 모든 입력에 대한 출력을 계산할 수 있다. DT에서는 convolution sum, CT에서는 convolution integral을 사용한다. 이 장은 impulse decomposition, 컨볼루션, LTI 시스템의 성질, 미분방정식ㆍ차분방정식 모델, 특이 함수까지 다룬다.

## DT 신호의 임펄스 분해

DT 신호는 지연된 임펄스들의 가중합으로 쓸 수 있다.

```text
x[n] = Σ[k=-∞,∞] x[k] δ[n - k]
```

LTI 시스템에서 `δ[n]`에 대한 응답을 `h[n]`이라 하면, `δ[n-k]`에 대한 응답은 time invariance 때문에 `h[n-k]`이다. 선형성을 적용하면 출력은

```text
y[n] = Σ[k=-∞,∞] x[k] h[n - k]
```

가 된다. 이를 convolution sum이라 하고 `y[n] = x[n] * h[n]`로 쓴다.

## CT 컨볼루션 적분

CT 신호도 아주 좁은 펄스의 합으로 근사하고, 극한을 취하면 임펄스 분해가 된다.

```text
x(t) = ∫ x(τ) δ(t - τ) dτ
```

LTI 시스템의 impulse response를 `h(t)`라 하면

```text
y(t) = ∫ x(τ) h(t - τ) dτ
```

이다. 이는 convolution integral이다.

```text
y(t) = x(t) * h(t)
```

## 그래픽 컨볼루션 절차

컨볼루션을 손으로 계산할 때는 다음 순서가 유용하다.

1. `h[k]` 또는 `h(τ)`를 시간 반전한다.
2. `n` 또는 `t`만큼 이동한다.
3. 입력과 겹치는 구간을 찾는다.
4. DT에서는 곱의 합, CT에서는 곱의 적분을 계산한다.
5. 이동 변수의 구간별로 결과식을 정리한다.

사각 펄스끼리의 컨볼루션은 겹치는 길이나 면적이 시간에 따라 어떻게 바뀌는지 보는 문제이다.

**숫자 예**: $$x(t)$$가 $$[0,2]$$에서 높이 1인 펄스, $$h(t)$$가 $$[0,1]$$에서 높이 1인 펄스라 하자. $$h(t-\tau)$$는 $$\tau\in[t-1,t]$$에서 1이므로, $$y(t)=\int x(\tau)h(t-\tau)d\tau$$는 두 구간의 **겹치는 길이**와 같다.

$$
y(t)=
\begin{cases}
0, & t<0\\
t, & 0\le t\le1\\
1, & 1\le t\le2\\
3-t, & 2\le t\le3\\
0, & t>3
\end{cases}
$$

$$0\to1$$로 선형 증가, $$1\sim2$$에서 평평(높이 1), $$2\to3$$에서 선형 감소하는 **사다리꼴**이다. 검산: 전체 면적은 $$\int y(t)\,dt = 0.5+1+0.5=2$$이고, 이는 $$\left(\int x\,dt\right)\left(\int h\,dt\right)=2\times1=2$$와 일치한다(convolution은 넓이를 보존하지 않지만, 두 신호 넓이의 곱과는 항상 같다 — $$\int y = \left(\int x\right)\left(\int h\right)$$는 컨볼루션의 일반 성질).

## 컨볼루션의 대수 성질

LTI 시스템에서 컨볼루션은 다음 성질을 가진다.

```text
x * h = h * x
x * (h1 + h2) = x * h1 + x * h2
x * (h1 * h2) = (x * h1) * h2
```

따라서 LTI 시스템의 직렬 연결은 impulse response의 컨볼루션으로, 병렬 연결은 impulse response의 합으로 표현할 수 있다.

## LTI 시스템의 memory

LTI 시스템이 memoryless이려면 impulse response가 원점 임펄스에 비례해야 한다.

```text
DT: h[n] = Kδ[n]
CT: h(t) = Kδ(t)
```

그 외의 시간에 `h`가 존재하면 출력이 과거나 미래 입력에 의존하므로 memory가 있다.

## LTI 시스템의 causality

causal LTI 시스템의 필요충분조건은 impulse response가 음의 시간에서 0이라는 것이다.

```text
DT: h[n] = 0 for n < 0
CT: h(t) = 0 for t < 0
```

이 조건이면 현재 출력이 미래 입력에 의존하지 않는다.

## LTI 시스템의 stability

BIBO 안정성을 만족하려면 impulse response가 절대합 가능 또는 절대적분 가능해야 한다.

```text
DT: Σ |h[n]| < ∞
CT: ∫ |h(t)| dt < ∞
```

예를 들어 누산기 `h[n] = u[n]`와 적분기 `h(t) = u(t)`는 impulse response의 절대합/절대적분이 무한대이므로 BIBO 안정하지 않다.

## 단위 계단 응답

unit step response는 입력이 `u`일 때의 출력이다.

```text
s[n] = h[n] * u[n] = Σ[k=-∞,n] h[k]
s(t) = h(t) * u(t) = ∫[-∞,t] h(τ)dτ
```

따라서

```text
h[n] = s[n] - s[n - 1]
h(t) = ds(t)/dt
```

관계가 성립한다.

## 미분방정식과 차분방정식

많은 causal LTI 시스템은 선형 상수계수 미분방정식 또는 차분방정식으로 표현된다.

### CT LCCDE

```text
Σ[k=0,N] ak d^k y(t)/dt^k = Σ[k=0,M] bk d^k x(t)/dt^k
```

미분방정식만으로는 시스템이 완전히 정해지지 않는다. 초기 조건 또는 initial rest 조건이 필요하다.

### DT LCCDE

```text
Σ[k=0,N] ak y[n-k] = Σ[k=0,M] bk x[n-k]
```

recursive 구조이면 과거 출력이 현재 출력에 영향을 주므로 일반적으로 IIR이 된다. nonrecursive 구조이면 impulse response가 유한 길이인 FIR이 된다.

## FIR과 IIR

| 구분 | 특징 | 예 |
| --- | --- | --- |
| FIR | impulse response가 유한 길이 | moving average |
| IIR | impulse response가 무한 길이 | recursive filter, accumulator |

강의의 예시처럼 `y[n] = (1/2)y[n-1] + x[n]` 형태는 impulse response가 `((1/2)^n)u[n]`로 이어져 IIR이다.

## 특이 함수

CT 단위 임펄스는 일반 함수가 아니라 이상화 함수이다. 컨볼루션 관점에서는 identity 역할을 한다.

```text
x(t) * δ(t) = x(t)
x(t) * δ(t - t0) = x(t - t0)
```

임펄스의 미분 `δ'(t)`와 컨볼루션하면 입력의 미분이 나온다.

```text
x(t) * δ'(t) = dx(t)/dt
```

반대로 단위 계단과 컨볼루션하면 적분기가 된다.

```text
x(t) * u(t) = ∫[-∞,t] x(τ)dτ
```

## 연결 노트

- [신호와 시스템 개요](01-signals-and-systems-overview.md)
- [푸리에 급수](03-fourier-series.md)
- [연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)

## 복습 질문

- $$[0,2]$$ 폭 1 펄스와 $$[0,1]$$ 폭 1 펄스의 convolution이 사다리꼴이 되는 이유를, 겹치는 구간 길이로 설명할 수 있는가?
- $$\int y(t)dt = \left(\int x\,dt\right)\left(\int h\,dt\right)$$가 항상 성립하는 이유는?
- causal LTI의 필요충분조건($$h(t)=0,\ t<0$$)과 BIBO 안정 조건($$\int|h(t)|dt<\infty$$)을 각각 설명할 수 있는가?

{% endraw %}

---

이전: [01. 신호와 시스템 개요](01-signals-and-systems-overview.md) · 다음: [03. 푸리에 급수](03-fourier-series.md)
