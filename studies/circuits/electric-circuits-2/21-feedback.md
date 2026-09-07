---
layout: page
title: "21. Feedback - 음귀환 기초"
permalink: /studies/circuits/electric-circuits-2/21-feedback/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/21%20Feedback%20-%20%EC%9D%8C%EA%B7%80%ED%99%98%20%EA%B8%B0%EC%B4%88.md)

{% raw %}
## 핵심 요약

Feedback은 output의 일부를 input으로 되돌리는 구조이다. negative feedback에서는 closed-loop gain이 open-loop gain `A`보다 작아지지만, gain이 feedback factor에 의해 안정화되고 bandwidth가 증가한다. 핵심 식은 `A/(1+KA)`이며, `KA`가 loop gain이다.

## Feedback Block Diagram

open-loop amplifier:

```text
Y = A F
```

feedback:

```text
F = X - K Y
```

closed-loop:

```text
Y/X = A / (1 + KA)
```

여기서:

- `A`: open-loop gain
- `K`: feedback factor
- `KA`: loop gain

## Negative Feedback 조건

negative feedback이면 output이 input error를 줄이는 방향으로 되돌아온다.

`KA > 0`이고 크면:

```text
Y/X ≈ 1/K
F = X - KY ≈ 0
```

즉 amplifier 입력 error가 거의 0이 된다.

## Gain Desensitization

open-loop gain `A`가 transistor parameter에 민감해도 closed-loop gain은:

```text
A_cl = A / (1+KA)
```

`KA >> 1`이면:

```text
A_cl ≈ 1/K
```

따라서 gain이 resistor ratio 같은 passive component에 의해 결정된다.

## Op-Amp Feedback 예

op-amp가 large gain을 가지면:

```text
V+ ≈ V-
```

non-inverting amplifier:

```text
Vo/Vs = 1 + R2/R1
```

inverting amplifier:

```text
Vo/Vs = -Rf/Rin
```

## CS with Degeneration as Feedback

source degeneration도 local negative feedback이다.

CS open-loop:

```text
A ≈ -gm RD
```

feedback factor는 source resistor `RS`에 의해 생긴다.

결과:

```text
Av = -gm RD / (1 + gm RS)
```

`gmRS >> 1`이면:

```text
Av ≈ -RD/RS
```

gain이 `gm` 변화에 덜 민감해진다.

## Bandwidth Extension

single-pole open-loop gain:

```text
A(s) = A0 / (1 + s/wp)
```

feedback 적용:

```text
A_cl(s) = A0 / (1 + KA0 + s/wp)
```

closed-loop bandwidth:

```text
wp,cl = (1 + KA0) wp
```

즉 gain은 줄지만 bandwidth는 loop gain만큼 증가한다. gain-bandwidth product는 대략 일정하게 유지된다.

## Feedback Polarity 판단

feedback 회로는 output 변화가 amplifier input error를 줄이는지 확인해 negative/positive를 판단한다.

절차:

1. input을 조금 증가시킨다.
2. feedforward output 변화를 본다.
3. feedback path가 input error를 줄이면 negative feedback이다.
4. error를 더 키우면 positive feedback이며 불안정해질 수 있다.

## 시험 포인트

- closed-loop gain `A/(1+KA)`를 기억한다.
- loop gain `KA`가 클수록 gain은 `1/K`에 가까워진다.
- feedback은 gain desensitization과 bandwidth extension을 제공한다.
- source degeneration은 local negative feedback이다.
- polarity는 output이 input error를 줄이는지로 판단한다.

## 같이 보면 좋은 노트

- [OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)
- [IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백](24-io-resistance-improvement-with-feedback-1.md)
- [IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선](25-io-resistance-improvement-with-feedback-2.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **21. Feedback - 음귀환 기초**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **21. Feedback - 음귀환 기초**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [20. Higher-Order Filters - Butterworth 고차 필터](20-higher-order-filters-butterworth.md) · 다음: [22. LTSpice Tutorial - 시뮬레이션 튜토리얼](22-ltspice-tutorial.md)
