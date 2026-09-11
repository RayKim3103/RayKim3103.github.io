---
layout: page
title: "08. 라플라스 변환과 z 변환"
permalink: /studies/signals/signals-and-systems/08-laplace-and-z-transforms/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Signals_and_Systems/lecture_notes/08%20%EB%9D%BC%ED%94%8C%EB%9D%BC%EC%8A%A4%20%EB%B3%80%ED%99%98%EA%B3%BC%20z%20%EB%B3%80%ED%99%98.md)

{% raw %}
## 핵심 요약

라플라스 변환은 CTFT를 복소평면 `s = σ + jω`로 확장한 것이고, z 변환은 DTFT를 복소평면 `z = rejΩ`로 확장한 것이다. 두 변환 모두 단순한 대수식만으로는 신호가 결정되지 않고 ROC가 반드시 함께 필요하다. 시스템 해석에서는 pole, zero, ROC를 통해 causality와 stability를 판단한다.

## 라플라스 변환 정의

양방향 라플라스 변환은

```text
X(s) = ∫[-∞,∞] x(t)e-st dt
```

로 정의한다. `s = σ + jω`이다. `σ = 0`이면 CTFT와 연결된다.

```text
X(jω) = X(s)|s=jω
```

단, CTFT가 존재하려면 라플라스 변환의 ROC가 `jω`축을 포함해야 한다.

## CTFT와의 관계

라플라스 변환은 `x(t)e-σt`의 Fourier transform으로 볼 수 있다.

```text
X(σ + jω) = F{x(t)e-σt}
```

따라서 원래 CTFT가 수렴하지 않는 growing 또는 nondecaying 신호도 적절한 `σ`를 곱하면 라플라스 변환이 존재할 수 있다.

## ROC

ROC는 라플라스 적분이 수렴하는 `s` 값들의 집합이다. 유리함수 형태의 라플라스 변환에서는 pole을 포함하지 않는 수직 영역으로 나타난다.

예를 들어

```text
x(t) = e-at u(t)
X(s) = 1 / (s + a)
ROC: Re{s} > -a
```

이고,

```text
x(t) = -e-at u(-t)
X(s) = 1 / (s + a)
ROC: Re{s} < -a
```

이다. 대수식은 같지만 ROC가 다르면 시간 신호가 달라진다.

## Pole-zero 표현

유리 라플라스 변환은

```text
X(s) = N(s) / D(s)
```

로 쓰며, `D(s)=0`인 점이 pole, `N(s)=0`인 점이 zero이다. pole은 수렴성과 자연응답을 결정한다.

## 역라플라스 변환

역변환은 복소평면 적분으로 정의되지만, 실제 계산은 보통 부분분수 전개로 수행한다.

같은 `X(s)`라도 ROC에 따라 결과가 달라진다.

- ROC가 오른쪽이면 right-sided 신호
- ROC가 왼쪽이면 left-sided 신호
- pole 사이 띠이면 two-sided 신호

**숫자 예**: $$X(s)=\dfrac{1}{(s+1)(s+3)}$$, ROC $$\text{Re}\{s\}>-1$$(causal)이라 하자. 부분분수:

$$
X(s)=\frac{A}{s+1}+\frac{B}{s+3}
$$

$$s=-1$$ 대입: $$1=A(2)\Rightarrow A=0.5$$. $$s=-3$$ 대입: $$1=B(-2)\Rightarrow B=-0.5$$. ROC가 두 pole($$-1,-3$$) 중 오른쪽 pole보다도 오른쪽이므로 둘 다 right-sided:

$$
x(t) = 0.5e^{-t}u(t) - 0.5e^{-3t}u(t)
$$

검산: $$x(0^+)=0.5-0.5=0$$이고, initial value theorem $$x(0^+)=\lim_{s\to\infty}sX(s)=\lim_{s\to\infty}\dfrac{s}{(s+1)(s+3)}=0$$과 일치한다.

## 라플라스 변환의 의의

라플라스 변환은 다음 상황에서 CTFT보다 강력하다.

- CTFT가 존재하지 않는 신호도 분석 가능
- 초기 조건이 있는 미분방정식 해석 가능
- pole 위치로 안정성 판단 가능
- causal LTI 시스템의 system function 분석 가능

## z 변환 정의

양방향 z 변환은

```text
X(z) = Σ[n=-∞,∞] x[n]z-n
```

로 정의한다. `z = rejΩ`이고, `r = 1`인 단위원 위에서 DTFT와 연결된다.

```text
X(ejΩ) = X(z)|z=ejΩ
```

단, DTFT가 존재하려면 ROC가 단위원을 포함해야 한다.

## z 변환과 DTFT의 관계

z 변환은 `x[n]r-n`의 DTFT로 볼 수 있다.

```text
X(rejΩ) = F{x[n]r-n}
```

`r`이 수렴을 조절하므로, DTFT가 수렴하지 않는 sequence도 z 변환은 존재할 수 있다.

## 대표 z 변환 쌍

```text
x[n] = anu[n]
X(z) = 1 / (1 - az-1)
ROC: |z| > |a|
```

```text
x[n] = -anu[-n-1]
X(z) = 1 / (1 - az-1)
ROC: |z| < |a|
```

역시 대수식은 같아도 ROC가 다르면 신호가 달라진다.

## z 변환 ROC 성질

- ROC는 원점을 중심으로 한 ring 형태이다.
- ROC는 pole을 포함하지 않는다.
- 유한 길이 sequence의 ROC는 0 또는 무한대를 제외한 전체 z-plane일 수 있다.
- right-sided sequence의 ROC는 가장 바깥 pole 밖이다.
- left-sided sequence의 ROC는 가장 안쪽 pole 안이다.
- two-sided sequence의 ROC는 pole 사이의 ring이다.

## 역 z 변환

역 z 변환은 세 방식으로 계산한다.

1. 부분분수 전개
2. power series expansion
3. contour integral 정의 사용

유리함수에서는 부분분수 전개가 가장 흔하다. 각 항의 ROC가 바깥이면 `anu[n]`, 안쪽이면 `-anu[-n-1]` 형태로 해석한다.

## z 변환 성질

| 성질 | 시간 영역 | z 영역 |
| --- | --- | --- |
| 선형성 | `ax[n]+by[n]` | `aX(z)+bY(z)` |
| 시간 이동 | `x[n-n0]` | `z-n0X(z)` |
| z 스케일링 | `zn0 x[n]`류 | `X(z/a)` 형태 |
| 시간 반전 | `x[-n]` | `X(z-1)` |
| 컨볼루션 | `x[n]*h[n]` | `X(z)H(z)` |
| z 미분 | `nx[n]` | `-z dX(z)/dz` |

ROC는 연산 후 pole-zero cancellation 여부에 따라 넓어질 수 있으므로 항상 별도로 확인한다.

## z 변환으로 LTI 시스템 해석

DT LTI 시스템에서

```text
Y(z) = H(z)X(z)
H(z) = Z{h[n]}
```

이다. `H(z)`를 system function이라고 한다.

### Causality

causal DT LTI 시스템은 impulse response가 right-sided이다. 따라서 rational `H(z)`에서 causality는 ROC가 가장 바깥 pole 밖이고 무한대를 포함하는 조건과 연결된다.

### Stability

BIBO stable LTI 시스템은 impulse response가 절대합 가능해야 한다. z 변환 관점에서는 ROC가 단위원을 포함해야 한다.

```text
stable <-> ROC includes |z| = 1
```

causal하고 stable한 rational DT LTI 시스템은 모든 pole이 단위원 안에 있어야 한다.

## LCCDE와 system function

차분방정식

```text
Σ[k=0,N] ak y[n-k] = Σ[k=0,M] bk x[n-k]
```

의 z 변환은

```text
H(z) = (Σ[k=0,M] bk z-k) / (Σ[k=0,N] ak z-k)
```

이다. 이 식을 통해 block diagram, cascade, parallel, feedback 구조를 대수적으로 분석할 수 있다.

## 피드백 연결

전방 경로 `H1(z)`와 피드백 경로 `H2(z)`가 있으면 전체 system function은 일반적으로

```text
H(z) = H1(z) / (1 + H1(z)H2(z))
```

형태가 된다. 피드백은 pole 위치를 바꿀 수 있으므로 안정성 분석에서 특히 중요하다.

## 단측 z 변환

단측 z 변환은

```text
X+(z) = Σ[n=0,∞] x[n]z-n
```

로 정의하며, nonzero initial condition이 있는 causal 차분방정식을 풀 때 유용하다. 양방향 z 변환은 시스템 성질을 보기에 좋고, 단측 z 변환은 실제 초기 조건 문제를 풀기에 좋다.

## 연결 노트

- [연속시간 푸리에 변환](04-continuous-time-fourier-transform.md)
- [이산시간 푸리에 변환](05-discrete-time-fourier-transform.md)
- [샘플링](07-sampling.md)

## 복습 질문

- $$X(s)=1/[(s+1)(s+3)]$$을 ROC $$\text{Re}\{s\}>-1$$로 역변환해 $$x(t)$$를 구하고, initial value theorem으로 검산할 수 있는가?
- 라플라스 변환이 CTFT보다 강력한 이유(수렴 안 하는 신호도 다룰 수 있는 이유)를 $$x(t)e^{-\sigma t}$$ 관점으로 설명할 수 있는가?
- causal하고 stable한 시스템의 pole 위치 조건을 s-plane과 z-plane 각각에서 설명할 수 있는가?

{% endraw %}

---

이전: [07. 샘플링](07-sampling.md)
