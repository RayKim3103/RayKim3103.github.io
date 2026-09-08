---
layout: page
title: "02. MOS 소자 모델과 비이상성"
permalink: /studies/circuits/cmos-ic/02-mos-device-models-and-nonidealities/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `02 Devices — MOS 소자 모델과 비이상성` 보강

{% raw %}
## 개요

MOSFET을 **이상적 switch가 아니라** 유한 저항 + parasitic capacitance + leakage를 가진 물리 소자로 이해한다. I–V characteristic, capacitance, switch-level RC model, nonideal 효과(velocity saturation, channel length modulation, body effect), leakage 3종, process variation, noise margin.

---

## 1. MOS Capacitor와 Terminal

gate와 body는 oxide로 분리된 **capacitor**. source/drain은 diffusion terminal.
- nMOS: 관례적으로 **더 낮은 전압 쪽을 source**로 본다.
- 기본 분석: body와 source를 0 V에 둠.

---

## 2. nMOS 동작 영역

| 영역 | 조건 | 동작 |
|---|---|---|
| **Cutoff** | `Vgs < Vt` | channel 미형성. 이상적 `Id = 0`, 실제로는 **subthreshold leakage** |
| **Linear (Triode)** | `Vgs > Vt`, `Vds < Vgs − Vt` | channel이 source→drain 연결, **resistor처럼** 동작 |
| **Saturation** | `Vgs > Vt`, `Vds ≥ Vgs − Vt` | drain 쪽 channel **pinch-off**, `Id`가 `Vds`에 둔감 |

digital gate switching 중에는 transistor가 linear ↔ saturation을 오가므로 **평균 ON 저항 모델**을 쓴다.

### 이상적 I–V (long-channel square-law)

```text
Linear:     Id = β [ (Vgs − Vt) Vds − Vds²/2 ]
Saturation: Id = (β/2) (Vgs − Vt)²          , β = μ Cox (W/L)
```

deep-submicron device에서는 **velocity saturation** 때문에 saturation current가 `(Vgs − Vt)²` 보다 **`(Vgs − Vt)¹`에 가깝게** 완만해진다.

---

## 3. Capacitance

| Capacitance | 위치 | 영향 |
|---|---|---|
| **Gate capacitance** `Cg` | gate–oxide–channel | 입력 부하, switching power (`Cg = Cox·W·L`) |
| **Diffusion capacitance** `Cd` | source/drain–body junction | 출력 node 부하 → **delay** (self-loading) |
| **Overlap capacitance** | gate ↔ source/drain 겹침 | **Miller 효과**, coupling |

- Gate capacitance는 **W가 커지면 증가**. L이 길어져도 gate 면적이 커져 증가.
- VDD 자체는 `Cg` 값을 키우지 않지만, 충방전 에너지 `C·VDD²`를 크게 만든다.

---

## 4. Switch-Level RC Model

digital timing 추정용 근사: transistor를 **ON resistance + capacitance**로.

- **Unit nMOS**: 저항 `R`, gate capacitance `C`
- **Unit pMOS**: 같은 W에서 mobility 낮아 저항 `≈ 2R`
- pMOS width를 2배로 키워 **rise/fall delay를 맞춤** (P/N ratio)
- 더 넓은 transistor → 저항 ↓ 이지만 capacitance ↑

SPICE만큼 정확하지 않지만 **gate sizing / delay 직관**을 빠르게 준다 ([03](03-delay-models-and-logical-effort.md)).

---

## 5. Nonideal I–V Effects

### Velocity Saturation
강한 lateral field에서 carrier velocity가 선형 증가를 멈추고 **포화**(`v_sat`). short-channel device에서 saturation current가 square-law 예측보다 작아지고, `Vgs`에 더 선형적. → gate overdrive를 키워도 기대만큼 빨라지지 않음.

### Channel Length Modulation
saturation에서도 `Vds` 증가 시 pinch-off point가 source 쪽으로 이동 → **effective L 감소** → `Id` 약간 증가. analog 관점에서 **유한 output resistance** `ro`의 원인.

### Body Effect
source–body 전압 `Vsb`가 증가하면 **threshold voltage `Vt` 증가**:
```text
Vt = Vt0 + γ ( √(|−2φF + Vsb|) − √|2φF| )
```
nMOS body가 GND 고정인데 source가 올라가면 `Vt` ↑ → transistor가 약해짐 (예: series stack의 위쪽 transistor, pass transistor).

---

## 6. Leakage Sources

| Leakage | 원인 | 특징 |
|---|---|---|
| **Subthreshold** | `Vgs < Vt`에서 weak inversion current | `Vgs`, `Vt`, **온도에 지수적** — scaling으로 `Vt`↓ 하며 급증 |
| **Gate leakage** | 얇은 oxide를 통한 **tunneling** | thin oxide에서 증가, **High-k dielectric**으로 완화 |
| **Junction leakage** | reverse-biased p–n junction | drain–body 전압, junction 면적·둘레; **GIDL**이 악화 |

subthreshold current: `I ∝ exp((Vgs − Vt)/(n·kT/q))`, **subthreshold swing** S ≈ 60–100 mV/decade.

---

## 7. Process Variation

실제 소자 특성이 공정 변화로 달라짐:
- effective channel length, threshold voltage, oxide thickness
- nMOS/pMOS mobility, 온도, supply voltage

→ **corner simulation** (TT / FF / SS / FS / SF, 그리고 V·T corner)으로 worst-case speed·leakage·noise margin 확인.

---

## 8. Noise Margin

logic level은 이상적 0/1이 아니라 **noise tolerance**를 가진다.

| 기호 | 의미 |
|---|---|
| `VOH` | 출력 high로 보장되는 **최소** 전압 |
| `VOL` | 출력 low로 보장되는 **최대** 전압 |
| `VIH` | 입력 high로 인식되는 최소 전압 (VTC 기울기 −1 점) |
| `VIL` | 입력 low로 인식되는 최대 전압 (VTC 기울기 −1 점) |

```text
NMH = VOH − VIH        (high 쪽 noise margin)
NML = VIL − VOL        (low 쪽 noise margin)
```

크게 하려면: logic level이 **rail-to-rail**에 가깝고, switching threshold `VM`이 `VDD/2` 근처로 균형.

---

## 시험·복습 체크포인트

- Cutoff / linear / saturation 영역 조건을 쓸 수 있는가?
- Gate capacitance와 diffusion capacitance가 각각 power와 delay에 주는 영향은?
- Subthreshold / gate / junction leakage를 원인으로 구분할 수 있는가? subthreshold swing의 의미는?
- Velocity saturation이 square-law 모델을 어떻게 깨뜨리는가? body effect로 `Vt`가 변하는 상황 예시는?
- `VOH, VOL, VIH, VIL, NMH, NML`을 정의하고, noise margin을 키우는 조건은?
{% endraw %}

---

이전: [01. CMOS 개요와 설계 흐름](01-cmos-overview-and-design-flow.md) · 다음: [03. 지연 모델과 Logical Effort](03-delay-models-and-logical-effort.md)
