---
layout: page
title: "02. MOS 소자 특성 — 대신호 · 소신호"
permalink: /studies/circuits/electric-circuits-2/02-mos-device-characteristics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `02 MOS Large-Signal Characteristics` + `03 MOS Small-Signal Characteristics` **통합**

{% raw %}
## 개요

MOSFET을 **대신호(large-signal) I–V** → **소신호(small-signal) model**로 이어서 복습. 대신호는 동작점을 정하고, 소신호는 그 동작점 주변을 선형화한 **VCCS**($g_m$, $r_o$, $g_{mb}$).

```text
MOS 구조 → cutoff/triode/saturation → I-V 식 → nonideal 효과(subthreshold, λ, body, 온도)
→ bias point 선형화 → gm, ro, gmb → small-signal 등가회로
```

---

# Part 1. Large-Signal Characteristics

## 1. MOSFET 구조

- **M**etal–**O**xide–**S**emiconductor **F**ET, NMOS / PMOS
- 수직: metal–oxide–semiconductor **capacitor**
- 수평: source–channel–drain **conduction path**

gate voltage가 threshold 이상이 되면 **inversion channel**이 형성되어 전류가 흐른다.

## 2. NMOS 동작 영역

| 영역 | 조건 | 전류 |
|---|---|---|
| **Cutoff** | $V_{GS} < V_{TH}$ | $I_D = 0$ (실제로는 subthreshold current) |
| **Triode** | $V_{GS} > V_{TH}$, $V_{DS} < V_{GS}-V_{TH}$ | $I_D = \mu_n C_{ox}\frac{W}{L}\left[(V_{GS}-V_{TH})V_{DS} - \frac{V_{DS}^2}{2}\right]$ |
| **Saturation** | $V_{GS} > V_{TH}$, $V_{DS} > V_{GS}-V_{TH}$ | $I_D = \frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{TH})^2$ |

- **Pinch-off**: $V_{GD} = V_{TH}$ 즉 $V_{DS} = V_{GS} - V_{TH}$ (triode↔saturation 경계, 두 식이 연속으로 이어짐)
- **Overdrive voltage** $V_{OV} = V_{GS} - V_{TH}$

## 3. PMOS 동작 영역

polarity 반대 — $V_{SG}$, $V_{SD}$, $|V_{TH}|$ 사용, drain current는 source에서 나가는 방향 기준.

| 영역 | 조건 |
|---|---|
| Cutoff | $V_{SG} < \|V_{TH}\|$ |
| Triode | $V_{SG} > \|V_{TH}\|$, $V_{SD} < V_{SG}-\|V_{TH}\|$ |
| Saturation | $V_{SG} > \|V_{TH}\|$, $V_{SD} > V_{SG}-\|V_{TH}\|$, $\;I_D = \frac{1}{2}\mu_p C_{ox}\frac{W}{L}(V_{SG}-\|V_{TH}\|)^2$ |

## 4. Ideal Model에서 벗어나는 효과

### Subthreshold Current
cutoff에서도 source–drain 사이 leakage. 작은 MOSFET일수록 중요 → **modern digital circuit의 큰 문제**. 손계산에선 무시, simulation엔 반영.

### Channel-Length Modulation
saturation에서도 $V_{DS} \uparrow$ → 실제 channel length 감소 → $I_D$ 증가:
$$
I_D = \frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{TH})^2(1 + \lambda V_{DS})
$$
BJT의 **Early effect**와 유사. 필요 시 해석에 포함.

### Body Effect
body voltage가 source와 다르면 $V_{TH}$ 변화:
$$
V_{TH} = V_{TH0} + \gamma\left(\sqrt{2\phi_F + V_{SB}} - \sqrt{2\phi_F}\right)
$$
IC에서는 body가 여러 transistor와 공유 → source에 항상 못 묶음.
- NMOS body → 가장 낮은 전위 · PMOS body → 가장 높은 전위

### Temperature Effect
온도 ↑ → mobility 감소 등으로 $I_D$ 감소 경향.

## 5. Two-Track Approach
- **손계산**: 단순·직관적 model
- **simulation**: 복잡·정확한 SPICE model

---

# Part 2. Small-Signal Characteristics

## 6. 왜 Small-Signal Model인가

대신호 특성은 비선형. 특정 DC bias point 근처 작은 변화만 보면 **Taylor 1차항**으로 근사:
$$
I_D \approx I_{D0} + g_m\, v_{gs}
$$
DC 성분과 small-signal 성분을 분리하면 회로 해석이 훨씬 쉬워진다.

## 7. MOSFET as VCCS

saturation에서 MOSFET = $V_{GS}$로 $I_D$를 조절하는 voltage-controlled current source:
$$
i_d = g_m v_{gs} \;\Rightarrow\; v_{out} = -g_m v_{gs} R \;\Rightarrow\; A_v = -g_m R
$$

## 8. Transconductance $g_m$

$$
g_m = \frac{\partial I_D}{\partial V_{GS}}
= \mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{TH})
= \frac{2I_D}{V_{GS}-V_{TH}}
= \sqrt{2\mu_n C_{ox}\frac{W}{L}I_D}
$$

- $g_m \propto V_{OV}$ (선형)
- $g_m \propto \sqrt{I_D}$
- **bias current와 transistor size가 small-signal gain을 결정**

## 9. Small-Signal Circuit 작성 규칙

- DC voltage source → **AC ground**
- DC current source → **open**
- MOSFET → $g_m v_{gs}$ current source
- channel-length modulation 고려 시 drain–source 사이 $r_o$ 추가

## 10. $r_o$ (Channel-Length Modulation)

$$
r_o = \frac{\partial V_{DS}}{\partial I_D} \approx \frac{1}{\lambda I_D}
$$
$r_o$가 finite이면 amplifier gain이 $R_D$가 아니라 $R_D \parallel r_o$로 제한.

## 11. Body Effect와 $g_{mb}$

body voltage가 source와 다르면 $I_D$ 변화 → small-signal에서 추가 current source:
$$
i_{d,body} = g_{mb}\, v_{bs}, \qquad g_{mb} = \chi\, g_m, \quad \chi \approx 0.1\text{–}0.3
$$
손계산에선 자주 무시, IC simulation에선 중요 (특히 source follower에서 $g_m + g_{mb}$).

## 12. PMOS Small-Signal Model

NMOS와 **동일한 형태**. 전압 기준을 $V_{SG}$, $V_{SD}$로, controlled current 방향과 node polarity를 일관되게.
$$
g_m = \mu_p C_{ox}\frac{W}{L}(V_{SG}-\|V_{TH}\|) = \frac{2I_D}{V_{SG}-\|V_{TH}\|}
$$

---

## 핵심 정리

- NMOS/PMOS의 cutoff/triode/saturation 조건과 $I_D$ 식, pinch-off($V_{DS}=V_{OV}$).
- Nonideal: subthreshold leakage, channel-length modulation($\lambda$), body effect($V_{SB}\to V_{TH}$), 온도.
- Small-signal: $i_d = g_m v_{gs}$, $g_m = 2I_D/V_{OV} = \sqrt{2\mu_n C_{ox}(W/L)I_D}$.
- $r_o \approx 1/(\lambda I_D)$ → gain을 $R_D \parallel r_o$로 제한.
- Body effect → $g_{mb}v_{bs}$, $g_{mb} = \chi g_m$.

## 복습 질문

- NMOS/PMOS의 세 영역 조건과 $I_D$ 식을 쓸 수 있나? pinch-off에서 triode·saturation 식이 연속인가?
- $g_m$의 세 가지 표현과, 각각을 언제 쓰는가?
- $\lambda$(channel-length modulation)와 $r_o$의 관계, small-signal gain에 주는 영향은?
- body effect가 $V_{TH}$를 바꾸는 식과, source follower에서 $g_{mb}$가 중요한 이유는?
{% endraw %}

---

이전: [01. Introduction](01-introduction-ii.md) · 다음: [03. Common-Source 증폭기](03-common-source-amplifier-cs.md)
