---
layout: page
title: "27. LC Oscillators - LC 발진기"
permalink: /studies/circuits/electric-circuits-2/27-lc-oscillators/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/27%20LC%20Oscillators%20-%20LC%20%EB%B0%9C%EC%A7%84%EA%B8%B0.md)

{% raw %}
## 핵심 요약

LC oscillator는 capacitor와 inductor 사이의 energy exchange를 이용해 비교적 깨끗한 sinusoidal oscillation을 만든다. 이상적인 LC tank는 `1/sqrt(LC)`에서 무손실로 진동하지만, 실제 tank는 parasitic resistance 때문에 에너지를 잃는다. amplifier 또는 cross-coupled pair가 이 손실을 보상하면 지속 발진이 가능하다.

## Ring Oscillator의 한계

ring oscillator는 CMOS에서 구현이 쉽고 tuning range가 넓지만 frequency가 sharp하지 않고 phase noise가 클 수 있다.

따라서 더 깨끗한 oscillation이 필요하면 LC oscillator를 사용한다.

## LC Tank의 물리적 동작

capacitor energy:

```text
EC = (1/2) C V^2
```

inductor energy:

```text
EL = (1/2) L I^2
```

capacitor가 충전되어 있으면 capacitor 전압이 inductor current를 만들고, inductor current가 다시 capacitor를 반대 방향으로 충전한다.

진동 주파수:

```text
w0 = 1 / sqrt(LC)
```

## s-Domain LC Tank

parallel 또는 series tank impedance는 `s = ±j/sqrt(LC)`에 pole을 갖는다.

이상적인 경우:

```text
Q = infinite
```

즉 energy loss가 없으면 resonance가 무한히 sharp하다.

## 실제 LC Tank

실제 tank에는 parasitic resistance `R`이 존재한다.

결과:

- energy loss 발생
- pole이 left-half plane으로 이동
- resonance는 있지만 self-sustained oscillation은 되지 않음

Q:

```text
Q = w0 R C
```

## 손실 보상

oscillation을 유지하려면 active circuit이 tank loss를 보상해야 한다.

조건:

```text
gm R >= 1
```

강의 표현:

- `gm R = 1`이면 loss를 정확히 보상
- startup을 위해 실제로는 `gm R > 1` 필요
- amplitude가 커지면 nonlinear effect로 제한됨

## Cross-Coupled LC Oscillator

cross-coupled pair는 LC tank에 negative resistance를 제공한다.

개념:

```text
active pair supplies energy lost in R
LC tank determines oscillation frequency
```

처음에는 noise가 작은 differential perturbation을 만들고, `gm > 1/R`이면 `VX`, `VY`의 차이가 성장한다. 이후 tail current `ISS`와 transistor nonlinearity가 amplitude를 제한한다.

## LC vs Ring Oscillator

| 항목 | LC Oscillator | Ring Oscillator |
|---|---|---|
| phase noise | 작음 | 큼 |
| 주파수 sharpness | 좋음 | 낮음 |
| CMOS 구현 | inductor 때문에 어려움 | 쉬움 |
| 최대 주파수 | 높게 가능 | delay 제한 |
| tuning range | 좁음 | 넓음 |
| 면적 | 큼 | 작음 |

## 시험 포인트

- LC tank의 resonance frequency `1/sqrt(LC)`를 기억한다.
- 실제 tank는 parasitic resistance 때문에 loss가 있다.
- active circuit은 loss를 보상하는 negative resistance 역할을 한다.
- cross-coupled LC oscillator의 startup 조건은 `gmR > 1`로 이해한다.
- LC는 clean oscillation, ring은 구현 용이성과 tuning range가 장점이다.

## 같이 보면 좋은 노트

- [Ring Oscillators - 링 발진기](26-ring-oscillators.md)
- [Feedback - 음귀환 기초](21-feedback.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **27. LC Oscillators - LC 발진기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **27. LC Oscillators - LC 발진기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [26. Ring Oscillators - 링 발진기](26-ring-oscillators.md)
