---
layout: page
title: "04. 연속시간 푸리에 변환"
permalink: /studies/signals/signals-and-systems/04-continuous-time-fourier-transform/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/04%20%EC%97%B0%EC%86%8D%EC%8B%9C%EA%B0%84%20%ED%91%B8%EB%A6%AC%EC%97%90%20%EB%B3%80%ED%99%98.md)

{% raw %}
## 핵심 요약

연속시간 푸리에 변환은 비주기 CT 신호를 연속 주파수 성분의 적분으로 표현한다. 푸리에 급수를 주기가 무한대로 가는 극한으로 확장하면 CTFT가 나온다. CTFT는 비주기 신호의 스펙트럼, LTI 시스템의 주파수 응답, 필터링, 변조, 미분방정식 해석의 기본 도구이다.

## CTFT 정의

CTFT 쌍은 다음과 같다.

```text
X(jω) = ∫[-∞,∞] x(t)e-jωt dt
x(t) = (1/2π)∫[-∞,∞] X(jω)ejωt dω
```

첫 식은 analysis equation이고, 둘째 식은 synthesis equation이다.

## 푸리에 급수에서 CTFT로

주기 신호의 주기 `T`를 점점 키우면 주파수 간격 `ω0 = 2π/T`가 점점 작아진다. 한 주기만 남은 비주기 신호의 스펙트럼은 이산적인 푸리에 급수 계수의 envelope에서 연속 스펙트럼으로 넘어간다.

푸리에 급수 계수와 CTFT의 관계는

```text
ak = (1/T) X(jkω0)
```

로 이해할 수 있다.

## 대표 CTFT 쌍

| 시간 영역 | 주파수 영역 |
| --- | --- |
| `e-at u(t), a > 0` | `1 / (a + jω)` |
| `e-a|t|, a > 0` | `2a / (a^2 + ω^2)` |
| `δ(t)` | `1` |
| 직사각 펄스 | sinc 형태 |
| sinc 형태 | 직사각 스펙트럼 |

시간 영역의 폭과 주파수 영역의 폭은 반비례한다. 좁은 펄스는 넓은 스펙트럼을 갖고, 넓은 펄스는 좁은 스펙트럼을 갖는다.

## 주기 신호의 CTFT

주기 신호는 CTFT에서 impulse train 형태의 스펙트럼을 가진다. 푸리에 급수 계수가 `ak`이면

```text
X(jω) = 2π Σ[k=-∞,∞] ak δ(ω - kω0)
```

이다. 즉 주기 신호의 에너지는 조화 주파수 위치에 impulse로 모인다.

## CTFT 수렴 조건

실용적으로 다음 조건을 만족하면 CTFT 표현이 잘 동작한다.

- `x(t)`가 절대적분 가능
- 유한 구간 안에서 최대ㆍ최소가 유한 개
- 유한 구간 안에서 불연속점이 유한 개

에너지 신호에서는 평균제곱 오차 관점에서 유효한 표현이 된다. 불연속점에서는 좌우 극한의 평균으로 수렴할 수 있다.

## 주요 성질

| 성질 | 시간 영역 | 주파수 영역 |
| --- | --- | --- |
| 선형성 | `ax(t)+by(t)` | `aX(jω)+bY(jω)` |
| 시간 이동 | `x(t-t0)` | `e-jωt0 X(jω)` |
| 주파수 이동 | `ejω0t x(t)` | `X(j(ω-ω0))` |
| 시간 반전 | `x(-t)` | `X(-jω)` |
| 시간 스케일링 | `x(at)` | `(1/|a|)X(jω/a)` |
| 미분 | `dx/dt` | `jωX(jω)` |
| 적분 | `∫[-∞,t]x(τ)dτ` | `X(jω)/(jω) + πX(0)δ(ω)` |

실수 신호의 스펙트럼은 conjugate symmetry를 가진다.

```text
X(-jω) = X*(jω)
```

따라서 magnitude는 짝함수이고 phase는 홀함수이다.

## Parseval 관계

시간 영역 에너지와 주파수 영역 에너지는 같다.

```text
∫ |x(t)|^2 dt = (1/2π)∫ |X(jω)|^2 dω
```

`|X(jω)|^2`는 주파수별 에너지 밀도 스펙트럼으로 해석할 수 있다.

## 컨볼루션 성질

시간 영역 컨볼루션은 주파수 영역 곱셈으로 바뀐다.

```text
y(t) = x(t) * h(t)
Y(jω) = X(jω)H(jω)
```

따라서 LTI 시스템의 frequency response `H(jω)`는 시스템을 완전히 특성화한다. 필터링은 주파수 영역에서 스펙트럼에 `H(jω)`를 곱하는 과정이다.

## 곱셈 성질과 변조

시간 영역 곱셈은 주파수 영역 컨볼루션으로 바뀐다.

```text
x(t)h(t) <-> (1/2π) X(jω) * H(jω)
```

특히 `cos(ωct)`를 곱하면 스펙트럼이 `±ωc`로 이동한다. 이것이 진폭 변조와 주파수 이동 필터링의 핵심이다.

## 이상적 저역통과 필터

이상적 LPF는

```text
H(jω) = 1, |ω| < ωc
H(jω) = 0, |ω| > ωc
```

이며 impulse response는 sinc 형태이다.

```text
h(t) = sin(ωc t) / (πt)
```

완벽한 주파수 선택성을 갖지만 impulse response가 양의 시간과 음의 시간에 모두 존재하므로 noncausal이다. 실제 필터 설계에서는 주파수 선택성과 시간 영역 ringing 사이의 trade-off가 생긴다.

## LCCDE 시스템의 주파수 응답

선형 상수계수 미분방정식

```text
Σ ak d^k y(t)/dt^k = Σ bk d^k x(t)/dt^k
```

에 CTFT를 적용하면 미분이 `jω` 곱으로 바뀌어

```text
H(jω) = Y(jω)/X(jω)
```

가 유리함수 형태로 나온다. 부분분수 전개를 사용하면 impulse response를 계산할 수 있다.

## 연결 노트

- [푸리에 급수](03-fourier-series.md)
- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
- [시간-주파수 특성](06-time-frequency-properties.md)
- [라플라스 변환과 z 변환](08-laplace-and-z-transforms.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. 연속시간 푸리에 변환**를 다루며, 신호와 LTI 시스템을 convolution, Fourier/Laplace/z 변환으로 해석하는 공학 수학의 핵심 기반이다.
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
- **04. 연속시간 푸리에 변환**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. 푸리에 급수](03-fourier-series.md) · 다음: [05. 이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
