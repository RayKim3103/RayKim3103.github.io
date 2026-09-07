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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. 이산시간 푸리에 변환**를 다루며, 신호와 LTI 시스템을 convolution, Fourier/Laplace/z 변환으로 해석하는 공학 수학의 핵심 기반이다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 신호 주제에서는 시간영역 연산이 주파수/z-domain에서 어떻게 단순해지는지 변환쌍으로 연결한다.
- sampling과 DFT에서는 주기화, aliasing, zero padding을 분리해 생각해야 한다.
- LTI 시스템은 impulse response만 알면 convolution으로 모든 입력에 대한 출력을 구할 수 있다.
- Fourier 계열과 변환은 신호를 frequency component로 분해해 filtering과 system response를 단순화한다.
- Laplace/z transform은 Fourier보다 ROC와 pole 관점을 추가해 stability와 transient까지 다룬다.

### 문제 풀이 또는 구현 루틴

- 신호를 continuous/discrete, periodic/aperiodic, energy/power로 먼저 분류한다.
- LTI 문제는 impulse response, convolution, frequency response 중 가장 쉬운 표현을 선택한다.
- 변환 문제에서는 transform pair, property, ROC 또는 convergence 조건을 함께 기록한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- time shift와 frequency shift property의 부호를 자주 혼동한다.
- sampling은 spectrum replication을 만들므로 Nyquist 조건을 항상 확인해야 한다.
- stability는 impulse response 절대적분/절대합 조건과 pole 위치 조건을 연결해 판단한다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 신호/시스템은 어떤 분류에 속하는가?
- time domain과 frequency domain 중 어느 쪽 계산이 더 단순한가?
- pole, zero, ROC가 causality와 stability를 어떻게 말해 주는가?
- **05. 이산시간 푸리에 변환**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. 연속시간 푸리에 변환](04-continuous-time-fourier-transform.md) · 다음: [06. 시간-주파수 특성](06-time-frequency-properties.md)
