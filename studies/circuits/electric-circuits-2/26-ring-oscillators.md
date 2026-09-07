---
layout: page
title: "26. Ring Oscillators - 링 발진기"
permalink: /studies/circuits/electric-circuits-2/26-ring-oscillators/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/26%20Ring%20Oscillators%20-%20%EB%A7%81%20%EB%B0%9C%EC%A7%84%EA%B8%B0.md)

{% raw %}
## 핵심 요약

발진기는 입력 없이 주기적인 출력을 만드는 회로이다. feedback loop가 특정 주파수에서 한 바퀴 돌아온 신호의 magnitude를 1 이상, phase를 0도 또는 360도로 만들면 oscillation이 가능하다. Ring oscillator는 odd number inverter 또는 amplifier stage를 loop로 연결해 지연과 phase shift로 발진을 만든다.

## Oscillator 정의

oscillator:

```text
input 없이 periodic output을 만드는 회로
```

MOS 회로에서는 amplifier와 feedback network를 이용한다.

## Barkhausen 조건

loop transfer가 `H(jw)`일 때 발진 조건:

```text
|H(jw_osc)| = 1
angle H(jw_osc) = 0 deg 또는 360 deg
```

강의에서는 한 바퀴 돌아온 신호가 in-phase이고 같은 magnitude가 되어야 한다고 설명한다.

실제로는 startup을 위해 처음에는 loop gain이 1보다 약간 커야 하고, large-signal nonlinearity가 amplitude를 제한한다.

## 단일 CS Stage

CS stage 하나:

```text
H(s) = -gm (RD || 1/(sCD))
```

하나의 CS만으로는 필요한 phase/magnitude 조건을 동시에 만족시키기 어렵다.

## 두 개의 CS Stage

두 개의 CS는 DC에서 phase가 360도에 가깝지만 pole phase shift와 magnitude 조건 때문에 안정적인 oscillation 조건을 만들기 어렵다.

## 세 개의 CS Stage

세 개의 CS stage는 각 stage가 60도 정도의 추가 phase shift를 제공해 전체 조건을 만족할 수 있다.

각 stage:

```text
H_stage(jw) = -gm RD / (1 + jw RD CD)
```

3-stage ring에서:

```text
3 tan^-1(w RD CD) = pi
```

oscillation frequency:

```text
w_osc ≈ sqrt(3) / (RD CD)
```

loop gain 조건은 이 주파수에서 magnitude가 1 이상이어야 한다.

## CMOS Inverter Ring Oscillator

실제 CMOS inverter ring oscillator는 출력이 rail-to-rail로 swing하므로 small-signal linear analysis가 정확하지 않다.

특징:

- oscillation은 noise가 시작한다.
- MOSFET은 항상 saturation에 있지 않다.
- large-signal simulation이 필요하다.
- clock signal 생성에 충분한 non-sinusoidal waveform을 만든다.

## N-Stage Ring Oscillator

odd number inverter ring oscillator의 주파수:

```text
fosc = 1 / (2 N TD)
```

여기서:

- `N`: stage 수
- `TD`: 한 stage의 propagation delay

예: 3-stage이면:

```text
fosc = 1 / (6 TD)
```

## Differential Ring Oscillator

differential amplifier stage를 이용하면 even-number stage도 가능하다.

특징:

- CMOS inverter chain보다 빠를 수 있다.
- 하지만 static current 때문에 power consumption이 증가한다.

## VCO

Voltage-Controlled Oscillator는 control voltage로 oscillation frequency를 조절하는 oscillator이다.

ring oscillator에서 frequency control 방법:

- current control
- load capacitance control
- delay cell bias control

## 시험 포인트

- Barkhausen 조건을 magnitude/phase로 설명한다.
- ring oscillator는 odd number inversion과 delay로 발진한다.
- CMOS inverter ring은 large-signal 회로라 small-signal 해석만으로 부족하다.
- `fosc = 1/(2NTD)`를 기억한다.
- differential ring oscillator는 faster but more power라는 trade-off가 있다.

## 같이 보면 좋은 노트

- [Feedback - 음귀환 기초](21-feedback.md)
- [LC Oscillators - LC 발진기](27-lc-oscillators.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **26. Ring Oscillators - 링 발진기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **26. Ring Oscillators - 링 발진기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [25. IO Resistance Improvement with Feedback 2 - 증폭기별 저항 개선](25-io-resistance-improvement-with-feedback-2.md) · 다음: [27. LC Oscillators - LC 발진기](27-lc-oscillators.md)
