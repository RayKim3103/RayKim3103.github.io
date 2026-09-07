---
layout: page
title: "19. Filters with Integrators - KHN Tow-Thomas Biquad"
permalink: /studies/circuits/electric-circuits-2/19-filters-with-integrators-khn-tow-thomas-biquad/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/19%20Filters%20with%20Integrators%20-%20KHN%20Tow-Thomas%20Biquad.md)

{% raw %}
## 핵심 요약

이 강의는 op-amp integrator를 이용해 하나의 active circuit에서 HP, BP, LP, AP 2차 필터를 얻는 biquad 구조를 다룬다. KHN(Kerwin-Huelsman-Newcomb) biquad는 high-pass output을 만들고 이를 적분해 band-pass, 다시 적분해 low-pass를 만든다. Tow-Thomas biquad도 component 선택으로 다양한 2차 응답을 구현한다.

## Integrator

op-amp integrator:

```text
Vo/Vi = -1/(sRC)
```

정의:

```text
w0 = 1/RC
```

적분기는 biquad filter의 핵심 building block이다.

## Biquadratic Filter

2차 표준 denominator:

```text
D(s) = s^2 + (w0/Q)s + w0^2
```

하나의 회로에서 numerator를 다르게 취하면 LP, HP, BP를 모두 얻을 수 있다.

## KHN Biquad 아이디어

KHN biquad는 다음 세 출력을 동시에 제공한다.

- `Vhp`: high-pass output
- `Vbp`: band-pass output
- `Vlp`: low-pass output

관계:

```text
Vbp = - (w0/s) Vhp
Vlp = - (w0/s) Vbp
```

즉 HP를 한 번 적분하면 BP, 두 번 적분하면 LP가 된다.

## Weighted Sum으로 HP 만들기

`Vhp`는 입력과 feedback된 `Vbp`, `Vlp`의 weighted sum으로 만든다.

개념:

```text
Vhp = K Vi - (1/Q) Vbp - Vlp
```

resistor ratio를 조절하여 `K`, `Q`, `w0`를 설정한다.

## KHN의 장점

- 한 회로에서 LP/HP/BP를 동시에 얻는다.
- `Q`, `w0`, gain을 resistor/capacitor ratio로 조절한다.
- active filter라 loading effect가 작다.

## All-Pass 구현

all-pass는 HP, BP, LP의 linear combination으로 만든다.

2차 all-pass 표준형:

```text
H_AP(s) = [s^2 - (w0/Q)s + w0^2] /
          [s^2 + (w0/Q)s + w0^2]
```

따라서:

```text
Vap = Vhp - (1/Q)Vbp + Vlp
```

와 같은 형태의 가중합을 만들면 all-pass 응답을 얻을 수 있다.

## Tow-Thomas Biquad

Tow-Thomas biquad도 integrator와 summing amplifier를 이용한 2차 active filter이다.

특징:

- component 선택으로 LP, HP, BP, AP 구현 가능
- `R`, `C`, `Q`, `R1`, `R2`, `R3`, `r` 등을 조절해 numerator와 denominator 설정
- KHN과 마찬가지로 integrator 기반 active filter

## 시험 포인트

- integrator transfer `-1/(sRC)`를 기억한다.
- KHN biquad에서 HP -> 적분 -> BP -> 적분 -> LP 흐름을 이해한다.
- AP는 HP/BP/LP linear combination으로 만든다.
- biquad는 2차 denominator를 직접 구현하는 active filter building block이다.

## 같이 보면 좋은 노트

- [First-Order Filters - 1차 필터](16-first-order-filters-1.md)
- [Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md)
- [Higher-Order Filters - Butterworth 고차 필터](20-higher-order-filters-butterworth.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **19. Filters with Integrators - KHN Tow-Thomas Biquad**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **19. Filters with Integrators - KHN Tow-Thomas Biquad**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터](18-filters-with-inductor-simulator.md) · 다음: [20. Higher-Order Filters - Butterworth 고차 필터](20-higher-order-filters-butterworth.md)
