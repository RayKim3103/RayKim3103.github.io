---
layout: page
title: "18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터"
permalink: /studies/circuits/electric-circuits-2/18-filters-with-inductor-simulator/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/18%20Filters%20with%20Inductor%20Simulator%20-%20%EC%9D%B8%EB%8D%95%ED%84%B0%20%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%ED%84%B0%20%ED%95%84%ED%84%B0.md)

{% raw %}
## 핵심 요약

IC에서 실제 inductor는 면적이 크고 특히 저주파용으로 구현하기 어렵다. 따라서 op-amp, resistor, capacitor를 이용해 inductor처럼 보이는 synthetic inductance를 만들 수 있다. 이 강의는 inductor-simulation circuit을 이용해 HP/BP/AP 등 2차 필터를 구현하는 방법을 설명한다.

## IC에서 Inductor가 어려운 이유

inductor 관계:

```text
v(t) = L di(t)/dt
V(s) = sL I(s)
```

저주파에서 큰 inductance가 필요하면 on-chip 면적이 매우 커진다. 그래서 active circuit으로 effective inductance를 만든다.

## Inductance-Simulation Circuit

자료의 핵심 결과:

```text
Leq = R1 R3 C4 R5 / R2
```

입력에서 본 impedance가:

```text
Zin ≈ s Leq
```

처럼 보이도록 op-amp 네트워크를 구성한다.

## Inductor를 대체한 2차 필터

수동 RLC 필터에서 `L`을 synthetic inductance로 대체하면 IC 친화적인 active filter가 된다.

HP filter 예:

```text
H_HP(s) = s^2 / [s^2 + (1/RC)s + 1/(LC)]
```

여기서 `L`에 `Leq`를 대입해 pole frequency와 Q를 조절한다.

## Band-Pass Filter

RLC band-pass도 synthetic inductor로 구현할 수 있다.

```text
H_BP(s) = (s/RC) / [s^2 + (1/RC)s + 1/(LC)]
```

center frequency:

```text
w0 = 1/sqrt(Leq C)
```

## All-Pass Filter 구현

2차 all-pass는 band-pass 출력 `T(s)`를 이용한 linear combination으로 만들 수 있다.

핵심 아이디어:

```text
Vout = 2 Vi T(s) - Vi
```

또는 op-amp summing 구조로 pole은 유지하고 zero를 mirror 위치에 배치한다.

## Grounded vs Floating Inductor

자료는 기본 inductor simulator가 grounded inductor임을 지적한다.

문제:

- 어떤 필터에서는 floating inductor가 필요하다.
- grounded inductor simulator만으로는 직접 대체가 안 될 수 있다.

이를 위해 generalized impedance converter(GIC)를 사용한다.

## Generalized Inductance-Simulation Circuit

GIC는 여러 impedance를 조합해 원하는 equivalent impedance를 만든다.

개념:

```text
Zin = product/ratio of several Z elements
```

적절히 resistor와 capacitor를 선택하면 floating 또는 grounded inductance-like impedance를 얻을 수 있다.

## 시험 포인트

- IC에서 physical inductor가 어려운 이유를 설명한다.
- `Leq = R1 R3 C4 R5 / R2` 형태의 synthetic inductance 개념을 이해한다.
- RLC filter에서 `L`을 synthetic inductor로 대체하면 active 2차 필터가 된다.
- grounded inductor와 floating inductor의 차이를 안다.
- GIC는 impedance 변환기로 이해한다.

## 같이 보면 좋은 노트

- [Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md)
- [Filters with Integrators - KHN Tow-Thomas Biquad](19-filters-with-integrators-khn-tow-thomas-biquad.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [17. Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md) · 다음: [19. Filters with Integrators - KHN Tow-Thomas Biquad](19-filters-with-integrators-khn-tow-thomas-biquad.md)
