---
layout: page
title: "02. MOS 소자 모델과 비이상성"
permalink: /studies/circuits/cmos-ic/02-devices-mos/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit/lecture_notes/02%20Devices%20-%20MOS%20%EC%86%8C%EC%9E%90%20%EB%AA%A8%EB%8D%B8%EA%B3%BC%20%EB%B9%84%EC%9D%B4%EC%83%81%EC%84%B1.md)

{% raw %}
tags: #cmos-integrated-circuit #mosfet #device #iv-characteristics #capacitance #leakage #noise-margin

관련 노트: [CMOS 집적회로 개요](01-introduction-cmos.md), [지연 모델과 Logical Effort](03-speed-logical-effort.md)

## 핵심 요약

이 장은 MOSFET의 I-V characteristic, capacitance, leakage, velocity saturation, channel length modulation, body effect, process variation, noise margin을 다룬다. 회로 설계자가 transistor를 이상적인 switch가 아니라 유한 저항과 parasitic capacitance, leakage를 가진 물리 소자로 이해해야 함을 강조한다.

## MOS Capacitor와 Terminal

Gate와 body는 oxide로 분리된 capacitor를 이룬다. Source와 drain은 diffusion terminal이며, nMOS에서는 관례적으로 더 낮은 전압 쪽을 source로 본다. 기본 nMOS 분석에서는 body와 source를 0 V에 둔다.

## nMOS 동작 영역

### Cutoff

```text
Vgs < Vt
```

Channel이 형성되지 않아 이상적으로는 drain current가 0이다. 실제로는 subthreshold leakage가 흐른다.

### Linear 또는 Triode

```text
Vgs > Vt
Vds < Vgs - Vt
```

Channel이 source부터 drain까지 이어져 resistor처럼 동작한다. Switch-level RC model에서는 ON transistor를 저항으로 근사한다.

### Saturation

```text
Vgs > Vt
Vds >= Vgs - Vt
```

Drain 쪽 channel이 pinch-off되어 current가 Vds에 덜 민감해진다. Digital gate switching 중에는 transistor가 linear와 saturation을 오가므로 평균 저항 모델을 사용한다.

## I-V Model

이상적인 long-channel nMOS 모델에서는 linear와 saturation current를 gate overdrive `Vgs - Vt`와 `Vds`의 함수로 표현한다. 실제 deep submicron device에서는 velocity saturation과 channel length modulation 때문에 단순 square-law와 차이가 생긴다.

## Capacitance

MOSFET에는 여러 capacitance가 존재한다.

| Capacitance | 위치 | 영향 |
|---|---|---|
| Gate capacitance | gate-oxide-channel | 입력 부하, switching power |
| Diffusion capacitance | source/drain-body junction | 출력 node 부하, delay |
| Overlap capacitance | gate와 source/drain overlap | Miller 효과와 coupling |

Gate capacitance는 transistor width가 커지면 증가한다. Length가 길어져도 gate 면적이 커져 capacitance가 증가한다. VDD 자체가 gate capacitance 값을 직접 키우지는 않지만, 충방전 에너지 `CVDD^2`를 크게 만든다.

## Switch-Level RC Model

Digital timing에서는 transistor를 ON resistance와 capacitance로 근사한다.

- Unit nMOS: 저항 `R`, capacitance `C`
- Unit pMOS: mobility가 낮아 같은 width에서는 nMOS보다 저항이 큼
- pMOS width를 키워 nMOS와 rise/fall delay를 맞춘다.

이 모델은 SPICE만큼 정확하지 않지만, gate sizing과 delay intuition을 빠르게 얻는 데 유용하다.

## Nonideal I-V Effects

### Velocity Saturation

강한 lateral electric field에서 carrier velocity가 더 이상 선형으로 증가하지 않고 포화된다. Short-channel device에서는 saturation current가 long-channel square-law 예측보다 덜 증가한다.

### Channel Length Modulation

Saturation 영역에서도 Vds가 증가하면 effective channel length가 줄어 drain current가 조금 증가한다. Analog 관점에서는 output resistance가 유한해지는 원인이다.

### Body Effect

Source-body 전압이 변하면 threshold voltage가 변한다. nMOS body가 GND에 고정되어 있고 source가 올라가면 `Vt`가 증가해 transistor가 약해진다.

## Leakage Sources

| Leakage | 원인 | 특징 |
|---|---|---|
| Subthreshold leakage | `Vgs < Vt`에서도 약한 inversion current | `Vgs`, `Vt`, 온도에 지수적으로 민감 |
| Gate leakage | 얇은 oxide를 통한 tunneling | thin oxide에서 증가, nMOS가 더 클 수 있음 |
| Junction leakage | reverse-biased p-n junction | drain-body 전압과 junction 면적/둘레에 영향 |

Scaling으로 oxide가 얇아지고 threshold가 낮아지면서 leakage는 점점 중요해졌다.

## Process Variation

실제 transistor 특성은 공정 변화로 달라진다.

- effective channel length
- threshold voltage
- oxide thickness
- nMOS/pMOS mobility
- temperature와 supply voltage

설계자는 corner simulation으로 worst-case speed, leakage, noise margin을 확인한다.

## Noise Margin

Logic level은 이상적인 0과 1만이 아니라 noise tolerance를 가진다.

| 기호 | 의미 |
|---|---|
| `VOH` | 출력 high로 보장되는 최소 전압 |
| `VOL` | 출력 low로 보장되는 최대 전압 |
| `VIH` | 입력 high로 인식되는 최소 전압 |
| `VIL` | 입력 low로 인식되는 최대 전압 |

```text
NMH = VOH - VIH
NML = VIL - VOL
```

Noise margin을 크게 하려면 logic level이 충분히 rail-to-rail에 가깝고 switching threshold가 균형 있게 잡혀야 한다.

## 시험ㆍ복습 체크포인트

- Cutoff, linear, saturation 영역 조건을 쓸 수 있어야 한다.
- Gate capacitance와 diffusion capacitance가 delay와 power에 주는 영향을 설명할 수 있어야 한다.
- Subthreshold, gate, junction leakage의 차이를 구분해야 한다.
- Velocity saturation과 body effect의 의미를 이해해야 한다.
- `VOH`, `VOL`, `VIH`, `VIL`, `NMH`, `NML`을 정의할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. MOS 소자 모델과 비이상성**를 다루며, MOS device 특성에서 CMOS gate, delay, power, interconnect, sequential timing까지 회로와 물리 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회로 주제에서는 DC 동작점, small-signal 모델, 주파수 응답, feedback 효과를 순서대로 분리한다.
- gain 식은 부호, loading, output resistance, capacitance가 들어가는 위치를 회로 노드에서 추적한다.
- 디지털 회로도 transistor와 wire RC 위에서 동작하므로 noise margin, delay, power를 함께 봐야 한다.
- logical effort는 복잡한 gate delay를 parasitic과 electrical effort로 나누어 설계 감각을 준다.
- scaling은 성능 이득과 함께 leakage, variability, reliability 문제를 키운다.

### 문제 풀이 또는 구현 루틴

- pull-up/pull-down network, 논리 기능, 최악 경로, load capacitance를 순서대로 확인한다.
- delay 계산은 equivalent resistance와 switched capacitance를 잡고 Elmore/RC 근사로 시작한다.
- sequential timing은 setup, hold, clock skew, contamination/propagation delay를 따로 계산한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- static CMOS에서 PUN과 PDN은 dual network여야 한다.
- dynamic power는 activity factor와 capacitance에 민감해 단순 주파수만으로 결정되지 않는다.
- setup violation과 hold violation은 고치는 방향이 다르다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 최악 delay path는 어디인가?
- 전력 중 switching, short-circuit, leakage 중 무엇이 지배적인가?
- 공정/전압/온도 변화가 margin을 얼마나 줄이는가?
- **02. MOS 소자 모델과 비이상성**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. CMOS 집적회로 개요](01-introduction-cmos.md) · 다음: [03. 지연 모델과 Logical Effort](03-speed-logical-effort.md)
