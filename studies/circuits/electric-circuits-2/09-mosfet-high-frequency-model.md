---
layout: page
title: "09. MOSFET 고주파 모델 (Capacitance · fT)"
permalink: /studies/circuits/electric-circuits-2/09-mosfet-high-frequency-model/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `11 MOSFET High-Frequency Model` 보강

{% raw %}
## 개요

MOSFET은 gate oxide·junction 때문에 여러 capacitance를 가진다. 고주파에서 $C_{gs}$, $C_{gd}$, $C_{db}$, $C_{sb}$가 pole/zero를 만들고 amplifier bandwidth를 제한. 트랜지스터 자체 속도는 **unit-gain frequency $f_T$**로 비교.

---

## 1. MOSFET Capacitance

| capacitance | 위치 |
|---|---|
| $C_{gs}$ | gate–source |
| $C_{gd}$ | gate–drain (**Miller** 때문에 amplifier BW에 큰 영향) |
| $C_{db}$ | drain–body (junction) |
| $C_{sb}$ | source–body (junction) |

PMOS도 동일한 방식.

## 2. 왜 단순화하는가

실제 SPICE model은 매우 복잡. 손계산에선 **frequency response를 결정하는 dominant capacitor**를 파악하는 것이 목표:
```text
MOS small-signal model → essential capacitances 추가 → pole/zero 근사 → bandwidth 추정
```

## 3. Capacitance 근사 (saturation)

$$
C_{gd} \approx W L_{ov} C_{ox} \quad(\text{overlap only})
$$
$$
C_{gs} \approx W L_{ov} C_{ox} + \frac{2}{3} W L C_{ox} \quad(\text{overlap + channel})
$$
보통 $C_{gs} > C_{gd}$.

## 4. Unit-Gain Frequency $f_T$

common-source **short-circuit current gain**의 크기가 1이 되는 주파수:
$$
|I_{out}/I_{in}| = 1 \;\; \text{at}\;\; f = f_T
$$
$$
\omega_T = \frac{g_m}{C_{gs} + C_{gd}}, \qquad f_T = \frac{g_m}{2\pi(C_{gs} + C_{gd})}
$$

의미: transistor speed 비교 지표, 가능한 최대 동작 주파수의 감각.

**$f_T$를 키우려면**: $g_m$ ↑, $C_{gs}$·$C_{gd}$ ↓. 단 $W$를 키우면 $g_m$과 capacitance가 함께 커져 단순하지 않다 (대략 $f_T \propto \mu V_{OV}/L^2$ → **짧은 채널·큰 overdrive**가 유리).

## 5. 예시

조건: $L = 0.25\,\mu\text{m}$, $W = 10\,\mu\text{m}$, $V_{DS} = 2\,\text{V}$, $V_{GS} = 1.5\,\text{V}$
→ 추정 $f_T \approx 24\,\text{GHz}$ (simulation/모델 값과 유사 범위).

---

## 핵심 정리

- 고주파 MOS model = small-signal model + $C_{gs}$, $C_{gd}$, $C_{db}$, $C_{sb}$.
- $C_{gs} \approx W L_{ov}C_{ox} + \frac23 WLC_{ox}$, $C_{gd} \approx WL_{ov}C_{ox}$; 보통 $C_{gs} > C_{gd}$.
- $f_T = g_m / [2\pi(C_{gs}+C_{gd})]$ — 트랜지스터 속도 지표.
- $C_{gd}$는 Miller effect로 amplifier bandwidth를 크게 좌우.

## 복습 질문

- 네 capacitor의 위치를 소신호 모델 위에 그릴 수 있나?
- $f_T$의 정의(short-circuit current gain = 1)와 식, $f_T$를 키우는 방법은?
- 왜 $C_{gd}$가 유독 amplifier bandwidth에 중요한가? (→ Miller)
{% endraw %}

---

이전: [08. Pole·Zero & Bode Plot](08-pole-zero-bode-plot.md) · 다음: [10. 증폭기 주파수 응답](10-amplifier-frequency-response.md)
