---
layout: page
title: "16. First-Order Filters - 1차 필터"
permalink: /studies/circuits/electric-circuits-2/16-first-order-filters-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/16%20First-Order%20Filters%20-%201%EC%B0%A8%20%ED%95%84%ED%84%B0.md)

{% raw %}
## 핵심 요약

필터는 입력 신호의 frequency spectrum을 바꾸는 회로이다. 1차 필터는 하나의 real pole을 가지며, zero 위치에 따라 low-pass, high-pass, all-pass 특성을 만든다. passive RC filter는 단순하지만 gain이 고정되고 loading effect가 있으며, active filter는 op-amp를 이용해 gain 제어와 buffering을 제공한다.

## Filter 정의

```text
Vo(s) = H(s) Vi(s)
```

`H(s)`가 transfer function이다.

일반 형태:

```text
H(s) = a * product(s - z_i) / product(s - p_i)
```

filter order는 denominator 차수로 결정된다.

## First-Order Filter

일반 형태:

```text
H(s) = (a1 s + a0) / (s + w0)
```

pole/zero 위치가 magnitude와 phase 변화를 결정한다.

## Low-Pass Filter

RC low-pass:

```text
H(s) = 1 / (1 + sRC)
```

pole:

```text
wp = 1 / RC
```

특징:

- low frequency 통과
- high frequency 감쇠
- high frequency slope: `-20 dB/dec`

## High-Pass Filter

RC high-pass:

```text
H(s) = sRC / (1 + sRC)
```

zero:

```text
s = 0
```

pole:

```text
wp = 1 / RC
```

특징:

- DC 차단
- high frequency 통과
- low frequency slope: `+20 dB/dec`

## All-Pass Filter

all-pass filter는 magnitude는 일정하고 phase만 변화시킨다.

대표 형태:

```text
H(s) = (s - w0) / (s + w0)
```

또는 gain sign을 포함한 형태로 구현된다.

특징:

- magnitude는 1
- phase shift 제공
- time domain에서 delay line처럼 활용 가능

## Passive RC Filter의 한계

한계:

- fixed gain
- loading effect
- source/load resistance에 따라 pole이 변함

## Active Filter

op-amp를 사용하면:

- loading effect 감소
- gain control 가능
- LP/HP/AP 구현이 쉬움

예:

```text
inverting active LP gain = -Rf/Rin * 1/(1+sRC)
```

## 시험 포인트

- LP, HP, AP의 transfer function 형태를 구분한다.
- RC pole `1/RC`를 바로 계산한다.
- passive filter의 loading effect를 설명한다.
- active filter는 op-amp feedback으로 gain과 impedance를 제어한다.

## 같이 보면 좋은 노트

- [Pole Zero Bode Plot - 극점 영점 보드선도](10-pole-zero-bode-plot.md)
- [Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md)
- [Filters with Integrators - KHN Tow-Thomas Biquad](19-filters-with-integrators-khn-tow-thomas-biquad.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **16. First-Order Filters - 1차 필터**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **16. First-Order Filters - 1차 필터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [15. OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md) · 다음: [17. Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md)
