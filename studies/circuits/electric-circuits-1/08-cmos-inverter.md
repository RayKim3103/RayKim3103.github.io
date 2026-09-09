---
layout: page
title: "08. CMOS 인버터"
permalink: /studies/circuits/electric-circuits-1/08-cmos-inverter/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/08%20CMOS%20%EC%9D%B8%EB%B2%84%ED%84%B0.md) · 교재: Razavi Ch.15 / Weste & Harris

{% raw %}
## 한눈에 보기

디지털 회로의 기본 블록 CMOS 인버터. **저항 부하 인버터의 한계** → NMOS+PMOS 상보 구조가 전력·속도에서 유리한 이유.

```text
인버터 기능 → 저항 부하 NMOS 인버터 → power/speed tradeoff
→ CMOS inverter → VTC → delay → dynamic power → CMOS logic gates
```

---

## 1. 인버터의 역할

| 입력 | 출력 |
|---:|---:|
| 0 ($\approx$ 0 V) | 1 ($\approx V_{DD}$) |
| 1 ($\approx V_{DD}$) | 0 ($\approx$ 0 V) |

NAND, NOR, sequential logic, processor의 기본 building block. 성능 지표 = **속도(delay), 전력, noise margin, 면적**.

## 2. 저항 부하 NMOS 인버터와 그 한계

위에 $R_D$, 아래 NMOS.
- 입력 0 → NMOS off → 출력 $V_{DD}$
- 입력 1 → NMOS on → 출력 ≈ 0

### Static power 문제
NMOS가 켜져 있을 때 $V_{DD}$ → $R_D$ → NMOS로 **DC 전류가 상시**:
$$
I_D \approx \frac{V_{DD}}{R_D} \quad(\text{출력 low일 때})
$$
전력 줄이려면 $R_D$ ↑.

### Speed 문제
출력 노드에 load capacitance $C_L$. low→high 전이는 $R_D C_L$ 1차 응답:
$$
V_{out}(t) = V_{final} + (V_{initial} - V_{final})e^{-t/(R_D C_L)}
$$
95% 도달 시간 $\approx 3 R_D C_L$ → 빠르게 하려면 $R_D$ ↓.

→ **power(작은 $R_D$ 불가) ↔ speed(큰 $R_D$ 불가)** 정면 충돌. + 출력 high가 $V_{DD}$까지 못 감(레벨 저하), $R_D$가 면적 큼.

---

## 3. CMOS 인버터

위 **PMOS**(pull-up), 아래 **NMOS**(pull-down), gate 공통 입력.

| 입력 | NMOS | PMOS | 출력 | DC 전류 |
|---|---|---|---|---|
| 0 | off | on | $V_{DD}$ | ≈ 0 |
| 1 | on | off | 0 | ≈ 0 |

정상 상태에서 **$V_{DD}$→GND 직접 경로 없음** → **static power ≈ 0** (leakage만). 출력 **rail-to-rail**.

## 4. 전달특성 (VTC)

$V_{in}$ 낮음 → $V_{out}$ 높음; $V_{in}$ 올라가면 어느 지점에서 급격히 하강; $V_{in}$ 높음 → $V_{out}$ 낮음.

| 구간 | NMOS | PMOS |
|---|---|---|
| $V_{in} < V_{th,n}$ | cutoff | triode | 
| 중간 (전이) | sat → triode | triode → sat | 
| $V_{in} > V_{DD}-\|V_{th,p}\|$ | triode | cutoff |

**Switching threshold (trip point) $V_M$** = $V_{in} = V_{out}$인 점. 두 소자 모두 saturation, 전류 같다고 놓으면:
$$
V_M = \frac{V_{th,n} + \sqrt{\beta_p/\beta_n}\,(V_{DD} - |V_{th,p}|)}{1 + \sqrt{\beta_p/\beta_n}}, \qquad \beta = \mu C_{ox}\frac{W}{L}
$$
$\beta_n = \beta_p$ (보통 $W_p \approx 2\text{–}3\,W_n$)이면 $V_M \approx V_{DD}/2$ → 대칭, noise margin 최대.

VTC 기울기 −1 점에서 $V_{IL}, V_{IH}$ → $NM_L = V_{IL}-V_{OL}$, $NM_H = V_{OH}-V_{IH}$.

## 5. 속도 (Propagation Delay)

출력 전이 = $C_L$ 충/방전:
- low→high: **PMOS**가 $C_L$ 충전 → $t_{PLH} \propto R_{on,p}C_L$
- high→low: **NMOS**가 $C_L$ 방전 → $t_{PHL} \propto R_{on,n}C_L$

MOSFET on 저항:
$$
R_{on} \approx \frac{1}{\mu C_{ox}\frac{W}{L}(V_{DD} - V_{th})}
$$
(더 정밀한 식은 $V_{th}/V_{DD}$ 항 포함.) 핵심: **$R_{on}$ 작고 $C_L$ 작을수록 빠름**. $W$↑ → $R_{on}$↓(빠름) 이지만 자기 gate capacitance·면적↑ → fan-out chain에서 최적 sizing 존재 ([logical effort](../cmos-ic/03-delay-models-and-logical-effort.md)).

$W_p \approx 2\text{–}3\,W_n$으로 잡으면 $t_{PLH} \approx t_{PHL}$ (대칭 delay).

## 6. Power Dissipation

### Dynamic (switching)
$C_L$ 한 번 충전 시 전원 공급 에너지 $C_L V_{DD}^2$ (절반은 $C_L$에 저장 $\frac12 C_L V_{DD}^2$, 절반은 PMOS 저항에서 열). 방전 시 저장분이 NMOS에서 소모.

$$
P \approx f\,C_L V_{DD}^2 \quad\xrightarrow{\text{activity factor}}\quad \boxed{P_{dyn} = \alpha\,C_L V_{DD}^2 f}
$$

- $\propto C_L$, $\propto f$, $\propto V_{DD}^2$ → **CMOS scaling에서 $V_{DD}$ 낮추기가 가장 강력**.
- $\alpha$ = 한 clock에 실제 전이가 일어나는 확률.

### Short-circuit
입력 전이 중 짧은 구간 NMOS·PMOS 동시 on → $V_{DD}$→GND 펄스 전류. 입력 slew가 느리면 커짐 (보통 전체의 <10%).

### Static
정상 상태 leakage (subthreshold + gate) $\times V_{DD}$. scaling으로 $V_{th}$↓ 하며 중요해짐.

## 7. CMOS 논리 게이트

PMOS pull-up network(PUN) + NMOS pull-down network(PDN), 서로 **dual**.

### NOR (하나라도 1이면 0)
- NMOS **병렬** (하나라도 켜지면 pull-down)
- PMOS **직렬** (모든 입력 0일 때만 pull-up)

| X | Y | NOR |
|---:|---:|---:|
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 0 |

### NAND (모두 1일 때만 0)
- NMOS **직렬** (모두 켜질 때만 pull-down)
- PMOS **병렬** (하나라도 0이면 pull-up)

| X | Y | NAND |
|---:|---:|---:|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### OR / AND
CMOS는 **inverting gate가 자연스러움** → OR = NOR + 인버터, AND = NAND + 인버터.
직렬 소자는 저항이 더해지므로 (pMOS 직렬인) **NOR가 NAND보다 느리다** → NAND 선호.

## 8. 설계 감각

| 선택 | 효과 |
|---|---|
| 큰 $W$ | $R_{on}$↓ (빠름), capacitance·면적↑ |
| 작은 $W$ | capacitance·면적↓, 느림 |
| 낮은 $V_{DD}$ | power↓, 속도·noise margin↓ |
| 작은 $C_L$ | 빠름 + 저전력 (둘 다 유리) |

---

## 핵심 정리

- 저항 부하 NMOS 인버터: **power ↔ speed** 정면 trade-off + 레벨 저하 + 면적.
- CMOS 인버터: 상보 구조 → 정상 상태 DC 경로 없음 → **static power ≈ 0**, 출력 rail-to-rail.
- Switching threshold $V_M$: $\beta_n = \beta_p$ ($W_p \approx 2\text{–}3W_n$)이면 $V_{DD}/2$, noise margin 최대.
- Delay $\propto R_{on}C_L$; $t_{PLH}$(PMOS 충전), $t_{PHL}$(NMOS 방전).
- $P_{dyn} = \alpha C_L V_{DD}^2 f$ + short-circuit + static leakage.
- NAND/NOR = PUN/PDN dual 구조로 자연 구현; NOR가 더 느림.

## 복습 질문

- 저항 부하 NMOS 인버터의 power–speed trade-off를 $R_D$로 설명하고, CMOS가 이를 어떻게 푸는가?
- 입력 0 / 1일 때 NMOS·PMOS 상태와 출력, 그리고 static power가 거의 없는 이유는?
- $V_M$ 식에서 $\beta_p/\beta_n = 1$로 만드는 sizing과, 그때 $V_M$·noise margin은?
- $t_{PLH}$와 $t_{PHL}$이 각각 어느 소자로 결정되는가? $W$를 키울 때의 trade-off는?
- $P_{dyn} = \alpha C_L V_{DD}^2 f$ 각 항의 의미와, 왜 $V_{DD}$ 축소가 가장 효과적인가?
- NAND와 NOR의 NMOS/PMOS 직렬·병렬 구성과, NOR가 더 느린 이유는?
{% endraw %}

---

이전: [07. 캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
