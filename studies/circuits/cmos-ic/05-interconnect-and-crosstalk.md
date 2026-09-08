---
layout: page
title: "05. 배선 모델과 Crosstalk (Interconnect)"
permalink: /studies/circuits/cmos-ic/05-interconnect-and-crosstalk/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `05 Wire — 배선 모델과 Crosstalk` 보강

{% raw %}
## 개요

Chip interconnect의 **resistance, capacitance, crosstalk, repeater insertion**. 현대 CMOS에서는 transistor만큼 **wire가 delay·power를 지배**한다. 긴 wire의 RC delay는 길이의 **제곱**에 비례 → repeater로 분할해 완화.

---

## 1. Interconnect의 중요성

chip은 transistor로 이뤄지지만, 실제 layout에서는 여러 metal layer의 wire가 **대부분의 면적·parasitic**을 차지. scaling 이후 **gate delay보다 wire delay가 병목**인 경우가 많다 (특히 global net).

---

## 2. Wire Geometry

wire 특성 결정 요소: **폭(W), 두께(T), 길이(L), 이웃 wire와의 간격(S)**.
- 좁고 긴 wire → resistance ↑
- 이웃 wire와 가까움 → **coupling capacitance ↑**
- 상하 metal layer·substrate와도 capacitance 형성

---

## 3. Wire Resistance

```text
R = ρ · L / A = ρ · L / (W · T)     [Ω]
sheet resistance:  R_□ = ρ / T  →  R = R_□ · (L / W)
```

| 요소 | 변화 | R |
|---|---|---|
| 길이 `L` ↑ | 전류 경로 길어짐 | ↑ |
| 단면적 `A` ↑ | 경로 넓어짐 | ↓ |
| resistivity `ρ` ↑ | 재료가 덜 도전적 | ↑ |

- 과거 **aluminum** → scaling·성능 요구로 **copper interconnect**(ρ 낮음, electromigration 강함)로 전환.
- **diffusion runner**는 R·C 모두 커서 긴 배선 부적합. **polysilicon**도 gate엔 필수지만 긴 wire엔 R이 큼.

---

## 4. Wire Capacitance

단순 parallel-plate만으로 설명 안 됨 — **fringe field + neighbor coupling**이 중요.

성분:
- wire ↔ substrate capacitance
- wire ↔ 위·아래 metal layer capacitance
- **인접 wire와의 coupling capacitance** (간격이 좁아진 현대 공정에서 지배적)

---

## 5. Distributed RC와 Elmore Delay

wire는 길이 방향으로 R·C가 분포된 **distributed system**. 간단 분석: **π-model** 또는 single-segment로 근사 + **Elmore delay**.

긴 wire의 **unrepeated RC delay ∝ L²**:

```text
t_wire ≈ 0.5 · R_wire · C_wire ∝ L²
       (R_wire = r·L, C_wire = c·L  →  0.5·r·c·L²)
```

---

## 6. Crosstalk

인접 wire 사이 **coupling capacitance** 때문에 한 wire(aggressor)의 switching이 다른 wire(victim)에 영향.

효과:
- **nonswitching victim**: noise pulse 발생 → 심하면 **false switching**, latch 오입력
- **switching victim**: aggressor와 **반대 방향** 전이 시 delay **증가**, **같은 방향** 전이 시 delay **감소**

### Crosstalk Delay — Miller factor

worst-case timing에서 coupling capacitance가 **Miller factor `k`**로 확대되어 보임:

```text
C_effective = C_ground + k · C_coupling
```

| aggressor 상태 | `k` |
|---|---|
| 정지 (constant) | 1 |
| victim과 **같은 방향** 전이 | ≈ 0 |
| victim과 **반대 방향** 전이 | ≈ 2 |

대응: **shielding**(양옆에 VDD/GND wire), spacing 확대, aggressor slew 제어, staggered repeater, net ordering.

---

## 7. Repeater Insertion

긴 wire를 하나의 driver가 구동하면 RC delay(∝L²)가 과도 → **repeater**(inverter buffer)로 wire를 여러 segment로 분할.

```text
long wire  →  [driver] seg [rpt] seg [rpt] seg ... [rpt] seg [load]
```

길이 `L`을 `N` segment로 나누면 각 segment `L/N` → 총 wire delay가 **`L²/N` + N·(repeater delay)** 형태 → **선형에 가깝게** 완화.

### Repeater Design

| 값 | 의미 |
|---|---|
| `N` | repeater 개수 / segment 수 |
| `W` | repeater inverter width |

- 너무 적으면 wire RC delay 지배, 너무 많으면 **repeater parasitic·power** 지배 → 최적점 존재 (wire r·c와 inverter R·C로 결정).
- 최적: `N_opt ∝ L·√(rc / (R_inv C_inv))`, `W_opt ∝ √(R_inv c / (r C_inv))`.

### Repeated Wire의 의미

properly repeated wire의 **delay per unit length**는 unrepeated long wire보다 훨씬 작다(제곱 → 선형). 단 repeater가 **area + dynamic power**를 추가하므로 **global interconnect / timing-critical long net**에 선택적으로 사용.

---

## 시험·복습 체크포인트

- Wire resistance가 `L`, `A`, `ρ`에 어떻게 의존하는가? sheet resistance `R_□`의 의미는?
- Wire capacitance에서 coupling capacitance가 현대 공정에서 지배적인 이유는?
- Unrepeated wire delay가 `L²`에 비례하는 이유를 `R_wire·C_wire`로 유도할 수 있는가?
- Crosstalk이 victim wire에 noise와 delay 변화를 만드는 과정, Miller factor `k`가 0/1/2가 되는 조건은?
- Repeater insertion의 delay/power/area tradeoff와, 어떤 wire에 쓰는지?
{% endraw %}

---

이전: [04. 전력과 저전력 설계](04-power-and-low-power-design.md) · 다음: [06. Scaling · Reliability · Variability](06-scaling-reliability-and-variability.md)
