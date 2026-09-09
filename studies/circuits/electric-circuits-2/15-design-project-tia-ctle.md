---
layout: page
title: "15. 설계 프로젝트 — Optical Receiver (PD · TIA · CTLE)"
permalink: /studies/circuits/electric-circuits-2/15-design-project-tia-ctle/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `23 Project Design Guide` (스펙·방법) + `00 Design Project Summary` (설계 결과) **통합**

{% raw %}
## 개요

**광수신기** `Photodiode(PD) → Transimpedance Amplifier(TIA) → Continuous-Time Linear Equalizer(CTLE)` 설계. PD가 광 신호를 전류로 바꾸고, TIA가 전류를 전압으로 변환·증폭하며, CTLE가 PD/TIA의 고주파 손실을 zero로 보상해 **10 Gbps급 eye opening**을 확보한다. 평가: frequency response · eye diagram · power · FoM.

```text
Optical signal → Photodiode → Current → TIA → Voltage → CTLE → Output
   PD: 광→전류        TIA: 전류→전압·증폭        CTLE: bandwidth 확장·ISI 감소
```

---

# Part 1. 설계 목표와 스펙

## 1. Target Specification

| 항목 | 목표 |
|---|---:|
| Data rate | 10 Gbps |
| OTA open-loop gain | > 15 dB |
| OTA open-loop bandwidth | > 6.5 GHz |
| Transimpedance gain | > 60 dBΩ |
| HPF cut-off | < 1 MHz |
| Overall bandwidth | > 4.5 GHz |
| Overall peaking | < 1.5 dB |
| Power consumption | < 5 mW |

$$
\text{FoM} = \frac{\text{eye height} \times \text{eye width}}{\text{power consumption}}
$$

## 2. Eye Diagram

unit interval(UI) 단위로 time waveform을 겹쳐 표시.
- **eye height**: vertical noise margin (voltage gain과 관련)
- **eye width**: timing margin (bandwidth·ISI에 민감)
- 자료 기준: eye height ≈ 0.7 $V_o$, cursor는 0.15$V_o$ / 0.85$V_o$
- 10 Gbps → $\text{UI} = 1/10\,\text{Gbps} = 100\,\text{ps}$

## 3. TIA 전달함수

open-loop gain $A_0$, PD capacitance $C_{PD}$, feedback resistor $R_F$:
$$
\frac{V_{out}}{I_{in}} = \frac{-A_0 R_F}{s C_{PD} R_F + 1 + A_0}
$$
open-loop bandwidth가 finite ($A(s) = A_0/(1 + s/\omega_p)$)이면 → feedback으로 **bandwidth 증가, gain 감소** (gain–bandwidth trade-off 그대로).

## 4. CTLE Core

source degeneration capacitor를 이용한 고주파 boosting:
$$
\frac{V_{out}}{V_x} = \frac{-g_m R_D(1 + s R_S C_S)}{1 + g_m R_S + s R_S C_S}
$$
$$
\omega_z = \frac{1}{R_S C_S}, \qquad \omega_p = \frac{1 + g_m R_S}{R_S C_S}
$$
zero를 PD+TIA bandwidth roll-off 근처에 배치해 loss 보상.

**입력 High-Pass Filter** (bias 생성 + DC 차단):
$$
\frac{V_x}{V_{in}} = \frac{s(R_1 \parallel R_2)C_i}{1 + s(R_1 \parallel R_2)C_i}, \qquad
V_x(\text{DC}) = \frac{R_2}{R_1 + R_2}V_{DD}
$$
target: cut-off < 1 MHz.

## 5. Overall Peaking

system gain의 최대값과 저주파 gain의 차이. target < 1.5 dB.
peaking이 너무 크면 고주파 noise가 과증폭되고 eye가 왜곡.

---

# Part 2. 설계 결과 (시뮬레이션)

## 6. 최종 성능

| 항목 | 값 |
|---|---:|
| OTA gain @ 10 MHz | 22.99 dB |
| OTA 3-dB bandwidth | 6.99 GHz |
| High-pass filter cut-off | 788.6 kHz |
| TIA gain @ 10 MHz (CTLE load) | 62 dB |
| System 3-dB bandwidth | 4.501 GHz |
| Overall peaking | 0.381 dB |
| Power consumption | 1.285 mW |
| Eye height | 226.32 mV |
| Eye width | 88.54 ps |
| FoM | 1.56e-8 |

→ 모든 target spec 충족 (gain > 15 dB, BW > 6.5 GHz, $Z_T$ > 60 dBΩ, HPF < 1 MHz, overall BW > 4.5 GHz, peaking < 1.5 dB, power < 5 mW).

## 7. 회로 파라미터

| 파라미터 | 값 |
|---|---:|
| $R_F$ | 1.6 kΩ |
| $C_{IN}$ | 40 pF |
| $R_1$, $R_2$ | 10 kΩ |
| $R_S$ | 1.2 kΩ |
| $C_S$ | 0.08 pF |
| $R_D$ | 2.29 kΩ |
| $I_{REF}$ | 300 µA |
| $V_{REF}$ | 0.6 V |

MOS 길이는 45 nm / 180 nm를 섞어 사용, headroom과 bandwidth를 동시에 맞추려 width 조정.

## 8. OTA 검증

**포화 영역 확인** — 모든 MOSFET:
- NMOS: $V_{GS} - V_{TH} > 0$, $V_{DS} > V_{GS} - V_{TH}$
- PMOS: $V_{SG} - |V_{TH}| > 0$, $V_{SD} > V_{SG} - |V_{TH}|$

보고서 가정 $V_{TH} \approx 0.3\,\text{V}$; PMOS 실측 $\approx 0.23\text{–}0.25\,\text{V}$ → M1–M8 모두 saturation으로 판단.

**OTA AC**: DC gain 22.99 dB, 3-dB BW 6.99 GHz.

설계 관점:
- $g_m$ ↑ → gain·bandwidth 유리
- $r_o = 1/(\lambda I_D)$ → bias current ↑ → $r_o$ ↓ → gain 감소 가능
- → $I_{REF}$, MOS width, headroom의 **균형**이 관건 (시뮬레이션으로 gain > 15 dB & BW > 6.5 GHz 동시 만족)

## 9. TIA 설계

PD는 고주파에서 내부 capacitance로 전류 일부가 capacitor로 빠져 bandwidth 제한:
- PD 단독: $I_{OUT}/I_{IN}$ @ 10 MHz = 0 dB, @ 8 GHz = −3 dB

$R_F$가 transimpedance gain을 결정하지만 너무 크면 출력단 MOSFET이 triode로 밀려 gain 감소 → **출력단 포화 조건을 유지하면서 gain을 키우는 값**. 조정 핵심: $V_{REF}$, $R_F$.
- $R_F$ ↑ → gain ↑, 단 headroom 부족 → MOS triode 진입 가능
- $V_{REF}$ 부적절 → saturation 실패

## 10. CTLE 설계 (추정 pole/zero)

| 요소 | 위치 |
|---|---:|
| HPF zero | 0 Hz |
| HPF pole | 788 kHz |
| CTLE zero | ~1.23 GHz |
| CTLE pole 1 | ~3 GHz |
| CTLE pole 2 | ~18.7 GHz |

PD+TIA만의 3-dB bandwidth ≈ 2.28 GHz → CTLE zero를 이 근처에 배치해 고주파 손실 보상.
CTLE는 작은 capacitance와 GHz 대역 parasitic 때문에 **이론식 ↔ simulation 차이가 큼** → pole/zero 공식으로 방향을 잡고 parameter sweep으로 조정.

## 11. Eye Diagram 결과

`PD + TIA + CTLE`:
- eye height 226.32 mV, eye width 88.54 ps
- overall bandwidth 4.502 GHz, overall peaking ~0.4 dB

eye height ← voltage gain, eye width ← bandwidth·ISI.

---

## Part 3. LTspice 실무 (프로젝트)

- **Eye diagram transient**: stop time 300 ns, save 시작 150 ns, max timestep 1 ps. 0–150 ns는 HPF settling으로 버리고 150–300 ns를 eye에 사용.
- SPICE directive: `.option baudrate={1/100p}`
- **Photodiode**: PD symbol을 user library에. transient는 `PWL FILE`에 `project_input.txt`, AC는 current input AC amplitude = 1.
- **Power**: `.meas TRAN Power_consumption 1*AVG I(V1)` → SPICE log에서 확인.

---

## 핵심 정리

- 광수신기 = PD(광→전류) + TIA(전류→전압, shunt feedback) + CTLE(zero로 고주파 보상).
- TIA: $V_{out}/I_{in} = -A_0 R_F/(sC_{PD}R_F + 1 + A_0)$; feedback으로 BW↑ gain↓.
- CTLE core: $\omega_z = 1/(R_S C_S)$, $\omega_p = (1+g_m R_S)/(R_S C_S)$; zero를 PD+TIA roll-off(~2.28 GHz) 근처에.
- 결과: OTA 22.99 dB / 6.99 GHz, TIA 62 dB, system BW 4.501 GHz, peaking 0.381 dB, power 1.285 mW, eye 226.32 mV × 88.54 ps, FoM 1.56e-8 — 전 spec 충족.

## 복습 질문

- TIA 전달함수에서 $C_{PD}$와 $R_F$가 gain·bandwidth에 각각 어떤 영향을 주나?
- CTLE의 zero를 어디에 배치해야 하고, 그 근거(PD+TIA roll-off ~2.28 GHz)는?
- OTA에서 $I_{REF}$를 키우면 gain과 bandwidth가 각각 어떻게 되고, 왜 균형이 필요한가?
- eye diagram에서 0–150 ns를 버리는 이유는? FoM의 정의는?
{% endraw %}

---

이전: [14. 발진기](14-oscillators.md) · 다음: [16. LTspice 튜토리얼](16-ltspice-tutorial.md)
