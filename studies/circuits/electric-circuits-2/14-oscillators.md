---
layout: page
title: "14. 발진기 — Ring · LC"
permalink: /studies/circuits/electric-circuits-2/14-oscillators/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `26 Ring Oscillators` + `27 LC Oscillators` **통합**

{% raw %}
## 개요

발진기 = 입력 없이 주기적 출력을 만드는 회로. feedback loop가 특정 주파수에서 한 바퀴 돌아온 신호의 **magnitude ≥ 1, phase = 0°(또는 360°)** 이면 발진 (**Barkhausen**). **Ring**은 delay·phase shift로, **LC**는 tank의 energy exchange로 발진.

---

## 1. Barkhausen 조건

loop transfer $$H(j\omega)$$:
$$
|H(j\omega_{osc})| = 1, \qquad \angle H(j\omega_{osc}) = 0° \text{ 또는 } 360°
$$
한 바퀴 돌아온 신호가 **in-phase**이고 **같은 magnitude**. 실제로는:
- **startup**을 위해 처음엔 loop gain이 1보다 약간 커야
- **large-signal nonlinearity**가 amplitude를 제한

---

# Part 1. Ring Oscillator

## 2. 단일 / 2단 CS

- 1단 CS: $$H(s) = -g_m(R_D \parallel 1/sC_D)$$ — 하나로는 phase/magnitude 조건 동시 만족 어려움
- 2단 CS: DC에서 phase ≈ 360°지만 pole phase shift·magnitude 조건 때문에 안정적 발진 어려움

## 3. 3단 CS Ring

각 stage가 ~60° 추가 phase shift 제공 → 전체 조건 만족.
$$
H_{stage}(j\omega) = \frac{-g_m R_D}{1 + j\omega R_D C_D}
$$
$$
3\tan^{-1}(\omega R_D C_D) = \pi \;\Rightarrow\; \omega_{osc} \approx \frac{\sqrt{3}}{R_D C_D}
$$
이 주파수에서 loop gain magnitude ≥ 1 필요.

## 4. CMOS Inverter Ring Oscillator

출력이 rail-to-rail swing → small-signal linear analysis 부정확.
- 발진은 **noise**가 시작
- MOSFET이 항상 saturation은 아님 → large-signal simulation 필요
- clock 생성에 충분한 non-sinusoidal waveform

**N-stage (odd) ring**:
$$
f_{osc} = \frac{1}{2 N T_D} \qquad (T_D = \text{stage propagation delay})
$$
예: 3-stage → $$f_{osc} = 1/(6T_D)$$.

## 5. Differential Ring Oscillator

differential amplifier stage → **even-number stage도 가능**.
- CMOS inverter chain보다 빠를 수 있음
- 단, static current로 **power consumption 증가**

## 6. VCO (Voltage-Controlled Oscillator)

control voltage로 발진 주파수 조절. ring에서 frequency control:
- current control
- load capacitance control
- delay cell bias control

---

# Part 2. LC Oscillator

## 7. Ring의 한계

CMOS 구현 쉽고 tuning range 넓지만 **주파수가 sharp하지 않고 phase noise가 큼** → 깨끗한 발진이 필요하면 LC.

## 8. LC Tank 물리

$$
E_C = \frac12 C V^2, \qquad E_L = \frac12 L I^2
$$
capacitor 전압 → inductor current → 반대 방향 충전 → 반복.
$$
\omega_0 = \frac{1}{\sqrt{LC}}
$$

## 9. Ideal vs Real Tank

| | Ideal | Real |
|---|---|---|
| parasitic $$R$$ | 없음 | 존재 |
| $$Q$$ | ∞ (resonance 무한 sharp) | $$Q = \omega_0 R C$$ (유한) |
| pole | 허수축 $$s = \pm j/\sqrt{LC}$$ | left-half plane으로 이동 → self-sustained 아님 |

## 10. 손실 보상

active circuit이 tank loss를 보상 (**negative resistance**):
$$
g_m R \ge 1
$$
- $$g_m R = 1$$: loss 정확히 보상
- startup: 실제로는 $$g_m R > 1$$ 필요
- amplitude는 nonlinear effect로 제한

## 11. Cross-Coupled LC Oscillator

cross-coupled pair가 LC tank에 negative resistance 제공.
```text
active pair → R에서 잃은 에너지 보충
LC tank → 발진 주파수 결정
```
noise가 작은 differential perturbation 생성 → $$g_m > 1/R$$ 이면 $$V_X$$, $$V_Y$$ 차이가 성장 → tail current $$I_{SS}$$와 transistor nonlinearity가 amplitude 제한.

## 12. LC vs Ring

| 항목 | LC | Ring |
|---|---|---|
| phase noise | 작음 | 큼 |
| 주파수 sharpness | 좋음 | 낮음 |
| CMOS 구현 | inductor 때문에 어려움 | 쉬움 |
| 최대 주파수 | 높게 가능 | delay 제한 |
| tuning range | 좁음 | 넓음 |
| 면적 | 큼 | 작음 |

---

## 핵심 정리

- Barkhausen: $$|H| = 1$$, $$\angle H = 0°/360°$$; startup은 loop gain > 1, amplitude는 nonlinearity가 제한.
- Ring: odd-number inversion + delay. 3단 CS → $$\omega_{osc} \approx \sqrt3/(R_D C_D)$$; CMOS inverter ring $$f_{osc} = 1/(2NT_D)$$. differential ring → even stage 가능, faster but more power.
- LC: $$\omega_0 = 1/\sqrt{LC}$$, real tank $$Q = \omega_0 RC$$. cross-coupled pair가 negative resistance로 loss 보상, startup 조건 $$g_m R > 1$$.
- LC = clean(낮은 phase noise), Ring = 구현 용이·넓은 tuning.

## 복습 질문

- Barkhausen 조건을 magnitude·phase로 쓰고, startup에서 loop gain이 왜 1보다 커야 하나?
- 3단 CS ring의 $$\omega_{osc} \approx \sqrt3/(R_D C_D)$$ 유도, CMOS inverter ring의 $$f_{osc} = 1/(2NT_D)$$는?
- 실제 LC tank가 self-sustained oscillation을 못 하는 이유와, cross-coupled pair의 역할($$g_m R > 1$$)은?
- LC와 ring oscillator를 phase noise·tuning range·면적으로 비교하면?
{% endraw %}

---

이전: [13. 음귀환](13-feedback.md) · 다음: [15. 설계 프로젝트 — TIA/CTLE](15-design-project-tia-ctle.md)
