---
layout: page
title: "07. 고급 CMOS Gates (Compound · Skewed · Dynamic · Domino)"
permalink: /studies/circuits/cmos-ic/07-advanced-cmos-gates/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `07 Gates — 고급 CMOS 게이트` 보강

{% raw %}
## 개요

기본 inverter/NAND/NOR를 넘어 **compound gate, asymmetric/symmetric gate, skewed gate, pseudo-nMOS, dynamic circuit, domino gate**. 목표: speed·area·power·noise margin의 tradeoff를 이해하고 **critical input / critical transition**에 맞춰 gate를 최적화.

---

## 1. Compound Gates

여러 논리 연산을 **한 개의 CMOS gate**로 합침. static CMOS는 **inverting gate 구현이 자연스러움** → AOI/OAI를 애용.

```text
Y = NOT(A·B + C·D)   →  AOI22 gate 한 개로 직접 구현
```

별도 AND·OR·INV를 여러 stage로 연결하는 것보다 **parasitic capacitance·stage delay 감소**.

| Gate | 의미 | 형태 |
|---|---|---|
| **AOI** | AND-OR-Invert | `Y = NOT(AB + CD + …)` |
| **OAI** | OR-AND-Invert | `Y = NOT((A+B)(C+D)…)` |

PDN이 함수의 곱-합 구조를 그대로 반영 (AOI: nMOS는 AB를 series, 그 그룹들을 parallel), PUN은 그 dual.

### Compound Gate의 Logical Effort
입력 위치에 따라 **`g`와 parasitic delay가 다름**. series stack 안쪽 transistor가 늦게 switching하면 내부 node 충방전 경로가 달라져 delay 차이 발생.

직관:
- stage 수 ↓ → parasitic ↓
- 너무 복잡한 gate → transistor stack 길어져 `g` ↑ (보통 **stack 4개 이하** 권장)
- **critical input을 출력에 가까운(빠른) 위치**에 배치

---

## 2. Asymmetric vs Symmetric Gates

| | Asymmetric gate | Symmetric gate |
|---|---|---|
| 목표 | 특정 입력을 더 빠르게 | 모든 입력이 같은 electrical behavior |
| 방법 | transistor 크기·배치를 **비대칭** 조정 | 균형 설계 |
| 대가 | critical input은 빨라지나 **다른 입력 effort ↑**, 평균 특성 악화 | — |
| 언제 | 어떤 입력이 critical인지 확실할 때 | critical input을 모를 때 / 모든 timing 비슷할 때 |

---

## 3. Skewed Gates

rising **또는** falling 중 하나를 더 빠르게 하려고 **pMOS/nMOS 비율을 의도적으로** 변경.

| 종류 | 유리한 transition | 방법 |
|---|---|---|
| **HI-skew** | output rising | pMOS 강화 (또는 nMOS 축소) |
| **LO-skew** | output falling | nMOS 강화 (또는 pMOS 축소) |

noncritical transition을 희생해 **critical transition의 delay·capacitance**를 줄임. domino 설계에서 흔히 사용(HI-skew inverter).

### P/N Ratio
일반 inverter는 pMOS mobility가 낮아 nMOS보다 크게(≈2×). **평균 delay 최소 P/N**은 rise/fall equalization 목표 비율(≈2.5~3)보다 **작음**(≈1.5~2). 최적 비율은 delay뿐 아니라 **area·power**까지 함께 고려.

---

## 4. Pseudo-nMOS

pMOS pull-up을 **항상 ON**(gate를 GND에)으로 두고 **nMOS PDN만으로** 논리 구현 (ratioed logic).

| 장점 | 단점 |
|---|---|
| pMOS network 없음 → **input capacitance ↓** | 출력 0일 때 `V_DD`→GND **DC current** 상시 |
| **wide NOR** 등에서 빠르고 area 작음 | **static power** 큼 |
| | noise margin이 pull-up/pull-down 저항비(ratio)에 의존 |

이 static power 문제가 순수 nMOS logic을 몰아내고 **complementary CMOS**를 주류로 만든 역사적 이유.

---

## 5. Dynamic Circuits

**clocked pMOS로 precharge** → **evaluation phase**에서 nMOS network가 조건에 따라 dynamic node를 discharge.

| Phase | clk | 동작 |
|---|---|---|
| **Precharge** | 0 | pMOS ON → dynamic node를 `V_DD`로 충전, nMOS 경로는 footer nMOS로 차단 |
| **Evaluation** | 1 | pull-down 조건이 참이면 node를 GND로 방전, 거짓이면 high 유지 |

- pMOS PUN이 없어 **input capacitance·area ↓, 속도 ↑** (`g`가 static의 절반 수준).
- 단점: dynamic node가 floating → **leakage, noise, charge sharing에 취약**.

### Monotonicity 제약
evaluation 동안 dynamic gate **입력은 monotonically rising**만 허용, **출력은 high→low만** 가능. → dynamic gate가 dynamic gate를 직접 구동하면 다음 stage가 **입력이 1→0으로 떨어지는 것**을 보고 잘못 평가.

---

## 6. Domino Gates

dynamic stage 뒤에 **static inverter**(보통 HI-skew)를 붙임:

```text
dynamic gate (출력 high→low) → static inverter → domino 출력 (low→high, monotonically rising)
```

domino 출력이 rising-only → 다음 dynamic stage의 **monotonic input 조건 충족** → dynamic gate들을 cascade 가능.

## 7. Keeper

dynamic node는 evaluation 중 high면 floating → leakage로 전압 하강. **Keeper** = 약한 feedback pMOS로 high 유지.

| Keeper 세기 | 영향 |
|---|---|
| 강함 | noise margin ↑, 그러나 pull-down evaluation **느려짐**(keeper와 싸움) |
| 약함 | leakage·noise에 취약 |

## 8. Charge Sharing

evaluation 중 dynamic output node와 **내부 node들이 charge를 나눔** → output voltage droop. 이 droop이 다음 inverter의 switching threshold를 넘으면 **잘못된 출력**.

해결: 내부 node **precharge**(secondary precharge transistor), keeper 강화, gate 구조 단순화, sizing 조정.

## 9. Circuit Pitfalls

pseudo-nMOS, latch, domino/dynamic + latch 조합에서 **leakage, power supply noise, delay variation, back-gate/coupling** 문제 발생 가능. 고성능 회로일수록 이런 비이상성을 **layout·timing까지 함께** 고려.

---

## 시험·복습 체크포인트

- `Y = NOT(AB+CD)`를 AOI22 static CMOS network(PUN/PDN)로 그릴 수 있는가?
- Asymmetric gate와 skewed gate의 최적화 목표 차이는? HI-skew / LO-skew는 각각 어느 전이를 빠르게 하나?
- Pseudo-nMOS가 static power를 소모하는 이유와, 그럼에도 쓰는 경우는?
- Dynamic gate의 precharge/evaluation 동작과 **monotonicity 제약**을 설명할 수 있는가?
- Domino gate가 monotonic 제약을 어떻게 우회하는가? keeper·charge sharing의 역할과 tradeoff는?
{% endraw %}

---

이전: [06. Scaling · Reliability · Variability](06-scaling-reliability-and-variability.md) · 다음: [08. Datapaths](08-datapaths-adders-shifters-multipliers.md)
