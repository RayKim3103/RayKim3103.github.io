---
layout: page
title: "01. CMOS 집적회로 개요와 설계 흐름"
permalink: /studies/circuits/cmos-ic/01-cmos-overview-and-design-flow/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `01 Introduction — CMOS 집적회로 개요` 보강
- 교재: *CMOS VLSI Design: A Circuits and Systems Perspective* (Weste & Harris)

{% raw %}
## 개요

CMOS 집적회로 설계의 전체 지도. MOS transistor의 switch 동작 → complementary CMOS gate → pass transistor / transmission gate → latch·flip-flop → fabrication·layout → HDL 기반 design flow → verification·packaging 으로 이어지는 큰 흐름.

---

## 1. MOS Transistor 기본

MOS 구조 = **gate / oxide / body**가 만드는 capacitor. gate 전압이 body 표면(channel) 상태를 제어해 **source–drain 사이 전류 경로**를 만든다. 4-terminal 소자(gate, source, drain, body).

### nMOS

| Gate 전압 | 동작 |
|---|---|
| 낮음 (`Vgs < Vt`) | **OFF** — source–drain 경로 끊김 |
| 높음 (`Vgs > Vt`) | **ON** — electron inversion channel 형성 |

- **강한 0**(strong 0)을 잘 전달 → **pull-down network**에 적합.
- 출력을 GND로 완전히 당김. 하지만 1을 전달하면 `VDD − Vt`까지만 (threshold drop).

### pMOS

| Gate 전압 | 동작 |
|---|---|
| 낮음 | **ON** |
| 높음 | **OFF** |

- **강한 1**(strong 1)을 잘 전달 → **pull-up network**에 적합.
- 같은 width에서 nMOS보다 mobility(정공 이동도)가 낮아 **저항이 2~3배 큼** → pMOS를 더 넓게 잡는다.

---

## 2. 전원 전압과 Scaling

VDD는 technology scaling과 함께 계속 낮아졌다. 이유:
- **Reliability**: oxide가 얇아지고 device가 작아지면 높은 전압이 **breakdown, hot carrier, leakage**를 악화.
- **Low power**: dynamic power가 `VDD²`에 비례 → VDD를 낮추는 것이 가장 강력한 전력 절감.

대가: VDD를 낮추면 gate overdrive(`VDD − Vt`)가 줄어 **delay가 증가**한다 (자세히는 [03](03-delay-models-and-logical-effort.md), [04](04-power-and-low-power-design.md)).

---

## 3. Complementary CMOS Logic

pMOS **pull-up network(PUN)** + nMOS **pull-down network(PDN)**를 상보적으로 구성.

```text
어떤 입력 조합에서도 출력은 VDD 또는 GND 중 하나에만 강하게 연결
(둘 다 연결되면 short-circuit, 둘 다 안 되면 floating — 정적 CMOS에서는 발생하지 않음)
```

**PUN과 PDN은 서로 dual**: PDN에서 series → PUN에서 parallel, 그 반대도.

### Inverter
- nMOS: 입력 1 → ON → 출력을 GND로
- pMOS: 입력 0 → ON → 출력을 VDD로

### NAND (`Y = NOT(A·B)`)
출력이 0이 되려면 **모든 입력이 1** → nMOS **series**, pMOS **parallel**.

### NOR (`Y = NOT(A+B)`)
출력이 0이 되려면 **입력 중 하나만 1이면 됨** → nMOS **parallel**, pMOS **series**.
→ pMOS series는 저항이 크므로 **NOR가 NAND보다 느리다** → 실무에서 NAND를 선호.

### Compound Gate (AOI / OAI)
`Y = NOT(AB + CD)` 같은 AND-OR-Invert를 **한 개의 CMOS gate**로 구현.
- 장점: stage 수 ↓, parasitic capacitance ↓
- 단점: transistor stacking 깊어짐, 입력별 logical effort 달라짐 ([07](07-advanced-cmos-gates.md))

---

## 4. Pass Transistor와 Transmission Gate

| | Pass transistor | Transmission gate |
|---|---|---|
| 구조 | 단일 nMOS 또는 pMOS | nMOS ∥ pMOS + 상보 제어 신호 |
| nMOS 단독 | strong 0 전달, high는 `VDD − Vt`로 약화 | — |
| pMOS 단독 | strong 1 전달, low 전달 약화 | — |
| TG | — | **0과 1 모두 강하게 전달** |
| 용도 | 간단한 스위치 | **mux, latch, tristate** |

**주의**: TG는 입력 noise를 출력으로 그대로 전달 → 필요하면 뒤에 **restoring inverter**.

### Tristate와 Mux
TG(또는 tristate inverter)를 enable로 켜고 끄면 tristate buffer. 여러 개 조합 → mux.

---

## 5. Latch와 Flip-Flop

| 요소 | 동작 |
|---|---|
| **D latch** | clock **level**에 민감 — transparent(통과) / opaque(유지) |
| **D flip-flop** | clock **edge**에 민감 — master-slave latch 2개로 구성 |

이 과목은 latch/FF를 논리 기호가 아니라 **transistor-level storage element**로 본다 → clocking, pass device, feedback path, **setup/hold time**이 이후 timing 분석([09](09-sequential-circuits-and-timing.md))으로 이어진다.

---

## 6. CMOS Fabrication

silicon wafer 위에 **mask 공정**을 반복. 보통 **p-type substrate**에 nMOS를 만들고, pMOS body를 위해 **n-well** 형성. (twin-well / triple-well 변형 존재.)

주요 layer (아래 → 위):

| layer | 역할 |
|---|---|
| **diffusion / active** | source/drain, transistor 영역 |
| **polysilicon** | gate (self-aligned) |
| **contact** | diffusion/poly ↔ metal1 연결 |
| **metal interconnect** | 배선 (metal1, metal2, …) |
| **via** | metal ↔ metal 연결 |

---

## 7. Layout과 Design Rules

**Design rule** = fabrication 가능성을 보장하는 최소 폭·간격·겹침 규칙. **λ (feature size)로 normalize** → 공정이 바뀌어도 같은 layout 원리 적용 (scalable design rules).

**Standard cell layout 관례**:
- pMOS는 **위쪽**(n-well 안), nMOS는 **아래쪽**
- **vertical polysilicon gate** — 입력이 수직 poly로 들어옴
- **cell 높이 고정** → 옆으로 붙여 배치(abutment) 가능
- 전원 rail: 위 **VDD**, 아래 **GND**로 정렬 → 인접 cell과 rail 공유

---

## 8. Design Partitioning과 Design Flow

큰 SoC는 **계층적**으로 분해. 예: MIPS datapath → control, register file, ALU, memory interface.

일반적 흐름:

1. **HDL**로 기능 기술 (Verilog/VHDL)
2. **Synthesis** → gate-level netlist
3. **Standard cell library**로 **place & route**
4. **Layout → parasitic extraction → timing/power 검증**
5. **Fabrication → packaging → test**

---

## 9. Verification의 중요성

Fabrication은 느리고 비싸다. tape-out 후 오류 발견 시 손실이 크다(재설계 + 재제작 수개월). 그래서 전체 chip 설계 effort의 큰 비중이 검증:

- **DRC** (Design Rule Check) — layout이 공정 규칙 만족?
- **LVS** (Layout vs Schematic) — layout이 netlist와 일치?
- **SPICE simulation** — 회로 동작·타이밍
- **STA** (Static Timing Analysis) — 모든 경로의 setup/hold
- **Functional verification** — RTL 기능 정확성

---

## 시험·복습 체크포인트

- nMOS/pMOS가 각각 strong 0 / strong 1을 전달하는 이유는?
- NAND와 NOR의 PUN/PDN 구성을 그리고, NOR가 더 느린 이유를 pMOS series로 설명할 수 있는가?
- Pass transistor와 transmission gate의 차이, TG 뒤에 restoring inverter가 필요한 이유는?
- Standard cell layout에서 pMOS/nMOS 배치와 전원 rail 관례는?
- HDL → fabrication 까지 design flow를 순서대로, DRC/LVS/STA의 역할과 함께 설명할 수 있는가?
{% endraw %}

---

다음: [02. MOS 소자 모델과 비이상성](02-mos-device-models-and-nonidealities.md)
