---
layout: page
title: "03. 지연 모델과 Logical Effort"
permalink: /studies/circuits/cmos-ic/03-speed-logical-effort/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit/lecture_notes/03%20Speed%20-%20%EC%A7%80%EC%97%B0%20%EB%AA%A8%EB%8D%B8%EA%B3%BC%20Logical%20Effort.md)

{% raw %}
tags: #cmos-integrated-circuit #delay #rc-model #elmore-delay #logical-effort #gate-sizing

관련 노트: [MOS 소자 모델과 비이상성](02-devices-mos.md), [Low Power Techniques](03-1-low-power-techniques.md)

## 핵심 요약

이 장은 CMOS gate의 속도를 propagation delay, contamination delay, RC delay model, Elmore delay, logical effort로 분석한다. 핵심은 transistor와 wire를 저항/커패시턴스로 근사해 delay를 빠르게 추정하고, multistage path에서 stage 수와 gate size를 선택하는 방법을 배우는 것이다.

## Delay 정의

| 기호 | 의미 |
|---|---|
| `tpdr` | rising propagation delay |
| `tpdf` | falling propagation delay |
| `tpd` | 평균 propagation delay |
| `tcdr` | rising contamination delay |
| `tcdf` | falling contamination delay |
| `tcd` | 평균 contamination delay |

```text
tpd = (tpdr + tpdf) / 2
tcd = (tcdr + tcdf) / 2
```

Propagation delay는 출력이 최종적으로 바뀌는 늦은 경우를 나타내고, contamination delay는 입력 변화가 출력에 처음 영향을 주기 시작하는 빠른 경우를 나타낸다.

## Delay Estimation

SPICE simulation이 가장 정확하지만, 초기 설계에서는 RC model로 빠르게 delay를 추정한다.

기본 가정:

- Unit nMOS는 resistance `R`, capacitance `C`
- Unit pMOS는 mobility가 낮아 더 큰 resistance를 가짐
- 더 넓은 transistor는 resistance가 작아지지만 capacitance가 커짐

Inverter fanout-of-1 delay를 기준 단위로 삼아 다른 gate delay를 비교한다.

## Elmore Delay

Pull-up 또는 pull-down network를 RC ladder로 모델링하면 Elmore delay를 사용할 수 있다.

```text
t_Elmore = sum_i R_common_i * C_i
```

각 capacitor `C_i`에 대해, 입력 source에서 그 capacitor까지 공유되는 resistance를 곱해 모두 더한다. Elmore delay는 정확한 waveform 해석보다 단순하지만 RC tree의 지연 직관을 잘 제공한다.

## Delay Components

Logical effort 모델에서 gate delay는 두 부분으로 나뉜다.

```text
d = f + p
f = g h
```

| 기호 | 의미 |
|---|---|
| `g` | logical effort, 같은 drive를 내는 inverter 대비 입력 capacitance |
| `h` | electrical effort, `Cout / Cin` |
| `f` | effort delay 또는 stage effort |
| `p` | parasitic delay |
| `d` | normalized stage delay |

## Logical Effort

Logical effort는 특정 gate가 inverter와 비교해 같은 output drive를 만들기 위해 얼마나 큰 input capacitance를 요구하는지 나타낸다.

일반적으로:

- NAND는 NOR보다 빠르다.
- NOR는 pMOS series 때문에 logical effort와 delay가 커지기 쉽다.
- Gate input마다 diffusion position과 stack 위치에 따라 parasitic delay가 다를 수 있다.

## FO4 Inverter

FO4는 fanout-of-4 inverter delay를 의미한다. Digital design에서 process-independent delay 단위처럼 자주 쓰인다.

```text
FO4 inverter: h = 4, g = 1
d = gh + p
```

FO4 delay는 서로 다른 공정이나 설계의 속도를 비교하는 rough metric으로 유용하다.

## Multistage Logical Effort

여러 gate path의 전체 effort는 다음 요소로 구성된다.

| 항목 | 의미 |
|---|---|
| Path logical effort `G` | 각 stage의 logical effort 곱 |
| Path electrical effort `H` | path 최종 load / path 입력 capacitance |
| Branching effort `B` | path 밖으로 갈라지는 load 영향 |
| Path effort `F` | `F = G B H` |

Path parasitic delay는 각 stage parasitic delay의 합이다.

## 최적 Stage Effort

N개 stage path에서 delay가 최소가 되려면 각 stage가 비슷한 effort를 부담하는 것이 좋다.

```text
f_hat = F^(1/N)
D = N*f_hat + P
```

실무적으로 stage effort가 약 4 근처일 때 빠른 경우가 많다. Delay는 최적 stage 수나 size에서 약간 벗어나도 크게 악화되지 않는 편이다.

## Gate Sizing 절차

1. Path logical effort `G`를 계산한다.
2. Electrical effort `H`를 계산한다.
3. Branch가 있으면 branching effort `B`를 포함한다.
4. `F = G B H`를 구한다.
5. Stage 수 `N`과 best stage effort를 정한다.
6. Load에서 입력 쪽으로 거꾸로 gate capacitance와 width를 계산한다.

## Logical Effort의 한계

- Delay model이 단순하다.
- Interconnect delay가 큰 회로에서는 반복적인 보정이 필요하다.
- 최소 delay를 주지만 최소 area 또는 최소 power를 보장하지 않는다.
- 매우 작은 공정에서는 velocity saturation, coupling, variation이 더 중요해진다.

## 시험ㆍ복습 체크포인트

- Propagation delay와 contamination delay의 차이를 설명할 수 있어야 한다.
- Elmore delay 계산 원리를 이해해야 한다.
- `d = gh + p`에서 `g`, `h`, `p`의 의미를 말할 수 있어야 한다.
- `F = GBH`와 best stage effort를 사용해 multistage path delay를 추정할 수 있어야 한다.
- NAND가 NOR보다 빠른 이유를 pMOS stack 관점에서 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. 지연 모델과 Logical Effort**를 다루며, MOS device 특성에서 CMOS gate, delay, power, interconnect, sequential timing까지 회로와 물리 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **03. 지연 모델과 Logical Effort**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. MOS 소자 모델과 비이상성](02-devices-mos.md) · 다음: [03. Low Power Techniques](03-1-low-power-techniques.md)
