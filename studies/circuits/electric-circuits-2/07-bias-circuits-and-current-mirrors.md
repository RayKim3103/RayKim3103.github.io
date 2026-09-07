---
layout: page
title: "07. Bias Circuits and Current Mirrors - 바이어스와 전류미러"
permalink: /studies/circuits/electric-circuits-2/07-bias-circuits-and-current-mirrors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/07%20Bias%20Circuits%20and%20Current%20Mirrors%20-%20%EB%B0%94%EC%9D%B4%EC%96%B4%EC%8A%A4%EC%99%80%20%EC%A0%84%EB%A5%98%EB%AF%B8%EB%9F%AC.md)

{% raw %}
## 핵심 요약

MOS amplifier의 small-signal parameter는 bias current와 gate-source voltage에 의해 결정된다. 따라서 원하는 동작점을 만들고 유지하는 bias circuit이 필수이다. IC에서는 resistor보다 current mirror를 이용해 기준전류 `IREF`를 복사하고 scaling하는 방식이 자주 쓰인다.

## 왜 Bias가 필요한가

MOSFET 증폭기가 원하는 gain을 내려면 MOSFET이 saturation에 있어야 하고, `ID`, `VGS`, `gm`, `ro`가 적절해야 한다.

```text
gm = 2ID / VOV
ro ≈ 1 / (lambda ID)
```

따라서 DC operating point를 먼저 정한 뒤 small-signal을 얹는다.

## Gate Biasing

저항 divider로 gate voltage를 고정할 수 있다.

```text
VGS = VDD * R2 / (R1 + R2)
```

입력 신호는 coupling capacitor를 통해 넣는다.

## Coupling Capacitor

capacitor impedance:

```text
ZC = 1 / (j omega C)
```

동작:

- DC에서는 open
- 관심 주파수에서 충분히 큰 `omega C`이면 short처럼 동작

따라서 DC bias는 유지하면서 AC input만 증폭기에 전달할 수 있다. 단, coupling network가 input resistance를 낮추고 low-frequency pole을 만든다.

## Current Mirror

IC에서는 current mirror를 이용해 기준전류를 복사한다.

```text
Icopy = IREF * [(W/L)copy / (W/L)ref]
```

전제:

- 두 MOSFET이 saturation
- threshold와 process가 잘 matching
- channel-length modulation 무시 또는 작음

## Current Mirror의 장점

- 하나의 `IREF`로 여러 bias current 생성 가능
- transistor size ratio로 current scaling 가능
- IC에서 resistor보다 면적/정밀도 측면에서 유리

## Current Mirror의 제한

### Output Compliance

copy transistor가 saturation을 유지해야 하므로 output voltage 범위에 제한이 있다.

NMOS current sink:

```text
VO >= VGS - VTH = VOV
```

### Channel-Length Modulation

`VO`가 변하면 `VDS`가 변하고, finite `ro` 때문에 `IO`가 `IREF`와 달라진다.

```text
IO ≈ IREF + (VO - VGS) / ro
```

즉 ideal current source가 아니라 finite output resistance를 가진 current source이다.

## CS with Current Mirror Load

저항 load 대신 current mirror 또는 current source load를 사용하면:

- DC bias current를 안정적으로 설정
- load resistance를 크게 만들어 gain 증가
- IC 면적 감소

하지만 current mirror의 left side가 input resistance처럼 작용하거나, output voltage range가 제한되는 문제가 있을 수 있다.

## 시험 포인트

- MOS small-signal parameter가 bias에 의존한다는 점을 기억한다.
- coupling capacitor는 DC open, AC short 근사이다.
- current mirror current ratio는 `(W/L)` ratio로 결정된다.
- saturation compliance와 channel-length modulation이 current mirror 오차의 핵심이다.
- current source load는 큰 output resistance로 gain을 높인다.

## 같이 보면 좋은 노트

- [MOS Large-Signal Characteristics - MOS 대신호 특성](02-mos-large-signal-characteristics.md)
- [Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md)
- [Differential Amplifiers 1 - 차동 증폭기 기본](08-differential-amplifiers-1.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. Bias Circuits and Current Mirrors - 바이어스와 전류미러**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **07. Bias Circuits and Current Mirrors - 바이어스와 전류미러**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Cascode Amplifier - 캐스코드 증폭기](06-cascode-amplifier.md) · 다음: [08. Differential Amplifiers 1 - 차동 증폭기 기본](08-differential-amplifiers-1.md)
