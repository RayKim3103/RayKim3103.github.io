---
layout: page
title: "03. 푸리에 급수"
permalink: /studies/signals/signals-and-systems/03-fourier-series/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/03%20%ED%91%B8%EB%A6%AC%EC%97%90%20%EA%B8%89%EC%88%98.md)

{% raw %}
## 핵심 요약

푸리에 급수는 주기 신호를 서로 조화 관계에 있는 복소 지수들의 가중합으로 표현한다. LTI 시스템에서 복소 지수는 eigenfunction이므로, 주기 입력을 푸리에 급수로 나누면 각 주파수 성분이 시스템의 frequency response만큼 스케일되고 위상 이동된 뒤 다시 합쳐진다.

## 역사적 배경

진동하는 현을 설명하려는 과정에서 Euler, Bernoulli, Lagrange가 정상모드와 삼각급수 문제를 다루었고, Fourier는 임의의 주기 신호가 조화 정현파의 합으로 표현될 수 있다고 주장했다. 이 관점이 현대 신호 해석의 기본이 된다.

## LTI 시스템과 복소 지수

CT LTI 시스템에 `x(t) = est`를 넣으면

```text
y(t) = H(s)est
H(s) = ∫ h(τ)e-sτ dτ
```

가 된다. DT에서는 `x[n] = zn`에 대해

```text
y[n] = H(z)zn
H(z) = Σ h[k]z-k
```

이다. 입력과 같은 복소 지수가 출력되고, 크기만 `H`만큼 바뀌므로 복소 지수는 LTI 시스템의 eigenfunction이다.

## CT 푸리에 급수

주기 `T0`, 기본각주파수 `ω0 = 2π/T0`를 가진 CT 주기 신호는

```text
x(t) = Σ[k=-∞,∞] ak ejkω0t
```

로 표현된다. 계수는 한 주기 적분으로 구한다.

```text
ak = (1/T0) ∫T0 x(t)e-jkω0t dt
```

첫 식은 synthesis equation이고, 둘째 식은 analysis equation이다.

**숫자 예 — 구형파(square wave)**: 한 주기 $$T_0=4$$ 안에서 $$|t|<T_1=1$$일 때 $$1$$, 나머지는 $$0$$인 50% duty cycle 구형파($$\omega_0=2\pi/T_0=\pi/2$$)의 계수는

$$
a_0=\frac{2T_1}{T_0}=\frac12,\qquad a_k=\frac{\sin(k\omega_0T_1)}{k\pi}=\frac{\sin(k\pi/2)}{k\pi}\ (k\ne0)
$$

$$
a_1=\frac{1}{\pi}\approx0.318,\quad a_2=0,\quad a_3=\frac{-1}{3\pi}\approx-0.106,\quad a_4=0,\ \ldots
$$

**짝수 harmonic이 전부 0**이 되는 것은 50% duty cycle 구형파가 흔히 아는 "구형파는 홀수 harmonic만 갖는다"는 결과 그대로다. $$a_0=0.5$$는 신호의 DC 성분(=한 주기 평균값)과 일치한다.

## 실수 주기 신호의 성질

`x(t)`가 실수이면 푸리에 계수는 conjugate symmetry를 가진다.

```text
a-k = ak*
```

따라서 양의 주파수 계수만 알아도 전체 신호를 복원할 수 있다. 또한 real-even 신호는 계수가 실수이고 짝대칭인 경향을, real-odd 신호는 순허수 계수와 홀대칭 성질을 가진다.

## 푸리에 급수의 수렴

Dirichlet 조건은 실용적인 주기 신호에서 푸리에 급수가 잘 동작하는 충분조건이다.

- 한 주기에서 절대적분 가능
- 한 주기에서 최대ㆍ최소가 유한 개
- 한 주기에서 불연속점이 유한 개

불연속점에서는 푸리에 급수가 좌우 극한의 평균값으로 수렴한다. 사각파처럼 불연속이 있는 신호에서는 부분합이 불연속점 근처에서 약 9% overshoot를 보이는 Gibbs phenomenon이 나타난다.

## CT 푸리에 급수의 주요 성질

| 성질 | 시간 영역 | 계수 영역 |
| --- | --- | --- |
| 선형성 | `Ax(t)+By(t)` | `Aak + Bbk` |
| 시간 이동 | `x(t-t0)` | `ak e-jkω0t0` |
| 시간 반전 | `x(-t)` | `a-k` |
| 켤레 | `x*(t)` | `a-k*` |
| 곱셈 | `x(t)y(t)` | 계수의 비주기 컨볼루션 |

Parseval 관계는 한 주기 평균 전력이 조화 성분들의 전력 합과 같다는 뜻이다.

```text
(1/T0)∫T0 |x(t)|^2 dt = Σ |ak|^2
```

## DT 푸리에 급수

DT 주기 신호의 주기가 `N`이면 서로 다른 조화 복소 지수는 `N`개뿐이다.

```text
x[n] = Σ<k=N> ak ej(2π/N)kn
ak = (1/N) Σ<n=N> x[n]e-j(2π/N)kn
```

`Σ<k=N>`는 길이 `N`의 한 주기에 대한 합을 뜻한다. CT와 달리 DT 푸리에 급수는 유한합이다.

## DT 푸리에 급수의 차이점

DT 주파수는 `2π` 주기성을 가지므로 `k`와 `k + N`은 같은 조화 성분이다.

```text
ak+N = ak
```

곱셈 성질에서는 계수 영역에서 periodic convolution이 등장한다. 이는 DT 주파수 축이 원형으로 감겨 있기 때문이다.

## 푸리에 급수와 LTI 시스템

주기 입력의 푸리에 계수가 `ak`이고 LTI 시스템의 주파수 응답이 `H(jω)`이면 CT 출력 계수는

```text
bk = H(jkω0) ak
```

DT에서는

```text
bk = H(ej(2π/N)k) ak
```

이다. 즉 시스템은 각 harmonic 성분에 대해 gain과 phase를 따로 적용한다.

## 필터링 관점

푸리에 급수는 필터링을 직관적으로 설명한다.

- frequency shaping filter: 주파수 성분의 상대적 크기를 조정
- frequency selective filter: 특정 주파수 대역을 통과 또는 제거
- low-pass filter: 저주파 통과
- high-pass filter: 고주파 통과
- band-pass filter: 특정 대역 통과

이퀄라이저는 저음, 중음, 고음을 서로 다른 gain으로 조절하는 frequency shaping filter의 예이다.

## 연결 노트

- [LTI 시스템과 컨볼루션](02-lti-systems-and-convolution.md)
- [연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
- [시간-주파수 특성](06-time-frequency-properties.md)

## 복습 질문

- 50% duty cycle 구형파의 $$a_k$$ 공식을 유도하고 $$k=1,2,3,4$$를 직접 계산할 수 있는가?
- 짝수 harmonic이 0이 되는 이유를 duty cycle과 연결해 설명할 수 있는가?
- Gibbs phenomenon이 왜 불연속점 근처에서만 나타나며, 항 수를 늘려도 overshoot 비율(약 9%)이 줄지 않는 이유는?



{% endraw %}

---

이전: [02. LTI 시스템과 컨볼루션](02-lti-systems-and-convolution.md) · 다음: [04. 연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
