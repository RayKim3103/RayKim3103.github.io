---
layout: page
title: "16. LTspice 튜토리얼"
permalink: /studies/circuits/electric-circuits-2/16-ltspice-tutorial/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · `22 LTSpice Tutorial` 보강

{% raw %}
## 개요

LTspice 설치 → NCSU 45 nm CMOS model 설정 → schematic → `.op` / `.dc` / `.step` / `.ac` / `.tran` → CS amplifier homework로 $V_{TH}$, $g_m$, $r_o$, gain, 3-dB bandwidth를 simulation으로 측정.

---

## 1. LTspice

Analog Devices의 **무료** circuit simulator. schematic 기반, 다양한 SPICE analysis.

## 2. 주요 단축키

| 기능 | 키 | | 기능 | 키 |
|---|---|---|---|---|
| Configure analysis | A | | Component | P |
| Run/Pause | Alt+R | | Wire | W |
| Stop | Alt+S | | Ground | G |
| Zoom to fit | Space | | Voltage source | V |
| Resistor | R | | Capacitor | C |
| Inductor | L | | Net name | N |
| SPICE directive | . | | | |

## 3. 단위 표기

| 표기 | 값 | | 표기 | 값 |
|---|---:|---|---|---:|
| k | 1e3 | | m | 1e-3 |
| **MEG** | 1e6 | | u | 1e-6 |
| G | 1e9 | | n | 1e-9 |
| | | | p | 1e-12 |
| | | | f | 1e-15 |

> **주의**: LTspice에서 `M`은 mega가 아니라 **milli**로 해석 → mega는 반드시 `MEG`.

## 4. NCSU 45 nm CMOS Model

1. `models_nom` 폴더 준비
2. LTspice setting의 user libraries directory에 추가
3. schematic에 `.inc` directive로 model file 포함
4. MOSFET instance model name → `NMOS_VTL`, `PMOS_VTL` 등
5. length / width 지정

**Inverter 예**: `nmos4` + `pmos4` + VDD source + input source + ground + IN/OUT net label. **PMOS body → VDD**, **NMOS body → ground**. (PMOS symbol의 drain/source 표시가 기대와 다를 수 있으니 terminal naming 주의.)

## 5. Analysis 종류

| directive | 목적 | 특징 |
|---|---|---|
| **`.op`** | DC operating point | node voltage, device current, operating region 확인. C = open, L = short |
| **`.dc`** | DC sweep | source 값 변화 → V/I curve. inverter VTC, $I_d$ vs $V_{gs}$, $V_{TH}$ 추정 |
| **`.step`** | parametric | `.step param width_nmos 0.5u 1u 0.1u`; component 값에 `{width_nmos}`. width 변화별 gain 비교, tuning |
| **`.ac`** | frequency response | Bode magnitude/phase, DC gain, 3-dB bandwidth. **time-domain 아님**, 입력 AC amplitude 보통 1 |
| **`.tran`** | time-domain | sine/pulse/PWL 입력 응답, eye diagram. x-axis range 조정 |

## 6. CS Amplifier Homework

조건: $V_{DD} = 1.2\,\text{V}$, $V_{SS} = 0$, $R_D = 1.4\,\text{k}\Omega$, $M_1$ length 180 nm, width 4.5 µm, $C_L = 100\,\text{fF}$, $V_{in}$ DC offset 0.6 V, amplitude 0.05 V, frequency 300 MHz.

할 일:
1. `Id-Vgs` curve로 $V_{TH}$ 결정
2. DC sweep에서 `gm` plot (`D(Id(M1))` 같은 derivative expression)
3. $V_{GS} = 0.6\,\text{V}$에서 `ro` 결정 (`Id-Vds` curve 기울기 역수)
4. transient + AC analysis 실행
5. DC gain과 3-dB bandwidth 측정

---

## 핵심 정리

- LTspice = 무료 SPICE simulator; `M` = milli이므로 mega는 `MEG`.
- NCSU 45 nm model은 `.inc`로 포함, MOSFET model name·W/L 지정, body 연결 확인.
- `.op`(동작점) / `.dc`(sweep, VTC) / `.step`(parametric) / `.ac`(Bode, amplitude 1) / `.tran`(time-domain, eye).
- `gm` = `D(Id(M1))`, `ro` = $I_d$–$V_{ds}$ 기울기의 역수.

## 복습 질문

- `.op` / `.dc` / `.ac` / `.tran` / `.step`의 용도를 각각 한 줄로?
- LTspice에서 1 MΩ을 어떻게 써야 하나? (`M`의 함정)
- AC analysis에서 입력 amplitude를 1로 두면 왜 편한가?
- simulation으로 $g_m$과 $r_o$를 어떻게 뽑나?
{% endraw %}

---

이전: [15. 설계 프로젝트 — TIA/CTLE](15-design-project-tia-ctle.md)
