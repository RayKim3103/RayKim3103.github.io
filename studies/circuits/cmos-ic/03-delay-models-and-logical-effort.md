---
layout: page
title: "03. 지연 모델과 Logical Effort"
permalink: /studies/circuits/cmos-ic/03-delay-models-and-logical-effort/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `03 Speed — 지연 모델과 Logical Effort` 보강

{% raw %}
## 개요

CMOS gate의 속도를 **propagation / contamination delay**, **RC delay model**, **Elmore delay**, **logical effort**로 분석. 핵심: transistor·wire를 R/C로 근사해 delay를 빠르게 추정하고, multistage path에서 **stage 수와 gate size**를 최적으로 고른다.

---

## 1. Delay 정의

| 기호 | 의미 |
|---|---|
| `tpdr` / `tpdf` | rising / falling **propagation** delay |
| `tpd` | 평균 propagation delay = `(tpdr + tpdf)/2` |
| `tcdr` / `tcdf` | rising / falling **contamination** delay |
| `tcd` | 평균 contamination delay = `(tcdr + tcdf)/2` |

- **Propagation delay** = 출력이 **최종적으로** 바뀌는 늦은 경우 (max-delay 분석에 사용).
- **Contamination delay** = 입력 변화가 출력에 **처음** 영향을 주는 빠른 경우 (min-delay / hold 분석에 사용).
- 측정 기준: 입력·출력 50% 지점 사이.

---

## 2. RC Delay Estimation

SPICE가 가장 정확하지만 초기 설계는 RC model로.

기본 가정:
- Unit nMOS: 저항 `R`, capacitance `C`
- Unit pMOS: mobility 낮아 저항 `≈ 2R` (같은 width)
- width `k`배 → 저항 `R/k`, capacitance `kC`

**기준 단위**: fanout-of-1 inverter delay. 다른 gate delay를 이 배수로 표현.

예 — unit inverter가 3-unit load(`3C`) 구동: `tpd ≈ RC·3` + 자기 diffusion.

---

## 3. Elmore Delay

pull-up / pull-down network를 **RC ladder**로 모델링:

```text
t_Elmore = Σ_i  R_(i에서 source까지 공유 저항) × C_i
```

각 capacitor `C_i`마다, 입력 source에서 그 노드까지 **경로에서 공유되는 저항**을 곱해 전부 더한다. 정확한 waveform 해석보다 단순하지만 **RC tree의 지연 직관**을 잘 준다. series stack(예: NAND의 nMOS 2개)에서 내부 노드 capacitance까지 고려하는 데 유용.

---

## 4. Logical Effort 모델

gate delay를 **effort delay + parasitic delay**로 분해:

```text
d = f + p            (normalized, τ 단위)
f = g · h            (effort delay = stage effort)
```

| 기호 | 의미 |
|---|---|
| `g` | **logical effort** — 같은 출력 drive를 내는 inverter 대비 입력 capacitance 비 |
| `h` | **electrical effort** (= fanout) = `Cout / Cin` |
| `f` | effort delay (stage effort) `= g·h` |
| `p` | **parasitic delay** — self-loading(diffusion) 때문, load와 무관 |
| `d` | normalized stage delay |

### 대표 logical effort 값 (1-input 기준, γ=2)

| gate | `g` | `p` |
|---|---|---|
| inverter | 1 | 1 |
| 2-input NAND | 4/3 | 2 |
| 2-input NOR | 5/3 | 2 |
| 2-input XOR | 4 | 4 |
| n-input NAND | (n+2)/3 | n |
| n-input NOR | (2n+1)/3 | n |

- **NAND < NOR**: NOR는 pMOS series → 같은 drive를 내려면 pMOS를 크게 → 입력 capacitance ↑ → `g` ↑.
- gate 입력마다 diffusion 위치·stack 위치에 따라 parasitic delay가 다를 수 있음.

---

## 5. FO4 Inverter

**FO4** = fanout-of-4 inverter delay. process-independent **delay 단위**로 자주 사용.

```text
FO4 inverter:  g = 1, h = 4  →  d = g·h + p = 4 + 1 = 5τ
```

서로 다른 공정/설계의 속도를 비교하는 rough metric. (통상 논리 경로는 수십 FO4.)

---

## 6. Multistage Logical Effort

N-stage path의 전체 effort:

| 항목 | 정의 |
|---|---|
| **Path logical effort** `G` | `Π g_i` (각 stage `g`의 곱) |
| **Path electrical effort** `H` | `C_out(path) / C_in(path)` |
| **Branching effort** `B` | `Π b_i`, `b = (C_on-path + C_off-path)/C_on-path` (경로 밖으로 갈라지는 load) |
| **Path effort** `F` | `F = G · B · H` |
| **Path parasitic delay** `P` | `Σ p_i` |

---

## 7. 최적 Stage Effort

N-stage path에서 delay 최소 → **각 stage가 같은 stage effort**를 부담:

```text
f̂ = F^(1/N)                (stage마다 동일)
D_min = N · F^(1/N) + P
```

- 실무적으로 **stage effort ≈ 3.6~4** 근처(≈ e = 2.718이 이론적 최적이나 완만)일 때 빠름.
- delay는 최적 stage 수/size에서 다소 벗어나도 **크게 나빠지지 않음** (완만한 최소).
- 최적 stage 수: `N ≈ log₄ F`.

---

## 8. Gate Sizing 절차

1. Path logical effort `G = Π g_i`
2. Path electrical effort `H = C_out / C_in`
3. Branch가 있으면 `B = Π b_i`
4. `F = G·B·H`
5. Stage 수 `N` 결정 → `f̂ = F^(1/N)`
6. **Load에서 입력 쪽으로 거꾸로**: `C_in,i = g_i · C_out,i / f̂` → width 산출

---

## 9. Logical Effort의 한계

- delay model이 단순 (선형 RC, step input 가정)
- **interconnect delay가 큰 회로**에서는 반복 보정 필요
- **최소 delay**를 주지만 최소 area / 최소 power는 보장 안 함
- 초미세 공정에서는 velocity saturation, coupling, variation이 더 중요

---

## 시험·복습 체크포인트

- Propagation delay와 contamination delay의 차이, 각각 어느 타이밍 제약에 쓰이는가?
- Elmore delay 계산 원리를 NAND의 nMOS stack 예로 설명할 수 있는가?
- `d = g·h + p`에서 `g, h, p`의 의미와, inverter/NAND2/NOR2의 `g` 값은?
- `F = G·B·H`, `f̂ = F^(1/N)`, `D_min = N·f̂ + P`를 써서 multistage delay를 추정할 수 있는가?
- NAND가 NOR보다 빠른 이유를 pMOS stack / logical effort로 설명할 수 있는가?
{% endraw %}

---

이전: [02. MOS 소자 모델과 비이상성](02-mos-device-models-and-nonidealities.md) · 다음: [04. 전력과 저전력 설계](04-power-and-low-power-design.md)
