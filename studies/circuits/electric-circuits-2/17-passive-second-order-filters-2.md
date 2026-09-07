---
layout: page
title: "17. Passive Second-Order Filters - 수동 2차 필터"
permalink: /studies/circuits/electric-circuits-2/17-passive-second-order-filters-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/17%20Passive%20Second-Order%20Filters%20-%20%EC%88%98%EB%8F%99%202%EC%B0%A8%20%ED%95%84%ED%84%B0.md)

{% raw %}
## 핵심 요약

2차 필터는 두 개의 pole을 가지며, complex conjugate pole을 사용하면 더 sharp한 frequency response와 resonance를 얻을 수 있다. 핵심 파라미터는 natural frequency `w0`와 quality factor `Q`이다. LP, HP, BP, AP 필터는 같은 denominator를 공유하고 numerator의 차이에 의해 기능이 달라진다.

## 왜 고차 필터가 필요한가

고차 필터의 장점:

- cutoff가 더 sharp하다.
- 더 다양한 magnitude/phase 응답을 만들 수 있다.
- Butterworth, Chebyshev, elliptic 같은 표준 응답을 구현할 수 있다.

## 2차 필터의 표준형

complex pole을 가진 2차 denominator:

```text
D(s) = s^2 + (w0/Q)s + w0^2
```

pole:

```text
p = -w0/(2Q) ± j w0 sqrt(1 - 1/(4Q^2))
```

`Q > 1/2`이면 complex conjugate pole이고 resonance가 나타날 수 있다.

## Damping Factor

감쇠비 `zeta`와 Q 관계:

```text
zeta = 1/(2Q)
```

분류:

- `zeta < 1`: under-damped
- `zeta = 1`: critically damped
- `zeta > 1`: over-damped

## Passive Second-Order LP Filter

RLC 기반 LP filter:

```text
H_LP(s) = w0^2 / [s^2 + (w0/Q)s + w0^2]
```

파라미터:

```text
w0 = 1/sqrt(LC)
Q = w0 R C
```

물리적 의미:

- `w0`: capacitor와 inductor 사이 energy transfer frequency
- `Q`: damping이 얼마나 작은지, resonance가 얼마나 sharp한지

## HP Filter

HP numerator는 `s^2`이다.

```text
H_HP(s) = s^2 / [s^2 + (w0/Q)s + w0^2]
```

특징:

- low frequency 차단
- high frequency 통과
- low frequency에서 `+40 dB/dec` slope

## BP Filter

BP numerator는 `(w0/Q)s` 형태이다.

```text
H_BP(s) = (w0/Q)s / [s^2 + (w0/Q)s + w0^2]
```

특징:

- peak at `w0`
- 3-dB bandwidth:

```text
BW = w0 / Q
```

Q가 클수록 passband가 좁고 sharp하다.

## All-Pass Filter

2차 all-pass는 pole과 mirror-image zero를 배치해 magnitude를 일정하게 유지하고 phase만 변화시킨다.

```text
H_AP(s) = [s^2 - (w0/Q)s + w0^2] /
          [s^2 + (w0/Q)s + w0^2]
```

특징:

- magnitude constant
- phase shift는 frequency에 따라 변함
- 고차 all-pass일수록 더 큰 phase shift 가능

## 시험 포인트

- 2차 denominator `s^2 + (w0/Q)s + w0^2`를 기억한다.
- `Q > 1/2`이면 complex pole이다.
- LP/HP/BP/AP는 numerator로 구분한다.
- BP bandwidth는 `w0/Q`이다.
- Q가 클수록 peaking과 sharpness가 커진다.

## 같이 보면 좋은 노트

- [First-Order Filters - 1차 필터](16-first-order-filters-1.md)
- [Filters with Inductor Simulator - 인덕터 시뮬레이터 필터](18-filters-with-inductor-simulator.md)
- [Higher-Order Filters - Butterworth 고차 필터](20-higher-order-filters-butterworth.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **17. Passive Second-Order Filters - 수동 2차 필터**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회로 주제에서는 DC 동작점, small-signal 모델, 주파수 응답, feedback 효과를 순서대로 분리한다.
- gain 식은 부호, loading, output resistance, capacitance가 들어가는 위치를 회로 노드에서 추적한다.
- midband gain만으로 평가하지 말고 input/output resistance, pole-zero, swing, noise, stability를 함께 봐야 한다.
- feedback은 gain을 희생해 bandwidth, 선형성, 저항 특성, 공정 민감도를 조절하는 도구다.
- filter와 oscillator는 s-domain pole 위치가 시간응답, 주파수응답, 안정성을 결정한다.

### 문제 풀이 또는 구현 루틴

- DC operating point, small-signal model, midband gain, pole-zero, feedback loop 순서로 해석한다.
- spec을 gain, bandwidth, noise, power, area, swing 제약으로 나누어 trade-off를 적는다.
- LTspice 결과는 operating point, AC response, transient response를 같은 회로에서 순차 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- Miller effect를 놓치면 high-frequency pole을 크게 잘못 예측한다.
- loop gain과 closed-loop gain을 섞으면 feedback 회로 해석이 흐려진다.
- oscillator는 loop gain뿐 아니라 phase 조건과 amplitude stabilization이 필요하다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 dominant pole은 어느 node capacitance와 resistance가 만드는가?
- feedback이 입력/출력 저항을 어느 방향으로 바꾸는가?
- spec 하나를 개선할 때 power, noise, swing, stability 중 무엇이 나빠지는가?
- **17. Passive Second-Order Filters - 수동 2차 필터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [16. First-Order Filters - 1차 필터](16-first-order-filters-1.md) · 다음: [18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터](18-filters-with-inductor-simulator.md)
