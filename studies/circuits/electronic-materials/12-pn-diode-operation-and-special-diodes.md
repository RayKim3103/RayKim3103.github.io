---
layout: page
title: "12. PN 다이오드와 특수 다이오드"
permalink: /studies/circuits/electronic-materials/12-pn-diode-operation-and-special-diodes/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electronic_Materials/lecture_notes) · 교재: Kasap Ch.6
- 이전: [11. 확산·흡수·접촉·PN접합](11-diffusion-optical-absorption-contacts-pn-junction.md) · 다음: [13. MOS 커패시터와 MOSFET](13-mos-capacitor-and-mosfet.md)

{% raw %}
## 개요

```text
fabrication: oxidation → litho → diffusion → metallization
forward bias: barrier·W ↓ → 지수적 전류 / reverse bias: barrier·W ↑ → -I_0
special: Zener, varicap, PIN, LED, tunnel diode (NDR)
```

---

## 1. PN Junction Diode Fabrication

1. damage-free 단결정 Si wafer
2. **thermal oxidation** → SiO₂ diffusion barrier
3. 1차 lithography → oxide에 diffusion window
4. **phosphorus diffusion** → 표면에 n⁺–p junction
5. **sputtering Al metallization** → 외부 연결
6. 2차 lithography → junction 외부 불필요 metal 제거

## 2. Ideal Diode Operation

### 평형 ($V_A = 0$)
$V_{bi}$와 depletion region 존재, 그러나 $E_F$ 같음 → **net current 0**, diffusion = drift 균형.

### Forward bias ($V_A = V_f > 0$)
$$
\text{barrier energy} = e(V_{bi} - V_f)
$$
- depletion width ↓
- majority carrier가 junction 넘어 상대 영역으로 **주입** → 반대쪽 quasi-neutral region에서 minority carrier가 되어 확산하며 recombination으로 감소

### Reverse bias ($V_A = V_r < 0$)
$$
\text{barrier energy} = e(V_{bi} + |V_r|)
$$
- depletion width ↑
- majority injection 억제
- depletion region이 minority carrier를 쓸어가는 **sink**

## 3. Diode Equation

$$
I = I_0\left[\exp\!\left(\frac{V_A}{V_{ref}}\right) - 1\right]
$$
- $I_0$ = reverse saturation current
- $V_{ref}$ = thermal voltage(또는 ideality factor 포함 $nV_T$)
- forward: 지수적 증가; reverse: $\to -I_0$

이상적 Shockley: $\;I_0 = qA\left(\dfrac{D_p p_{n0}}{L_p} + \dfrac{D_n n_{p0}}{L_n}\right)$, $\;L = \sqrt{D\tau}$ (diffusion length).

## 4. Junction Voltage Drop / Current Density

- 인가 전압 $V_A$는 대부분 depletion region에 걸린다고 근사 (quasi-neutral region은 중성이라 전위 변화 작음)
- forward bias: junction barrier = $V_{bi} - V_A$
- forward-biased diode 내부 전류 = electron current + hole current. 위치에 따라 비율이 달라도 **total current density는 일정** (연속).
- depletion을 지난 minority carrier는 quasi-neutral region에서 확산하며 recombination.

### Carrier concentration under bias
- **Forward**: barrier ↓ → p쪽 hole·n쪽 electron이 반대쪽으로 대량 주입 → 접합 근처 minority carrier 농도 큼, 깊이 들어갈수록 recombination으로 감소.
- **Reverse**: depletion이 minority carrier를 빠르게 쓸어 접합 근처 농도 낮음. reverse current는 주로 **thermal generation** minority carrier 수집으로 결정.

## 5. 특수 다이오드

| Diode | 이용 물리 | 대표 응용 |
|---|---|---|
| **Zener** | reverse breakdown에서 거의 일정 전압 | regulator, ESD protection |
| **Varicap (varactor)** | reverse bias에 따른 junction capacitance 변화 | RF tuning, VCO |
| **PIN** | intrinsic layer의 저항·capacitance 차이 | RF switch, photodiode |
| **LED** | e–h recombination light emission | display, lamp |
| **Tunnel diode** | tunneling + negative differential resistance | 고속 스위칭, 고주파 |

### Zener diode
heavily doped PN junction → reverse bias에서 breakdown voltage $V_{BR}$ 도달 시 전류 흐름. 일정 reverse voltage 유지 → voltage regulator. 높은 field 견딤 → ESD 보호. (낮은 $V_{BR}$은 Zener 터널링, 높은 $V_{BR}$은 avalanche.)

### Varicap diode
$$
V_R \uparrow \;\Rightarrow\; W_d \uparrow \;\Rightarrow\; C_d = \frac{\varepsilon_s A}{W_d} \downarrow
$$
N⁻ layer를 capacitance control layer로 넣어 전압–capacitance 곡선 설계. $C_j(V_R) = C_{j0}/(1 + V_R/V_{bi})^m$.

### PIN diode
p–**intrinsic**–n. intrinsic layer가 reverse bias에서 넓은 depletion/absorption 영역 제공. forward: 낮은 RF resistance / reverse: 높은 resistance + 낮은 capacitance. photodiode에서는 intrinsic layer가 photo-absorption layer.

### LED
forward bias → e·h가 접합에 주입되어 recombination → recombination energy가 photon으로:
$$
\text{photon energy} \approx E_g, \qquad \lambda \downarrow \Leftrightarrow E_g \uparrow
$$
- brightness ∝ current
- InGaN → 청색/녹색, AlGaAs → 적색
- **direct band gap** 재료 필요 (Si는 부적합)

### Tunnel diode
p·n 모두 **degenerate** doping → depletion region 매우 얇음 → tunneling 확률 큼. threshold voltage 낮고 응답 빠름.

**Negative Differential Resistance (NDR)**:
1. 작은 forward bias: n-region CB 전자 상태와 p-region VB 빈 상태가 에너지상 잘 맞음 → direct tunneling current ↑
2. 특정 bias에서 overlap 최대 → **peak current $I_P$**
3. bias ↑ → overlap 감소 → tunneling current ↓ → **전압 ↑ 인데 전류 ↓ (NDR)**, valley current $I_V$
4. 더 큰 forward bias → 일반 PN diode injection current 지배 → 전류 다시 ↑

## 6. PN diode vs Schottky diode

- **p⁺–n diode**: 작은 forward bias에서 depletion region recombination, 큰 forward bias에서 p⁺→n hole injection이 주 전류
- **MS Schottky diode**: semiconductor → metal majority carrier **thermionic emission**이 주 전류
- Schottky는 minority carrier storage 작음 → switching 빠름 (reverse recovery 없음)

---

## 복습 질문

- PN diode fabrication의 주요 공정 순서는?
- forward / reverse bias에서 barrier height와 depletion width는 어떻게 변하나?
- diode 내부에서 total current density가 일정해야 하는 이유는?
- Zener / varicap / PIN / LED / tunnel diode 각각의 이용 물리와 응용은?
- tunnel diode I–V 곡선의 NDR 구간을 band overlap으로 설명할 수 있나? Schottky diode가 빠른 이유는?
{% endraw %}

---

이전: [11. 확산·흡수·접촉·PN접합](11-diffusion-optical-absorption-contacts-pn-junction.md) · 다음: [13. MOS 커패시터와 MOSFET](13-mos-capacitor-and-mosfet.md)
