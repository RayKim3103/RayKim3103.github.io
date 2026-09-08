---
layout: page
title: "05. 이산시간 푸리에 변환"
permalink: /studies/signals/signals-and-systems/05-discrete-time-fourier-transform/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/05%20%EC%9D%B4%EC%82%B0%EC%8B%9C%EA%B0%84%20%ED%91%B8%EB%A6%AC%EC%97%90%20%EB%B3%80%ED%99%98.md)

{% raw %}
## 핵심 요약

DTFT는 비주기 이산시간 신호를 연속 주파수 변수 `Ω`의 함수로 표현한다. CTFT와 비슷하지만 DTFT 스펙트럼은 `2π` 주기를 가진다. 이 장은 DTFT의 정의, DTFS와의 관계, 주기 신호의 DTFT, 성질, DT LTI 시스템의 주파수 응답, 차분방정식 해석을 정리한다.

## DTFT 정의

DTFT 쌍은 다음과 같다.

```text
X(ejΩ) = Σ[n=-∞,∞] x[n]e-jΩn
x[n] = (1/2π)∫[2π] X(ejΩ)ejΩn dΩ
```

합성식의 적분은 길이 `2π`인 임의의 한 주파수 구간에서 수행한다. DTFT의 가장 중요한 차이는

```text
X(ej(Ω+2π)) = X(ejΩ)
```

라는 주기성이다.

## DTFS에서 DTFT로

유한 길이 신호 `x[n]`을 아주 긴 주기의 주기 신호로 확장하면 DTFS 계수의 envelope가 DTFT가 된다. DTFS 계수와 DTFT 사이에는

```text
ak = (1/N) X(ej(2πk/N))
```

관계가 있다. 즉 DTFT는 DTFS 주파수 샘플들의 연속화로 볼 수 있다.

## 대표 DTFT 쌍

| 시간 영역 | 주파수 영역 |
| --- | --- |
| `δ[n]` | `1` |
| `anu[n], |a| < 1` | `1 / (1 - ae-jΩ)` |
| `a|n|, |a| < 1` | `(1-a^2)/(1 - 2a cosΩ + a^2)` |
| 길이 `2N+1` 직사각 sequence | Dirichlet kernel 형태 |
| 이상적 DT LPF | sinc 형태 impulse response |

DT에서 시간 영역이 길어질수록 주파수 영역의 main lobe는 좁아지고, 시간 영역이 짧으면 넓은 스펙트럼을 갖는다.

## 수렴 관점

DTFT analysis sum은 다음 조건에서 잘 수렴한다.

```text
Σ |x[n]| < ∞
```

그러나 synthesis integral은 유한한 `2π` 구간에서 적분하므로 CTFT보다 다루기 편한 면이 있다. 절대합 가능하지 않은 신호도 일반화 함수나 극한 의미로 DTFT를 다룰 수 있다.

## 주기 DT 신호의 DTFT

주기 `N`인 sequence의 DTFS 계수가 `ak`이면 DTFT는 주파수축의 impulse train이다.

```text
X(ejΩ) = 2π Σ[k=<N>] ak Σ[r=-∞,∞] δ(Ω - 2πk/N - 2πr)
```

주기 신호의 스펙트럼은 CT와 마찬가지로 discrete line spectrum이지만, DT 주파수축 자체가 `2π` 주기성을 갖는다는 점이 다르다.

## 주요 성질

| 성질 | 시간 영역 | 주파수 영역 |
| --- | --- | --- |
| 선형성 | `ax[n]+by[n]` | `aX(ejΩ)+bY(ejΩ)` |
| 시간 이동 | `x[n-n0]` | `e-jΩn0X(ejΩ)` |
| 주파수 이동 | `ejΩ0n x[n]` | `X(ej(Ω-Ω0))` |
| 시간 반전 | `x[-n]` | `X(e-jΩ)` |
| 차분 | `x[n]-x[n-1]` | `(1-e-jΩ)X(ejΩ)` |
| 주파수 미분 | `nx[n]` | `j dX(ejΩ)/dΩ` |

실수 sequence이면

```text
X(e-jΩ) = X*(ejΩ)
```

이다. 따라서 magnitude는 짝대칭, phase는 홀대칭이다.

## 누산기와 DC 성분

누산기

```text
y[n] = Σ[m=-∞,n] x[m]
```

는 주파수 영역에서 `1/(1-e-jΩ)` 형태와 DC impulse 항이 관련된다. DC 성분은 평균값과 연결되므로, 차분기와 누산기를 해석할 때 `Ω = 0` 근처의 특이성을 주의해야 한다.

## 컨볼루션 성질

DT LTI 시스템에서

```text
y[n] = x[n] * h[n]
Y(ejΩ) = X(ejΩ)H(ejΩ)
```

가 성립한다. 따라서 `H(ejΩ)`는 DT LTI 시스템의 frequency response이다.

이상적 DT 저역통과 필터는

```text
H(ejΩ) = 1, |Ω| < Ωc
H(ejΩ) = 0, Ωc < |Ω| < π
```

이고 impulse response는

```text
h[n] = sin(Ωc n) / (πn), n ≠ 0
h[0] = Ωc / π
```

이다.

## 곱셈 성질

시간 영역 곱셈은 주파수 영역에서 periodic convolution이 된다.

```text
x[n]h[n] <-> (1/2π)∫[2π] X(ejθ)H(ej(Ω-θ)) dθ
```

DT 주파수축은 원형이므로 컨볼루션도 주기적으로 감긴다.

## DT LCCDE와 주파수 응답

선형 상수계수 차분방정식

```text
Σ[k=0,N] ak y[n-k] = Σ[k=0,M] bk x[n-k]
```

에 DTFT를 적용하면 delay가 `e-jΩk` 곱으로 바뀐다.

```text
H(ejΩ) = (Σ[k=0,M] bk e-jΩk) / (Σ[k=0,N] ak e-jΩk)
```

이 식은 디지털 필터의 주파수 응답을 계산하는 표준 형태이다.

## CTFT와 DTFT 차이 정리

| 항목 | CTFT | DTFT |
| --- | --- | --- |
| 시간 변수 | 연속 `t` | 정수 `n` |
| 주파수 변수 | 비주기 `ω` | `2π` 주기 `Ω` |
| analysis | 적분 | 합 |
| synthesis | 전체 주파수축 적분 | 길이 `2π` 구간 적분 |
| LTI 응답 | `Y(jω)=H(jω)X(jω)` | `Y(ejΩ)=H(ejΩ)X(ejΩ)` |

## 연결 노트

- [푸리에 급수](03-fourier-series.md)
- [연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
- [시간-주파수 특성](06-time-frequency-properties.md)
- [샘플링](07-sampling.md)
- [라플라스 변환과 z 변환](08-laplace-and-z-transforms.md)

{% endraw %}

---

이전: [04. 연속시간 푸리에 변환](04-continuous-time-fourier-transform.md) · 다음: [06. 시간-주파수 특성](06-time-frequency-properties.md)
