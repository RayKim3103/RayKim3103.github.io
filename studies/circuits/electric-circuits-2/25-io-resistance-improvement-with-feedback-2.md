---
layout: page
title: "25. IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선"
permalink: /studies/circuits/electric-circuits-2/25-io-resistance-improvement-with-feedback-2/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/25%20IO%20Resistance%20Improvement%20with%20Feedback%202%20-%20%EC%A6%9D%ED%8F%AD%EA%B8%B0%EB%B3%84%20%EC%A0%80%ED%95%AD%20%EA%B0%9C%EC%84%A0.md)

{% raw %}
## 핵심 요약

feedback이 input/output resistance에 주는 효과는 amplifier type과 sampling/mixing 방식에 따라 달라진다. 전압 증폭기, 전류 증폭기, transconductance amplifier, transimpedance amplifier는 각각 이상적인 input/output resistance가 다르므로, negative feedback은 그 이상 조건에 가까워지도록 저항을 증가 또는 감소시킨다.

## 증폭기별 이상 저항

| 유형 | 입력 | 출력 | 이상적 Rin | 이상적 Rout |
|---|---|---|---:|---:|
| Voltage amplifier | V | V | infinite | 0 |
| Current amplifier | I | I | 0 | infinite |
| Transconductance amplifier | V | I | infinite | infinite |
| Transimpedance amplifier | I | V | 0 | 0 |

## Voltage Amplifier

Voltage-voltage feedback:

```text
Av,closed = Av,open / (1 + KAv)
Rin,closed = Rin,open (1 + KAv)
Rout,closed = Rout,open / (1 + KAv)
```

전압 증폭기에 적합한 방향:

- input resistance 증가
- output resistance 감소

## Transconductance Amplifier

Current-voltage feedback:

```text
Gm,closed = Gm,open / (1 + K Gm,open)
Rin,closed = Rin,open (1 + K Gm,open)
Rout,closed = Rout,open (1 + K Gm,open)
```

transconductance amplifier의 이상 조건은:

- input voltage를 잘 받기 위해 `Rin` 큼
- output current source처럼 보이기 위해 `Rout` 큼

## Current Amplifier

Current-current feedback:

```text
Ai,closed = Ai,open / (1 + K Ai,open)
Rin,closed = Rin,open / (1 + K Ai,open)
Rout,closed = Rout,open (1 + K Ai,open)
```

current amplifier의 이상 조건:

- input resistance 작음
- output resistance 큼

## Transimpedance Amplifier

Voltage-current feedback:

```text
Rm,closed = Rm,open / (1 + K Rm,open)
Rin,closed = Rin,open / (1 + K Rm,open)
Rout,closed = Rout,open / (1 + K Rm,open)
```

transimpedance amplifier의 이상 조건:

- input current를 받기 위해 `Rin` 작음
- output voltage source처럼 보이기 위해 `Rout` 작음

## 회로 예 - Transconductance Feedback

differential amplifier + CS 구조에서 output current를 feedback voltage로 변환해 입력에 되돌린다.

효과:

- transconductance gain 감소
- output resistance 증가
- voltage-to-current amplifier에 더 적합한 특성

## 회로 예 - Current Amplifier

CG + CS 구조에서 output current 일부를 current로 feedback한다.

효과:

- current gain 감소
- input resistance 감소
- output resistance 증가

## 회로 예 - TIA

CG + CS와 큰 feedback resistor `RF`를 사용한 transimpedance amplifier에서는:

```text
Zt,open ≈ -gm RD RF
Zt,closed ≈ -RF   (large loop gain)
```

즉 feedback이 충분히 크면 transimpedance gain이 `RF`에 의해 안정적으로 결정된다.

## 시험 포인트

- feedback topology별로 `Rin`, `Rout`이 증가/감소하는 방향을 외우기보다 이상적인 증폭기 조건과 연결해 이해한다.
- voltage amplifier: `Rin up`, `Rout down`
- current amplifier: `Rin down`, `Rout up`
- transconductance amplifier: `Rin up`, `Rout up`
- transimpedance amplifier: `Rin down`, `Rout down`
- gain은 항상 loop gain만큼 감소한다.

## 같이 보면 좋은 노트

- [Feedback - 음귀환 기초](21-feedback.md)
- [IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백](24-io-resistance-improvement-with-feedback-1.md)
- [Project Design Guide - TIA CTLE 설계 가이드](23-project-design-guide-tia-ctle.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **25. IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **25. IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [24. IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백](24-io-resistance-improvement-with-feedback-1.md) · 다음: [26. Ring Oscillators - 링 발진기](26-ring-oscillators.md)
