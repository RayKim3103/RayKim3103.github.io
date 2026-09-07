---
layout: page
title: "20. Higher-Order Filters - Butterworth 고차 필터"
permalink: /studies/circuits/electric-circuits-2/20-higher-order-filters-butterworth/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/20%20Higher-Order%20Filters%20-%20Butterworth%20%EA%B3%A0%EC%B0%A8%20%ED%95%84%ED%84%B0.md)

{% raw %}
## 핵심 요약

고차 필터는 1차/2차 필터보다 더 가파른 cutoff와 다양한 응답 특성을 제공한다. 이 강의는 Butterworth low-pass filter를 중심으로, ripple 없는 maximally flat passband와 pole 위치를 구하는 방법을 설명한다. 고차 Butterworth 필터는 1차 섹션과 2차 섹션의 cascade로 구현한다.

## 필터 종류 비교

| 필터 | passband ripple | stopband ripple | cutoff |
|---|---|---|---|
| Butterworth | 없음 | 없음 | 느림 |
| Chebyshev | 있음 또는 없음 | 유형에 따라 | 중간/빠름 |
| Elliptic | 있음 | 있음 | 빠름 |

Butterworth는 ripple이 없는 대신 cutoff가 가장 완만한 편이다.

## Butterworth Magnitude

N차 Butterworth LPF:

```text
|H(jw)|^2 = 1 / [1 + (w/wp)^(2N)]
```

`wp`는 cutoff frequency이며, 이 주파수에서 3-dB drop이 발생한다.

```text
|H(jwp)| = 1/sqrt(2)
```

## Pole 위치

Butterworth pole은 반지름 `wp`인 원 위에 균일하게 배치되고, 안정성을 위해 left-half plane pole만 선택한다.

개념:

```text
sk = wp exp[j theta_k]
```

left-half plane pole만 사용하여 stable transfer function을 만든다.

## N = 1

1차 Butterworth:

```text
H(s) = wp / (s + wp)
```

이는 일반적인 1차 low-pass filter이다.

## N = 2

2차 Butterworth는 complex conjugate pole 한 쌍을 갖는다.

표준형:

```text
H(s) = w0^2 / [s^2 + (w0/Q)s + w0^2]
```

Butterworth 조건:

```text
w0 = wp
Q = 1/sqrt(2) ≈ 0.707
```

## N = 3

3차 Butterworth는 다음 cascade로 구현할 수 있다.

```text
1차 Butterworth LPF
+
2차 LPF with Q = 1
```

즉 real pole 하나와 complex pole pair 하나를 cascade한다.

## N차 구현 전략

N차 Butterworth 필터는 다음 섹션들의 곱으로 구현한다.

- N이 홀수: 1차 section 하나 + 2차 sections
- N이 짝수: 2차 sections만

각 2차 section은 서로 다른 Q를 갖고 같은 cutoff scale을 공유한다.

## 설계 절차

1. passband/stopband specification에서 필요한 order `N`을 구한다.
2. cutoff `wp`를 정한다.
3. Butterworth pole 위치를 계산한다.
4. 1차/2차 section으로 factorization한다.
5. op-amp active filter 또는 passive filter로 각 section을 구현한다.

## 시험 포인트

- Butterworth는 passband가 maximally flat이고 ripple이 없다.
- `|H(jw)|^2 = 1/[1+(w/wp)^(2N)]` 형태를 기억한다.
- 2차 Butterworth의 `Q = 1/sqrt(2)`가 중요하다.
- 고차 필터는 1차/2차 section cascade로 구현한다.

## 같이 보면 좋은 노트

- [Passive Second-Order Filters - 수동 2차 필터](17-passive-second-order-filters-2.md)
- [Filters with Integrators - KHN Tow-Thomas Biquad](19-filters-with-integrators-khn-tow-thomas-biquad.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **20. Higher-Order Filters - Butterworth 고차 필터**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **20. Higher-Order Filters - Butterworth 고차 필터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [19. Filters with Integrators - KHN Tow-Thomas Biquad](19-filters-with-integrators-khn-tow-thomas-biquad.md) · 다음: [21. Feedback - 음귀환 기초](21-feedback.md)
