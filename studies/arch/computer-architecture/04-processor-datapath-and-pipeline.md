---
layout: page
title: "04. The Processor: Datapath & Pipeline"
permalink: /studies/arch/computer-architecture/04-processor-datapath-and-pipeline/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `04` + 과제 `09(Branch Prediction)` 통합·보강
- 교재: Patterson & Hennessy ch 4. **과제 세부(Kite)는 원문 그대로**입니다.

{% raw %}
## 개요

RISC-V instruction이 **datapath + control signal**로 어떻게 실행되는가. single-cycle → **5-stage pipeline** → hazard / forwarding / branch prediction.

## 1. Single-Cycle Datapath 구성요소

`PC`, instruction memory, **register file**(2 read + 1 write port), **ALU**, data memory, **control unit**, immediate generator, mux들.

| instruction | datapath 동작 |
|---|---|
| `ld`/`sd` | ALU로 effective address 계산 → data memory read/write |
| `add`/`sub`/`and`/`or` | register operand → ALU → destination register write |
| `beq` | ALU 비교 → PC = (taken) branch target / (not taken) PC+4 |

> control signal이 instruction type별로 정확히 설정되지 않으면 datapath가 맞아도 결과가 틀린다.

## 2. 5-Stage Pipeline

| stage | 하는 일 |
|---|---|
| **IF** | instruction fetch (PC → instruction memory) |
| **ID** | decode + register read + immediate 생성 |
| **EX** | ALU 연산 / branch 조건·target 계산 |
| **MEM** | data memory access |
| **WB** | register file write-back |

- **latency는 줄지 않고 throughput이 올라간다** (여러 instruction이 stage를 겹쳐 실행).
- ideal CPI → 1. hazard·miss가 stall cycle을 추가.

## 3. Hazard

### Structural hazard
같은 자원을 동시에 필요 → 자원 복제(별도 I-mem/D-mem)로 해결.

### Data hazard
뒤 instruction이 앞 instruction의 결과를 **쓰기 전에 읽으려** 할 때.
- **forwarding (bypassing)**: EX/MEM 또는 MEM/WB의 결과를 ALU 입력으로 앞당겨 전달 → 대부분의 data hazard를 stall 없이 해결.
- **load-use hazard**: load 결과는 MEM 끝에야 나오므로 바로 다음 instruction이 쓰면 forwarding으로도 **1 cycle stall(bubble)** 필요. compiler가 instruction 재배치로 완화.

### Control hazard
branch 방향·target을 알기 전에 fetch가 진행됨.
- **stall / flush**: branch 해소 전 fetch한 instruction을 무효화.
- **branch prediction**: 예측해서 진행, 틀리면 flush + 올바른 PC로.
- branch를 EX가 아니라 ID에서 해소하도록 앞당기면 penalty↓.

## 4. Branch Prediction

- **direction predictor** (taken/not-taken): 대표적으로 **2-bit saturating counter**.
  $$
  \text{taken → increment, not-taken → decrement, 0과 3에서 saturate}
  $$
  state 0,1 = predict not-taken / 2,3 = predict taken. 한 번 틀려도 방향이 바로 안 바뀜(hysteresis).
- **BTB (Branch Target Buffer)**: PC로 조회해 예측 target 주소 제공(taken일 때).
- **direction과 target은 서로 다른 예측 문제** — 방향이 맞아도 target이 틀리면 flush 필요.

$$
\text{Prediction accuracy} = \frac{\text{correct predictions}}{\text{total branch predictions}}
$$
$$
\text{beq target: condition true → PC + (imm} \ll 1),\quad \text{false → PC + 4}
$$

---

## 과제 A4 — Kite에 Branch Predictor + BTB 구현

- **fetch stage**: instruction memory에서 PC의 instruction을 읽음. **SB type**이면 branch predictor가 PC로 taken/not-taken 예측.
  - taken 예측 → BTB에서 predicted target을 읽어 PC를 그 주소로. 아니면 PC+4.
- **execute stage**: ALU가 실제 branch condition·target 계산.
- **writeback stage**: 실제 branch outcome으로 predictor **update**. taken이면 BTB도 실제 target으로 update.
- predicted target ≠ actual target → **pipeline register, ALU, register dependency map을 flush**하고 PC를 correct target으로.
- 구현 = `br_predictor`와 `br_target_buffer`의 lookup/update 로직 완성.
- correctness: 잘못된 path instruction이 architectural state를 바꾸기 **전에** flush되는지, predictor state가 **실제** 결과로 update되는지.

## 자주 틀리는 지점

- pipeline은 latency가 아니라 **throughput**을 높인다.
- forwarding으로 해결 안 되는 **load-use hazard**는 stall 필요.
- **counter update를 fetch 시점에 하면** speculative path 때문에 predictor가 오염 → outcome 확정 후(writeback) update.
- flush할 때 IF/ID, ID/EX, EX/MEM, MEM/WB **모든 pipeline register + dependency map**을 함께 정리.
- BTB miss에서 잘못된 target을 쓰면 PC 흐름이 깨짐.

## 복습 질문

- 5-stage의 각 stage가 하는 일과, pipeline이 CPI/latency/throughput에 주는 영향은?
- data hazard를 forwarding으로 해결하는 원리와, load-use hazard가 여전히 stall이 필요한 이유는?
- 2-bit saturating counter의 상태 전이와, direction/target 예측이 별개 문제인 이유는?
- branch 오예측 시 flush해야 하는 대상은?
{% endraw %}

---

이전: [03. Computer Arithmetic](03-computer-arithmetic.md) · 다음: [05. Memory Hierarchy & Caches](05-memory-hierarchy-and-caches.md)
