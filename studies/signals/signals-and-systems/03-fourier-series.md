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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. 푸리에 급수**를 다루며, 신호와 LTI 시스템을 convolution, Fourier/Laplace/z 변환으로 해석하는 공학 수학의 핵심 기반이다.
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
- **03. 푸리에 급수**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. LTI 시스템과 컨볼루션](02-lti-systems-and-convolution.md) · 다음: [04. 연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
