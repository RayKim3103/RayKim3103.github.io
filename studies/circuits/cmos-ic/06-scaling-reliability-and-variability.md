---
layout: page
title: "06. Scaling · Reliability · Variability"
permalink: /studies/circuits/cmos-ic/06-scaling-reliability-and-variability/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `06 Scaling Reliability Variability — 스케일링 신뢰성 변동성` 보강

{% raw %}
## 개요

CMOS scaling이 device·interconnect·power·reliability·variability에 미치는 영향. **Moore's law**와 **constant-field scaling**의 이상적 그림 → 실제 scaling에서 **oxide tunneling, wire delay, leakage, power density, design productivity**가 한계로 등장.

---

## 1. Moore's Law

일정 기간(≈2년)마다 chip에 집적 가능한 transistor 수가 약 2배. scaling은 더 많은 transistor·더 작은 gate·더 높은 기능 집적을 가능하게 했지만 **전력·배선 문제**를 함께 키웠다.

---

## 2. Constant-Field Scaling (이상적)

모든 치수와 전압을 같은 비율 `S`(>1)로 축소해 **전계(E-field)를 일정하게** 유지.

| 파라미터 | scaling |
|---|---|
| gate length `L`, width `W`, oxide `tox` | `1/S` |
| supply `V_DD`, threshold `Vt` | `1/S` |
| gate capacitance `C` (`= εWL/tox`) | `1/S` |
| gate delay `~ CV/I` | `1/S` |
| power/gate `~ CV²f` | `1/S²` |
| **power density** | ≈ 일정 |
| 집적도 (gate/area) | `S²` |

→ 이상적으로는 **성능·전력이 함께 개선**. 하지만 현실은 여러 물리 한계로 이렇게 진행되지 않음.

---

## 3. Real Scaling

- **oxide thickness scaling 둔화**: gate oxide가 수 원자층까지 얇아지면 **tunneling current 급증**(gate leakage). → **High-k dielectric**: 물리적으로 두꺼운 막을 쓰면서 높은 gate capacitance 유지.
- `V_DD` scaling 둔화: `Vt`를 무한정 낮출 수 없음(subthreshold leakage). → `V_DD/Vt` 비가 나빠져 gate overdrive 부족.

---

## 4. Wire Scaling

| Wire | 특징 |
|---|---|
| **Local interconnect** | transistor 근처 짧은 연결, device scaling과 함께 줄어듦 |
| **Global interconnect** | chip 전체를 가로지르는 긴 연결, **길이가 충분히 줄지 않음** |

- wire 단면적 ↓ → **resistance per unit length ↑**.
- total wire capacitance·RC delay는 geometry·coupling에 따라 복잡하게 변함.
- **Interconnect delay**: unrepeated wire delay가 커짐 → repeated wire로 완화하나 repeater area·power 추가. transistor gate delay는 줄어도 **global wire delay는 상대적으로 나빠짐**.

---

## 5. Scaling Implications

### Interconnect Woes
wire delay가 특정 공정 세대 이후 **전체 성능 병목**. long wire는 transistor가 빨라져도 더 빨라지지 않거나 오히려 나빠짐 → **floorplanning, buffering, hierarchy, locality**가 매우 중요.

### Power Woes
- **Dynamic power density**: clock frequency·transistor 수 증가로 문제, `V_DD` scaling 둔화로 완화 폭 감소.
- **Static leakage**: `Vt`↓ + gate oxide tunneling으로 증가.

## 6. Static Power / Leakage 증가

낮은 `V_DD`에서 성능 유지 위해 `Vt`를 낮추는 경향 → OFF 상태 **subthreshold leakage 증가**. thin oxide → **gate leakage 증가**. 결과적으로 **standby power**가 핵심 설계 문제 (모바일에서 특히). → [04](04-power-and-low-power-design.md)의 MTCMOS/power gating.

---

## 7. Reliability 문제

scaling이 키우는 이슈:

| 메커니즘 | 설명 |
|---|---|
| **Oxide breakdown (TDDB)** | 얇은 oxide에 높은 전계 → 시간 경과 절연 파괴 |
| **Hot carrier degradation (HCI)** | 고에너지 carrier가 oxide에 trap → `Vt` shift, `gm` 저하 |
| **Electromigration** | 높은 전류밀도가 metal atom을 이동 → void/hillock → 단선/단락 |
| **Bias Temperature Instability (NBTI/PBTI)** | bias·온도 stress로 `Vt` 시간 열화 |
| **Supply noise / IR drop** | 전류 급변·배선 저항으로 국부 `V_DD` 강하 → delay 변동 |

작은 device일수록 전압·온도·공정 변화에 **더 민감**.

---

## 8. Variability

공정 변동으로 transistor·wire 특성이 **die마다, die 내 위치마다** 다름.

| 변동 원인 | |
|---|---|
| effective channel length | line edge roughness |
| threshold voltage | **random dopant fluctuation (RDF)** |
| oxide thickness | wire width/thickness |

- **systematic** (레이아웃 의존, lithography) vs **random** (RDF 등).
- variability가 커지면 **timing closure와 yield**가 어려워짐 → guard band, statistical STA, on-chip monitor.

---

## 9. Design Productivity

transistor 수는 빠르게 증가하지만, 설계자가 검증·구현 가능한 gate 수는 같은 속도로 늘지 않음 (**design gap**). → **HDL synthesis, reusable IP, hierarchical design, standard cell methodology**가 필수.

---

## 시험·복습 체크포인트

- Constant-field scaling에서 delay, power/gate, power density, 집적도가 각각 어떻게 변하는가?
- Real scaling에서 oxide thickness가 더 줄기 어려운 이유와 High-k의 역할은?
- Interconnect delay가 scaling 이후 병목이 되는 이유, global vs local wire의 차이는?
- TDDB / HCI / electromigration / NBTI를 각각 한 줄로 설명할 수 있는가?
- Random dopant fluctuation이 왜 소자가 작아질수록 심해지는가? variability가 yield에 주는 영향은?
{% endraw %}

---

이전: [05. 배선 모델과 Crosstalk](05-interconnect-and-crosstalk.md) · 다음: [07. 고급 CMOS Gates](07-advanced-cmos-gates.md)
