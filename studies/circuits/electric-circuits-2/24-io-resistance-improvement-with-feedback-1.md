---
layout: page
title: "24. IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백"
permalink: /studies/circuits/electric-circuits-2/24-io-resistance-improvement-with-feedback-1/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/24%20IO%20Resistance%20Improvement%20with%20Feedback%201%20-%20%EC%A0%84%EC%95%95%20%EC%A6%9D%ED%8F%AD%EA%B8%B0%20%ED%94%BC%EB%93%9C%EB%B0%B1.md)

{% raw %}
## 핵심 요약

negative feedback은 gain desensitization과 bandwidth extension뿐 아니라 input/output resistance도 이상적인 증폭기 조건에 가깝게 만든다. 전압 증폭기에서는 input resistance를 키우고 output resistance를 줄이는 방향으로 개선된다.

## Feedback 기본식

closed-loop gain:

```text
A_cl = A / (1 + KA)
```

loop gain:

```text
T = KA
```

feedback이 강할수록 gain은 줄지만 회로 특성은 안정화된다.

## 전압 증폭기의 이상적 저항

voltage amplifier:

```text
Rin -> infinity
Rout -> 0
```

negative voltage-voltage feedback은 이 방향으로 저항을 개선한다.

## Input Resistance 개선

feedback 없는 경우:

```text
Rin,open = 1/gm
```

feedback 있는 경우:

```text
Rin,closed = Rin,open (1 + KA)
```

직관:

- 입력 test current가 들어오면 feedback이 input voltage 변화를 줄인다.
- 같은 current에 대해 더 큰 effective resistance처럼 보인다.

## Output Resistance 개선

feedback 없는 경우:

```text
Rout,open = RD
```

feedback 있는 경우:

```text
Rout,closed = Rout,open / (1 + KA)
```

직관:

- output voltage가 흔들리면 feedback이 amplifier를 통해 반대 방향 전류를 만든다.
- test voltage에 대해 더 큰 test current가 흐르므로 effective output resistance가 작아진다.

## 전압 증폭기 요약

| 항목 | feedback 효과 |
|---|---|
| gain | `1/(1+KA)`만큼 감소 |
| input resistance | `(1+KA)`만큼 증가 |
| output resistance | `(1+KA)`만큼 감소 |
| bandwidth | `(1+KA)`만큼 증가 |

## 시험 포인트

- 전압 증폭기 negative feedback은 `Rin` 증가, `Rout` 감소를 만든다.
- 개선 비율은 loop gain `1+KA`이다.
- closed-loop gain, input resistance, output resistance를 같은 loop gain 관점에서 정리한다.

## 같이 보면 좋은 노트

- [Feedback - 음귀환 기초](21-feedback.md)
- [IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선](25-io-resistance-improvement-with-feedback-2.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **24. IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **24. IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [23. Project Design Guide - TIA CTLE 설계 가이드](23-project-design-guide-tia-ctle.md) · 다음: [25. IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선](25-io-resistance-improvement-with-feedback-2.md)
