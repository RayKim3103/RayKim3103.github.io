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

{% endraw %}

---

이전: [18. Filters with Inductor Simulator - 인덕터 시뮬레이터 필터](18-filters-with-inductor-simulator.md) · 다음: [20. Higher-Order Filters - Butterworth 고차 필터](20-higher-order-filters-butterworth.md)
