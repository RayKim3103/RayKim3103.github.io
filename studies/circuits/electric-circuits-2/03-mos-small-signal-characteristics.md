---
layout: page
title: "03. MOS Small-Signal Characteristics - MOS 소신호 모델"
permalink: /studies/circuits/electric-circuits-2/03-mos-small-signal-characteristics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/03%20MOS%20Small-Signal%20Characteristics%20-%20MOS%20%EC%86%8C%EC%8B%A0%ED%98%B8%20%EB%AA%A8%EB%8D%B8.md)

{% raw %}
## 핵심 요약

MOSFET의 saturation 영역 전류는 `VGS`에 비선형적으로 의존하지만, bias point 근처의 작은 변화는 선형화하여 voltage-controlled current source로 볼 수 있다. 이때 핵심 파라미터가 transconductance `gm`, output resistance `ro`, body transconductance `gmb`이다.

## 왜 Small-Signal Model을 쓰는가

MOSFET의 대신호 특성은 비선형이다. 하지만 특정 DC bias point 근처에서 작은 입력 변화만 고려하면 Taylor expansion의 1차항으로 근사할 수 있다.

```text
ID ≈ ID0 + gm * vgs
```

DC 성분과 small-signal 성분을 분리하면 회로 해석이 훨씬 쉬워진다.

## MOSFET as VCCS

saturation에서 MOSFET은 gate-source voltage로 drain current를 조절하는 voltage-controlled current source이다.

```text
id = gm vgs
```

이 전류가 load resistor를 흐르면 voltage amplification이 생긴다.

```text
vout = - gm vgs R
Av = vout / vgs = - gm R
```

## Transconductance `gm`

saturation current:

```text
ID = (1/2) mu_n Cox (W/L) (VGS - VTH)^2
```

정의:

```text
gm = dID / dVGS
```

대표 표현:

```text
gm = mu_n Cox (W/L) (VGS - VTH)
gm = 2ID / (VGS - VTH)
gm = sqrt(2 mu_n Cox (W/L) ID)
```

해석:

- `gm`은 `VGS - VTH`에 선형적으로 비례한다.
- `gm`은 `ID`의 제곱근에 비례한다.
- bias current와 transistor size가 small-signal gain을 결정한다.

## Small-Signal Circuit 작성 규칙

small-signal equivalent를 만들 때:

- DC voltage source는 AC ground
- DC current source는 open
- MOSFET은 `gm vgs` current source로 대체
- channel-length modulation을 고려하면 drain-source 사이에 `ro` 추가

## Channel-Length Modulation과 `ro`

channel-length modulation 포함:

```text
ID = (1/2) mu_n Cox (W/L) (VGS - VTH)^2 (1 + lambda VDS)
```

small-signal output resistance:

```text
ro = dVDS / dID ≈ 1 / (lambda ID)
```

`ro`가 finite이면 amplifier gain이 `RD`가 아니라 `RD || ro`에 의해 제한된다.

## Body Effect와 `gmb`

body voltage가 source와 다르면 drain current가 변한다.

small-signal에서는 body effect를 다음 current source로 표현한다.

```text
id_body = gmb vbs
gmb = chi gm
```

여기서 `chi`는 보통 `0.1 ~ 0.3` 수준으로 다룬다. 손계산에서는 자주 무시하지만 IC simulation에서는 중요하다.

## PMOS Small-Signal Model

PMOS도 NMOS와 같은 구조로 small-signal model을 만든다. 다만 전압 기준을 `VSG`, `VSD`로 잡고 current 방향을 주의한다.

PMOS transconductance:

```text
gm = mu_p Cox (W/L) (VSG - |VTH|)
gm = 2ID / (VSG - |VTH|)
```

small-signal model은 NMOS와 동일한 형태로 사용할 수 있으나, controlled current 방향과 node polarity를 일관되게 잡아야 한다.

## 시험 포인트

- `gm = dID/dVGS` 정의와 세 가지 표현을 모두 연결한다.
- DC source 처리 규칙을 기억한다.
- `ro ≈ 1/(lambda ID)`가 channel-length modulation에서 나온다.
- PMOS도 같은 model이지만 polarity가 반대임을 주의한다.
- body effect를 포함하면 `gmb vbs` current source가 추가된다.

## 같이 보면 좋은 노트

- [MOS Large-Signal Characteristics - MOS 대신호 특성](02-mos-large-signal-characteristics.md)
- [Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md)
- [MOSFET High-Frequency Model - 고주파 모델](11-mosfet-high-frequency-model.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. MOS Small-Signal Characteristics - MOS 소신호 모델**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **03. MOS Small-Signal Characteristics - MOS 소신호 모델**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. MOS Large-Signal Characteristics - MOS 대신호 특성](02-mos-large-signal-characteristics.md) · 다음: [04. Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md)
