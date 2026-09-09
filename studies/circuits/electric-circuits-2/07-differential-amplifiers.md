---
layout: page
title: "07. Differential Amplifiers — DM/CM · CMRR · Active Load"
permalink: /studies/circuits/electric-circuits-2/07-differential-amplifiers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `08 Differential Amplifiers 1` + `09 Differential Amplifiers 2` **통합**

{% raw %}
## 개요

MOS differential pair는 두 입력의 **차이** $V_{in1}-V_{in2}$에 반응하고 **공통** noise를 억제한다. 해석은 **differential mode(DM)**와 **common mode(CM)**로 분해. DM은 대칭성으로 **half-circuit**, CM은 **tail current source의 finite resistance**가 핵심. symmetry를 깨는 mismatch·mirror action이 CMRR·gain에 영향.

```text
differential pair → DM/CM 분해 → half-circuit → Adm, Acm → CMRR
→ mismatch → active load (current mirror)
```

---

# Part 1. Differential Pair 기본

## 1. 구조

입력 $V_{G1}, V_{G2}$, 출력 $V_{out} = V_{D1} - V_{D2}$. tail current $I_{SS}$.

대칭: $V_{in1} = V_{in2} \Rightarrow V_{out} = 0$.  $V_{in1} > V_{in2} \Rightarrow I_{D1}\uparrow, V_{out1}\downarrow$.

## 2. 왜 Differential Pair인가

- 두 입력에 동시에 들어오는 **noise cancel**
- op-amp 입력단에 적합
- analog IC의 matching·symmetry 활용
- SNR 향상

## 3. Large-Signal

tail current $I_{SS}$가 두 트랜지스터로 나뉜다.
- $V_{in1} = V_{in2}$: $I_{D1} = I_{D2} = I_{SS}/2$
- $V_{in1}\uparrow$: $I_{D1}\uparrow$, $I_{D2}\downarrow$
- 충분히 큰 differential input: 한쪽이 $I_{SS}$ 거의 전부 (**차동 입력이 $\pm\sqrt{2}\,V_{OV}$를 넘으면 완전 스위칭**)

linear region은 $V_{in1} \approx V_{in2}$ 근처의 작은 차동 입력.

## 4. DM/CM 분해

$$
V_{CM} = \frac{V_{in1}+V_{in2}}{2}, \qquad V_{id} = V_{in1}-V_{in2}
$$
$$
V_{in1} = V_{CM} + \frac{V_{id}}{2}, \qquad V_{in2} = V_{CM} - \frac{V_{id}}{2}
$$
선형 영역에서 DM 응답과 CM 응답을 **superposition**.

## 5. Differential Mode 해석

DM에서 회로는 **anti-symmetric** → 가운데 tail node가 **AC ground** (virtual ground) → 각 절반 = CS amplifier (half-circuit):
$$
\frac{V_{out}}{V_{id}} = -g_m R_D \;\;(\text{$r_o$ 무시}), \qquad
A_{dm} = -g_m(R_D \parallel r_o)
$$

## 6. Common Mode 해석

CM 입력에서 두 입력이 같이 움직인다.

**Ideal tail current source**: $V_{out1} = V_{out2}$ → **differential output CM gain = 0**.

**Finite tail resistance $R_{SS}$**: half-circuit에서 각 절반은 source degeneration $2R_{SS}$가 있는 CS처럼 해석. single-ended output이 변할 수 있다.

## 7. Input Common-Mode Range (ICMR)

입력 CM voltage가 너무 크거나 작으면 saturation이 깨진다.
- **상한**: input transistor의 drain–source saturation 조건 → $V_{CM,max} \approx V_{DD} - I_D R_D + V_{TH}$
- **하한**: tail current source가 정상 동작할 headroom

설계 시 확인: input pair saturation, tail source saturation, output swing.

---

# Part 2. CMRR · Mismatch · Active Load

## 8. Single-Ended Output

한쪽 drain만 output으로 쓰면 common-mode 성분이 완전히 사라지지 않는다.

$$
A_{dm}\text{(single-ended)} \approx -\frac{1}{2}g_m R_D
$$
$$
A_{cm} \approx -\frac{R_D}{2R_{SS} + 1/g_m} \;\xrightarrow{g_m R_{SS}\gg1}\; -\frac{R_D}{2R_{SS}}
$$

## 9. CMRR

$$
\text{CMRR} = \left|\frac{A_{dm}}{A_{cm}}\right| \approx g_m R_{SS} \;\;(\text{single-ended})
$$
tail current source의 output resistance $R_{SS}$가 클수록 **common-mode rejection** 좋아짐 → cascode current source tail이 유리.

## 10. Component Mismatch

좌우 대칭이 깨지면 **CM 입력이 DM 출력으로 변환**된다.

저항 mismatch $R_{D2} = R_D + \Delta R_D$:
$$
A_{cm\to dm} \propto \frac{\Delta R_D}{R_{SS}}
$$
반면 differential-mode gain은 작은 저항 mismatch에 1차적으로 크게 영향받지 않는다. (mismatch는 CMRR을 떨어뜨리는 주범 — offset voltage도 발생.)

## 11. Active-Loaded Differential Amplifier

저항 load 대신 **PMOS current mirror active load**.

| 장점 | |
|---|---|
| 저항 없이 구현 | matching 좋음 |
| 면적 작음 | **single-ended output**을 자연스럽게 |
| 큰 output resistance | → high gain |

## 12. Half-Circuit 근사의 한계

active load에서는 current mirror action 때문에 단순 half-circuit이 정확하지 않다. mirror가 **short-circuit transconductance를 2배로** 키우는 효과 → factor of 2 차이.

강의 핵심 해석:
$$
G_m \approx -g_{mN}, \qquad R_{out} \approx r_{oN} \parallel r_{oP}, \qquad A_v \approx -G_m R_{out} = -g_{mN}(r_{oN} \parallel r_{oP})
$$
(정밀 해석에서는 mirror action으로 $G_m \approx -g_{mN}$이 아니라 유효 $g_m$이 up되어, 두 입력의 전류가 모두 출력에 더해진다.)

---

## 핵심 정리

- 입력을 $V_{CM}$, $V_{id}$로 분해 → DM/CM superposition.
- DM half-circuit: tail node = AC ground → $A_{dm} = -g_m(R_D \parallel r_o)$.
- Ideal tail source면 differential-output CM gain = 0. Finite $R_{SS}$면 single-ended에서 $A_{cm} \approx -R_D/(2R_{SS})$.
- CMRR $\approx g_m R_{SS}$ (single-ended) → tail source $R_{SS}$ 클수록 좋음.
- Mismatch → CM을 DM으로 변환, CMRR·offset 악화.
- Active load (PMOS mirror): single-ended, high gain $-g_{mN}(r_{oN}\parallel r_{oP})$; current-mirror action으로 half-circuit에 factor-2 보정.

## 복습 질문

- DM half-circuit에서 tail node가 AC ground가 되는 이유는?
- ideal tail source에서 differential-output CM gain이 0인 이유, finite $R_{SS}$면 어떻게 되나?
- CMRR $\approx g_m R_{SS}$를 유도하고, mismatch가 CMRR을 떨어뜨리는 메커니즘은?
- active load의 current-mirror action이 half-circuit 해석에 factor 2 차이를 만드는 이유는?
{% endraw %}

---

이전: [06. Bias & Current Mirrors](06-bias-circuits-and-current-mirrors.md) · 다음: [08. Pole·Zero & Bode Plot](08-pole-zero-bode-plot.md)
