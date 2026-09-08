---
layout: page
title: "04. CMOS 전력과 저전력 설계 (Power · Low-Power · MTCMOS)"
permalink: /studies/circuits/cmos-ic/04-power-and-low-power-design/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `04 Power — CMOS 전력소모와 저전력기법` + `03_1 Low Power Techniques — 저전력 설계 보강` + `06_1 MTCMOS and Power Gating — 한글날 보강` **통합**
- 세 자료 모두 CMOS power/leakage를 다루고 `P_dynamic = αCV_DD²f` 유도를 공유합니다. 본 노트는 **power/energy 기초 → dynamic/short-circuit/static → architecture·logic·circuit level 저감 → MTCMOS/power gating**을 한 흐름으로 정리했습니다.

{% raw %}
## 개요

CMOS 전력은 **dynamic + short-circuit + static(leakage)**. dynamic은 switching activity·capacitance·`V_DD`·frequency로 결정되고, static은 subthreshold·gate·junction leakage가 원인. 저감은 **architecture / logic / circuit** 세 계층에서 이뤄지며, sleep-mode leakage 차단의 대표 구조가 **MTCMOS + power gating**이다.

---

# Part 1. Power와 Energy 기초

## 1. 정의

```text
P(t) = i(t) · V_DD
P_avg = E / T
```

CMOS 회로는 `V_DD` pin에서 에너지를 공급받아 **switching + leakage**로 소모한다. **Energy**(J)는 배터리 수명에 직결, **Power**(W)는 발열·전원 설계에 직결.

## 2. Average Switching (Dynamic) Power

capacitor를 0 → `V_DD`로 충전할 때 전원이 공급하는 에너지 = `C·V_DD²`. 그중 **절반(`½C V_DD²`)만 capacitor에 저장**, 나머지 절반은 pMOS 저항에서 **열**로 소모. 방전 시 저장 에너지는 nMOS network에서 소모.

한 번의 0→1→0 사이클당 소모 에너지 = `C·V_DD²`.

```text
P_dynamic = α · C · V_DD² · f
```

| 항 | 의미 | 저감 방법 |
|---|---|---|
| `α` | **activity factor** — 한 clock에 switching이 일어나는 확률 | glitch 억제, clock gating, operand isolation, 코딩 |
| `C` | switching node capacitance | sizing 축소, wire 단축, fanout 축소 |
| `V_DD` | supply voltage | **VDD scaling (제곱 효과)**, dual-VDD |
| `f` | clock frequency | frequency scaling (선형) |

→ **`V_DD` 감소가 제곱 효과로 가장 강력**.

## 3. Short-Circuit Power

입력이 전이하는 **유한한 시간** 동안 pMOS와 nMOS가 **동시에 ON** → `V_DD`→GND 직접 전류 경로.

- 입력 transition이 **느릴수록**, rise/fall balance가 나쁠수록 short-circuit current ↑.
- 통상 전체 dynamic power의 ~10% 이하로 관리 (입력 slew를 출력 slew와 비슷하게 유지).

## 4. Power Dissipation Sources 요약

| 종류 | 원인 |
|---|---|
| **Dynamic** | load capacitance 충방전 |
| **Short-circuit** | switching 중 pMOS/nMOS 동시 ON |
| **Static** | leakage current (subthreshold + gate + junction) |

---

# Part 2. Dynamic Power 저감

## 5. Activity Factor (`α`) 감소

- 불필요한 switching 제거
- **glitch 억제** (§8)
- **clock gating** — 사용하지 않는 register의 clock을 정지 (α → 0)
- **operand isolation** — 비활성 block 입력을 latch로 고정해 내부 전이 차단

## 6. Capacitance (`C`) 감소

- transistor sizing 축소 (특히 non-critical path는 작게)
- 긴 wire 축소, floorplan으로 거리 단축
- high-fanout node 분산

## 7. VDD / Frequency Scaling

- **VDD scaling**: `V_DD²` 항으로 가장 효과적. 단 delay ↑ → **timing slack이 있는 block** 또는 **architecture-level 병렬화/파이프라인**과 함께.
- **Frequency scaling**: dynamic power를 **선형** 감소. 목표 throughput이 낮거나 workload가 작을 때. (DVFS = 둘을 동적으로 조절)

---

# Part 3. Architecture-Level Low Power (`03_1` 보강)

같은 throughput을 유지하면서 **낮은 `V_DD`로 동작**할 수 있게 구조를 바꾼다.

## 8. Parallelism

연산기를 N개 병렬화 → 각 연산기는 `f/N`로 동작해도 전체 throughput 유지 → 생긴 frequency 여유를 **`V_DD` 낮추는 데** 사용 → `CV_DD²` 효과로 power 대폭 감소.
- **대가**: 병렬 hardware만큼 **capacitance·area 증가**, mux/routing 추가.

## 9. Pipelining

pipeline register 삽입 → critical path 단축 → 낮은 `V_DD`에서도 목표 frequency 달성.
- **대가**: register의 **clock power + area** 추가.

## 10. Arithmetic Computation Scaling

adder-comparator datapath 예제: architecture 변환으로 delay를 줄인 뒤 `V_DD`를 낮춤. **extra latch로 capacitance가 약간 늘어도 `V_DD` 감소 효과가 더 크면** 전체 dynamic power 감소.

```text
tradeoff:  추가 hardware/register capacitance ↑   vs   낮은 V_DD로 CV_DD² ↓
```

## 11. Signal Dynamic Range와 Switching Activity

- 신호의 dynamic range가 작고, 인접 sample 사이 **음의 상관관계**가 크면 transition 수 감소.
- **shift 연산 = scaling 연산** → signal dynamic range를 줄이는 효과.
- switching activity 감소 = dynamic power 직접 감소 (`P_dyn = αCV_DD²f`).

## 12. Glitching Activity

static CMOS에서도 logic block 사이 **propagation delay 불균형** → glitch(불필요한 중간 전이).

원인: 입력 도착 시간 불균형, **reconvergent fanout**, dynamic hazard, critical race.
→ 최종 논리값이 안 바뀌어도 내부 node를 충방전 → power ↑.
대응: path delay balancing, logic depth 축소, **재수렴 경로 정렬**.

## 13. Logic Depth vs Register Power

logic depth를 줄이면 glitch가 줄 수 있으나, pipeline register가 많아지면 **clock power + register capacitance** 증가. → combinational depth, glitch activity, register overhead를 **함께** 봐야 함.

## 14. Resource Sharing의 전력 Tradeoff

물리 adder 2개 vs time-multiplexed adder 1개 공유 — 항상 한쪽이 유리하진 않음:
- 물리 adder 多 → hardware capacitance ↑
- 공유 adder → mux·control·switching pattern ↑
- 입력이 고정·반복되는 정도에 따라 activity가 달라짐

---

# Part 4. Circuit-Level Low Power

## 15. 선택지 개요

static vs dynamic style · pass gate vs 일반 CMOS · transistor sizing · supply voltage 조절 · **threshold voltage 조절** · power gating 계열.

## 16. Dual VDD

빠른 timing이 필요한 gate엔 `V_DDH`, slack 있는 gate엔 `V_DDL`.

| 영역 | 사용 |
|---|---|
| `V_DDH` | critical path, speed 중요 cell |
| `V_DDL` | noncritical path, power saving 큰 cell |

**주의**: `V_DDL` 출력이 `V_DDH` gate를 직접 구동하면 pMOS가 완전히 꺼지지 않아 **static current** 발생 → **level converter** 필요 → area/power overhead. (보통 `V_DDL`→`V_DDH` 경계와 register 위치에 level converter 배치.)

---

# Part 5. Static Power와 Leakage

## 17. Static Power

```text
P_static = I_leakage · V_DD
```

chip이 quiescent 상태여도 소모. scaling으로 `Vt`↓, `tox`↓ 하며 **비중이 커짐** (한때 전체 power의 30~40%까지).

## 18. Subthreshold Leakage Control

OFF transistor를 흐르는 전류. **delay ↔ leakage tradeoff**.

저감:
- **높은 `Vt`** 사용 (느려짐)
- `Vgs`를 음수/0 이하로 유지 (reverse-biased)
- **source–body bias** 조절 (RBB: reverse body bias → `Vt`↑)
- **stack effect** 활용
- sleep mode에서 **power gating**

## 19. Stack Effect (`06_1` 보강)

여러 OFF transistor가 **series**로 연결되면 leakage 감소. 내부 virtual node 전압이 상승/하강하면서 각 OFF transistor의 effective `Vgs`(음수화), `Vds`(감소), body bias가 **leakage를 줄이는 방향**으로 바뀜.

```text
single OFF transistor leakage  >  stacked OFF transistor leakage   (수배~10배)
```

**Forced stack** = leakage를 줄이려고 하나의 transistor를 둘로 나눠 의도적 series stack 생성. 대가: series 저항 ↑ → delay ↑.

## 20. Gate Replacement

worst-leakage input state에서, 논리 기능은 유지하면서 leakage가 더 작은 library gate로 교체.
조건: ① 논리 기능 유지 ② worst leakage state에서 leakage 감소 ③ delay·area overhead 허용 범위.

## 21. Gate Leakage / Junction Leakage

- **Gate leakage**: thin-oxide tunneling. 65 nm 이하에서 중요. **High-k dielectric** = 물리적으로 두꺼운 막 + 높은 gate capacitance 유지 → tunneling ↓.
- **Junction leakage**: reverse-biased p–n junction. high-`Vt` transistor에서 다른 leakage가 작을 때 상대적으로 드러남. **GIDL**(Gate-Induced Drain Leakage)이 drain 조건에서 악화.

---

# Part 6. MTCMOS와 Power Gating (`06_1` 보강)

## 22. MTCMOS 기본 구조

**MTCMOS** (Multi-Threshold CMOS): logic엔 빠른 **low-`Vt`** transistor, power rail 차단엔 leakage 작은 **high-`Vt` sleep transistor**.

| Mode | sleep transistor | virtual rail | 결과 |
|---|---|---|---|
| **Active** | ON | `virtual V_DD ≈ V_DD`, `virtual GND ≈ GND` | low-`Vt` logic이 빠르게 동작 |
| **Sleep** | OFF | virtual rail floating | block leakage를 high-`Vt` switch가 제한 → 크게 감소 |

## 23. Performance Constraint

sleep transistor는 active mode에서 **series 저항**처럼 작용 → virtual rail이 ideal rail과 달라짐 → logic의 effective supply 감소 → **delay 증가**.

```text
V_eff = V_DD − (sleep switch 양단 전압 강하)
```

sleep switch width ↑ → delay penalty ↓ 이지만 **area·gate capacitance ↑**.

## 24. Header vs Footer Switch

| 방식 | 위치 | 특징 |
|---|---|---|
| **Headswitch** | `V_DD`와 logic 사이, 보통 **pMOS** | leakage 차단, pMOS라 면적 큼 |
| **Footswitch** | logic과 GND 사이, 보통 **nMOS** | drive 강하고 면적 효율적 |

- **Local footswitch**: 작은 block마다 switch → 제어 세밀, area overhead 큼.
- **Global footswitch**: 큰 영역을 하나의 switch network로 공유 → 효율적, **rail bounce·wake-up control** 중요.

## 25. Sleep Transistor Sizing

목표: **active delay 증가를 제한하면서 leakage saving을 충분히**.
고려: peak current, virtual rail 전압 강하, **wake-up time**, **rush current**, area, sleep switch gate capacitance.
- **SOR** (Size Of Ratio) 개념: sleep switch의 상대 크기가 delay와 leakage에 영향. 큰 switch → delay ↓, power/area overhead ↑.

## 26. Data Holding (State Retention)

power gating으로 block 전원이 꺼지면 **register state 소실**. 방지:
- **data holding flip-flop** / **balloon latch** (항상 켜진 `V_DD`에 연결된 작은 shadow latch)
- **leakage feedback flip-flop**
- **intermittent power supply**
- **virtual power/ground clamp**

retention 회로는 **sleep 중 필요한 상태만** 저장, wake-up 후 정상 동작 재개.

## 27. Power Gating Transition 문제

conventional power gating은 sleep → active 전환 시 **큰 current spike + on-chip power distribution noise**:
- virtual rail 충전으로 **rush current**
- **supply bounce / ground bounce** (Ldi/dt)
- **wake-up delay**
- retention state 복원 timing

## 28. 개선된 Power Gating Scheme

- **intermediate power-saving mode** (완전 차단 전 중간 단계)
- **zigzag / staged (다단계) wake-up** — sleep transistor를 순차적으로 켜서 rush current 분산

공통 목표: sleep leakage ↓ · active 성능 유지 · **wake-up current 분산** · virtual rail fluctuation ↓.

---

## 시험·복습 체크포인트

- `P_dynamic = α·C·V_DD²·f`의 각 항과, 항마다의 저감 기법을 연결할 수 있는가?
- Dynamic / short-circuit / static power를 원인으로 구분하고, 한 사이클당 dynamic 소모 에너지가 `CV_DD²`인 이유는?
- Parallelism·pipelining이 낮은 `V_DD` 동작을 가능하게 하는 메커니즘은? 각각의 대가는?
- Glitch가 power를 늘리는 과정, register 추가가 항상 power를 줄이지 않는 이유는?
- Dual-VDD에서 level converter가 필요한 이유는?
- Stack effect가 leakage를 줄이는 물리적 이유, forced stack의 대가는?
- MTCMOS에서 low-`Vt` logic과 high-`Vt` sleep transistor의 역할 분담, active mode의 delay penalty 식은?
- Header vs footer, local vs global footswitch의 차이는? power gating의 retention·wake-up noise 문제와 staged wake-up의 목적은?
{% endraw %}

---

이전: [03. 지연 모델과 Logical Effort](03-delay-models-and-logical-effort.md) · 다음: [05. 배선 모델과 Crosstalk](05-interconnect-and-crosstalk.md)
