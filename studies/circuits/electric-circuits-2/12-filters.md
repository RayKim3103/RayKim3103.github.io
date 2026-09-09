---
layout: page
title: "12. 필터 — 1차 · 2차 수동 · 인덕터 시뮬레이터 · Biquad · 고차 Butterworth"
permalink: /studies/circuits/electric-circuits-2/12-filters/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `16 First-Order` + `17 Passive Second-Order` + `18 Inductor Simulator` + `19 Integrator Biquad (KHN, Tow-Thomas)` + `20 Higher-Order Butterworth` **통합**

{% raw %}
## 개요

필터는 입력 신호의 frequency spectrum을 바꾸는 회로 ($V_o(s) = H(s)V_i(s)$). filter order = denominator 차수.

```text
1차 필터 (1 real pole) → 2차 필터 (complex pole, w0·Q) → LP/HP/BP/AP
→ IC용: inductor simulator, integrator biquad (KHN, Tow-Thomas)
→ 고차: Butterworth pole placement, 1차·2차 section cascade
```

---

# Part 1. First-Order Filters

## 1. 일반형

$$
H(s) = \frac{a_1 s + a_0}{s + \omega_0}
$$
pole/zero 위치가 magnitude·phase 변화를 결정.

## 2. Low-Pass / High-Pass / All-Pass

| 필터 | $H(s)$ | zero | pole | 특징 |
|---|---|---|---|---|
| **LP** | $\dfrac{1}{1 + sRC}$ | — | $1/RC$ | 고주파 $-20\,\text{dB/dec}$ |
| **HP** | $\dfrac{sRC}{1 + sRC}$ | $s = 0$ | $1/RC$ | DC 차단, 저주파 $+20\,\text{dB/dec}$ |
| **AP** | $\dfrac{s - \omega_0}{s + \omega_0}$ | $+\omega_0$ | $-\omega_0$ | \|H\| = 1, phase만 변화 (delay line) |

## 3. Passive RC의 한계 vs Active Filter

| Passive RC | Active (op-amp) |
|---|---|
| fixed gain | gain control 가능 |
| loading effect | loading effect 감소 |
| source/load 저항이 pole을 이동 | buffering, LP/HP/AP 구현 쉬움 |

예 (inverting active LP): $\;H(s) = -\dfrac{R_f}{R_{in}}\cdot\dfrac{1}{1 + sRC}$

---

# Part 2. Second-Order Passive Filters

## 4. 표준형

$$
D(s) = s^2 + \frac{\omega_0}{Q}s + \omega_0^2
$$
$$
p = -\frac{\omega_0}{2Q} \pm j\,\omega_0\sqrt{1 - \frac{1}{4Q^2}}
$$
$Q > 1/2$ 이면 **complex conjugate pole** → resonance 가능.

### Damping factor
$$
\zeta = \frac{1}{2Q}: \quad \zeta<1\ \text{under-damped},\;\; \zeta=1\ \text{critically},\;\; \zeta>1\ \text{over-damped}
$$

## 5. LP / HP / BP / AP (같은 분모, 분자만 다름)

| 필터 | numerator | 특징 |
|---|---|---|
| **LP** | $\omega_0^2$ | $w_0 = 1/\sqrt{LC}$, $Q = \omega_0 RC$ (RLC) |
| **HP** | $s^2$ | 저주파 $+40\,\text{dB/dec}$ |
| **BP** | $(\omega_0/Q)s$ | peak at $\omega_0$, $\text{BW} = \omega_0/Q$ |
| **AP** | $s^2 - (\omega_0/Q)s + \omega_0^2$ | \|H\| 일정, phase만 변화 |

- $\omega_0$: L↔C energy transfer frequency
- $Q$: damping이 얼마나 작은지 / resonance가 얼마나 sharp한지. Q 클수록 peaking·passband sharpness ↑.

## 6. 왜 고차 필터인가

- cutoff가 더 sharp
- 다양한 magnitude/phase 응답
- Butterworth, Chebyshev, elliptic 같은 표준 응답 구현

---

# Part 3. Filters with Inductor Simulator

## 7. IC에서 Inductor가 어려운 이유

$$
v = L\frac{di}{dt}, \qquad V(s) = sL\,I(s)
$$
저주파용 큰 inductance → on-chip 면적 과대. → op-amp + R + C로 **synthetic inductance**.

## 8. Inductance-Simulation Circuit

$$
L_{eq} = \frac{R_1 R_3 C_4 R_5}{R_2}, \qquad Z_{in} \approx s L_{eq}
$$
입력에서 본 impedance가 inductor처럼 보이도록 op-amp 네트워크 구성.

## 9. Inductor를 대체한 2차 필터

수동 RLC 필터의 $L$을 $L_{eq}$로 대체:
$$
H_{HP}(s) = \frac{s^2}{s^2 + (1/RC)s + 1/(L_{eq}C)}, \qquad
H_{BP}(s) = \frac{s/RC}{s^2 + (1/RC)s + 1/(L_{eq}C)}
$$
$$
\omega_0 = \frac{1}{\sqrt{L_{eq}C}}
$$
**All-pass**: band-pass 출력 $T(s)$의 linear combination, 예: $V_{out} = 2V_i T(s) - V_i$.

## 10. Grounded vs Floating Inductor

기본 inductor simulator는 **grounded inductor**. 일부 필터는 floating inductor 필요 → **Generalized Impedance Converter (GIC)**:
$$
Z_{in} = \text{(여러 } Z \text{ 요소의 곱/비)}
$$
resistor·capacitor 선택으로 floating/grounded inductance-like impedance를 얻는다.

---

# Part 4. Filters with Integrators (KHN, Tow-Thomas Biquad)

## 11. Integrator

$$
\frac{V_o}{V_i} = -\frac{1}{sRC}, \qquad \omega_0 = \frac{1}{RC}
$$
biquad filter의 핵심 building block.

## 12. Biquadratic Filter

2차 표준 분모 $D(s) = s^2 + (\omega_0/Q)s + \omega_0^2$. 하나의 회로에서 numerator를 다르게 취하면 LP/HP/BP를 모두 얻는다.

## 13. KHN (Kerwin–Huelsman–Newcomb) Biquad

세 출력을 동시에 제공:
$$
V_{bp} = -\frac{\omega_0}{s}V_{hp}, \qquad V_{lp} = -\frac{\omega_0}{s}V_{bp}
$$
→ **HP를 한 번 적분 = BP, 두 번 적분 = LP.**

$V_{hp}$는 입력과 feedback된 $V_{bp}$, $V_{lp}$의 weighted sum:
$$
V_{hp} = K V_i - \frac{1}{Q}V_{bp} - V_{lp}
$$
resistor ratio로 $K$, $Q$, $\omega_0$ 설정.

**장점**: 한 회로에서 LP/HP/BP 동시, $Q$·$\omega_0$·gain을 R/C ratio로 조절, active라 loading 작음.

**All-pass**: HP/BP/LP의 linear combination, $\;V_{ap} = V_{hp} - \frac{1}{Q}V_{bp} + V_{lp}$.

## 14. Tow-Thomas Biquad

integrator + summing amplifier 기반 2차 active filter. component 선택으로 LP/HP/BP/AP 구현. ($R$, $C$, $R_1$, $R_2$, $R_3$, $r$ 등으로 numerator·denominator 설정.) KHN처럼 integrator 기반이지만 damping 방식이 다름(저항 피드백 damping).

---

# Part 5. Higher-Order Filters (Butterworth)

## 15. 필터 응답 비교

| 필터 | passband ripple | stopband ripple | cutoff |
|---|---|---|---|
| **Butterworth** | 없음 (maximally flat) | 없음 | 느림 |
| Chebyshev | 있거나 없음 | 유형에 따라 | 중간/빠름 |
| Elliptic | 있음 | 있음 | 빠름 |

## 16. Butterworth Magnitude

$$
|H(j\omega)|^2 = \frac{1}{1 + (\omega/\omega_p)^{2N}}, \qquad |H(j\omega_p)| = \frac{1}{\sqrt{2}}\;(-3\,\text{dB})
$$

## 17. Pole 위치

반지름 $\omega_p$인 원 위에 균일 배치, **안정성을 위해 left-half plane pole만** 선택:
$$
s_k = \omega_p\, e^{j\theta_k}
$$

| $N$ | 구현 |
|---|---|
| 1 | $H(s) = \omega_p/(s + \omega_p)$ (일반 1차 LP) |
| 2 | $H(s) = \omega_0^2/[s^2 + (\omega_0/Q)s + \omega_0^2]$, $\;\omega_0 = \omega_p$, $\;Q = 1/\sqrt{2} \approx 0.707$ |
| 3 | 1차 Butterworth + 2차 ($Q = 1$) cascade |

## 18. N차 구현 전략

- $N$ 홀수: 1차 section 1개 + 2차 sections
- $N$ 짝수: 2차 sections만
- 각 2차 section은 서로 다른 $Q$, 같은 cutoff scale 공유

**설계 절차**: spec → order $N$ → cutoff $\omega_p$ → pole 위치 → 1차/2차 factorization → op-amp active filter 또는 passive로 각 section 구현.

---

## 핵심 정리

- 1차: LP/HP/AP, pole $1/RC$. active filter로 gain·impedance 제어.
- 2차 표준 분모 $s^2 + (\omega_0/Q)s + \omega_0^2$; $Q > 1/2$ → complex pole. LP/HP/BP/AP는 numerator로 구분. BP $\text{BW} = \omega_0/Q$.
- IC용 inductor: $L_{eq} = R_1 R_3 C_4 R_5/R_2$ (grounded); floating은 GIC.
- Integrator $-1/(sRC)$; KHN biquad: HP→적분→BP→적분→LP; AP는 세 출력의 가중합. Tow-Thomas도 integrator 기반.
- Butterworth: maximally flat, $|H|^2 = 1/[1+(\omega/\omega_p)^{2N}]$; 2차 $Q = 1/\sqrt2$; 고차는 1차/2차 section cascade.

## 복습 질문

- 1차 LP/HP/AP의 transfer function과, passive RC의 loading effect·active filter의 이점은?
- 2차 분모에서 $Q$의 물리적 의미, $Q > 1/2$이면 왜 complex pole인가? BP bandwidth는?
- IC에서 physical inductor가 어려운 이유와 $L_{eq}$ 개념, grounded vs floating(GIC)?
- KHN biquad에서 HP→BP→LP가 적분으로 이어지는 관계식과, AP를 만드는 가중합은?
- Butterworth 2차의 $Q = 1/\sqrt2$는 어디서 나오나? N차를 1차/2차 section으로 나누는 규칙은?
{% endraw %}

---

이전: [11. OTA & Op-Amp](11-ota-and-op-amp.md) · 다음: [13. 음귀환 (Feedback)](13-feedback.md)
