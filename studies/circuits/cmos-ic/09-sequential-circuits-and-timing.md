---
layout: page
title: "09. 순차회로 설계와 Timing"
permalink: /studies/circuits/cmos-ic/09-sequential-circuits-and-timing/
sitemap: false
---

- **원본**: [GitHub — CMOS Integrated Circuit](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit) · `09 Sequential Circuit Design — 순차회로와 Timing` 보강

{% raw %}
## 개요

순차회로의 **sequencing element 설계**와 **timing constraint**. latch/flip-flop 구조, C2MOS latch, pulsed latch, enabled/resettable element, **setup/hold, max-delay / min-delay, time borrowing, clock skew**.

---

## 1. Sequencing의 필요성

combinational logic은 입력이 바뀌면 delay 후 출력이 바뀐다. 실제 회로는 **stage마다 logic delay가 다름** → sequencing element가 빠른 token을 지연시켜 **한 clock cycle에 정확히 한 stage씩** 이동하게 한다.

```text
combinational logic + storage element + clock = synchronous system
```

---

## 2. Sequencing Overhead

flip-flop/latch는 slow token에도 delay를 더한다 → cycle time은 logic delay만이 아니라 **storage element overhead**를 포함:

```text
T_cycle ≥ t_pcq + t_pd(logic) + t_setup + t_skew
```

---

## 3. Latch vs Flip-Flop

| 요소 | 동작 |
|---|---|
| **Latch** | clock **level** 민감 — transparent(통과) / opaque(유지) |
| **Flip-flop** | clock **edge** 민감 — edge 근처에서만 캡처 |

---

## 4. Latch Design

| 구조 | 특징 |
|---|---|
| **Pass transistor latch** | data path를 pass TR로 개폐. 단순하나 **threshold drop / degraded level** |
| **Transmission gate latch** | nMOS+pMOS로 0·1 모두 잘 전달. CMOS latch의 기본 |
| **Tristate feedback latch** | 입력 path와 feedback path를 clock에 따라 교대로 ON → transparent/hold |
| **Buffered I/O** | 입출력에 inverter buffer → drive·noise margin 개선, 그러나 delay·capacitance ↑ |

### C2MOS Latch
**Clocked CMOS** — clocked transistor + inverter 구조로 **race를 줄이고** clock phase에 따라 저장. clocked device 배치로 transparency/hold 특성 결정. (C2MOS-C2MOS 캐스케이드는 clk/clk̄ skew에 강함.)

---

## 5. Flip-Flop Design

```text
master latch + slave latch = edge-triggered flip-flop
```

positive edge-triggered: clock이 0→1 되는 순간 **master 닫히고 slave 열림** → edge에서 값 전달. (master는 clk=0에서 transparent, slave는 clk=1에서 transparent.)

---

## 6. Pulsed Latch

짧은 **clock pulse** 동안만 transparent. 외부에서 보면 flip-flop처럼 동작하나 latch 기반이라 **일부 time borrowing 가능**.

tradeoff: flip-flop보다 빠를 수 있음 · **hold time 요구 증가** · **pulse width 설계가 관건**.

---

## 7. Enabled / Resettable / Settable Elements

- **Enabled** latch/FF: enable이 켜졌을 때만 새 값 캡처 (clock gating 또는 mux feedback로 구현).
- **Resettable**: reset 입력으로 0 초기화. **Settable**: 1 초기화.
- **Asynchronous set/reset**: clock과 무관하게 동작 → **metastability와 release timing** 주의(recovery/removal time).

## 8. Incorporating Logic into Latches
간단한 logic을 latch 내부에 흡수 → gate stage 절약. 단 storage node의 noise margin·setup/hold·clock loading이 달라져 **재특성화 필요**.

---

## 9. Timing Delay 종류

| 기호 | 의미 |
|---|---|
| `t_pcq` | clock edge → Q가 **안정**될 때까지 propagation delay |
| `t_ccq` | clock edge 후 Q가 **변하기 시작**하는 contamination delay |
| `t_setup` | clock edge **전** D가 안정되어야 하는 시간 |
| `t_hold` | clock edge **후** D가 유지되어야 하는 시간 |
| `t_pd` / `t_cd` | combinational logic propagation / contamination delay |

---

## 10. Max-Delay Constraint (setup)

data가 다음 element의 setup time 전에 도착 못 하면 **max-delay(setup) failure**.

```text
T_cycle ≥ t_pcq + t_pd(logic) + t_setup + t_skew
```

→ 위반 시 combinational logic을 나누거나(pipeline), gate sizing, cycle time 완화.

## 11. Min-Delay Constraint (hold)

data가 **너무 빨리** 도착해 현재 edge의 hold time을 깨면 **min-delay(hold) failure**:

```text
t_ccq + t_cd(logic) ≥ t_hold + t_skew
```

→ 두 FF 사이 logic이 거의 없거나 clock skew가 불리하면 발생. **buffer(delay) 삽입**으로 수정 (cycle time과 무관하게 항상 고쳐야 함).

---

## 12. 2-Phase Latch Timing

겹치지 않는(non-overlapping) 두 clock phase 사용. latch가 transparent인 동안 data 통과 → **time borrowing 가능**, 단 **race-through**(두 phase가 겹치면 data가 두 latch를 관통)와 hold 조건 주의. non-overlap time이 race 방지 마진.

## 13. Time Borrowing

flip-flop system: 각 stage logic이 **한 cycle 안에** 끝나야 함.
latch system: 한 stage가 조금 늦어도 **다음 latch의 transparent window를 일부 빌림**.

```text
긴 logic stage가 다음 phase의 일부 시간을 borrow  (전체 loop는 cycle time 안에 완료되어야 함)
```

intentional 설계일 수도, skew/delay variation을 흡수하는 opportunistic 효과일 수도.

## 14. Clock Skew

서로 다른 storage element에 clock이 도착하는 **시간 차**.

- **flip-flop**(hard edge): skew가 **useful computation time을 줄이고 hold risk를 키움**.
- **latch / pulsed latch**(soft edge): transparency window 덕분에 **일부 skew 흡수**.
- 대응: balanced clock tree(H-tree), clock mesh, useful skew scheduling.

## 15. Sequencing 방식 비교

| 방식 | 장점 | 단점 |
|---|---|---|
| **Flip-flop** | 설계 단순, timing 분석 명확 | time borrowing 제한, clock overhead |
| **2-phase latch** | 큰 time borrowing | clock phase 설계·race 관리 어려움 |
| **Pulsed latch** | 빠름, 일부 borrowing | hold time·pulse width에 민감 |

---

## 시험·복습 체크포인트

- Latch와 flip-flop의 transparent / edge-triggered 차이, master-slave FF의 각 latch가 언제 transparent한가?
- `t_pcq, t_ccq, t_setup, t_hold`를 정의할 수 있는가?
- Max-delay와 min-delay constraint 식을 쓰고, 각각 어떻게 위반을 고치는가? (hold는 왜 cycle time과 무관?)
- Time borrowing이 latch 기반 system에서 가능한 이유는?
- Clock skew가 setup·hold에 주는 영향이 flip-flop과 pulsed latch에서 다른 이유는?
{% endraw %}

---

이전: [08. Datapaths](08-datapaths-adders-shifters-multipliers.md)
