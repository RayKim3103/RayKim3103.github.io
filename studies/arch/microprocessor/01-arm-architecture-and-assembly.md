---
layout: page
title: "01. ARM — 프로세서 설계 · 아키텍처 · 어셈블리"
permalink: /studies/arch/microprocessor/01-arm-architecture-and-assembly/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · 강의 노트 `01(Ch1 Processor Design)` + `02(Ch2 ARM Architecture)` + `03(Ch3 ARM Assembly)` 통합·보강
- 교재: *ARM System-on-Chip Architecture* (Furber). 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

Ch1 — processor를 **instruction set / datapath / control / memory interface** 계층으로 이해, RISC, low-power.
Ch2 — programmer에게 보이는 **ARM 상태**(register, CPSR/SPSR)와 **exception 모델**.
Ch3 — ARM **assembly**: data processing, shifted register, load/store addressing, multiple transfer, stack, branch, **conditional execution**.

---

# Part 1. Processor Design Introduction

## 1. ARM & Embedded System

**ARM** = Advanced RISC Machine. low power + small area → embedded/SoC. **자체 chip 대량생산이 아니라 core·architecture를 license**(IP model).

Embedded system: 특정 목적, 비용·전력·크기 제약, sensor/actuator/I/O와 밀접, real-time constraint.
- **Hard real-time**: deadline miss = system failure. **Soft real-time**: deadline miss = 품질 저하.

## 2. Architecture vs Organization

| | 의미 |
|---|---|
| **Architecture** | programmer에게 보이는 instruction set, register, memory model |
| **Organization** | 이를 hardware로 구현하는 datapath, control, pipeline |

같은 architecture(ARM ISA)도 core마다 pipeline depth, cache, branch predictor가 다름.

## 3. Abstraction & MU0

```text
transistor → gate → register/ALU → datapath → processor → system
```
**MU0**: 개념 설명용 초단순 processor (PC, IR, accumulator, ALU, memory, control). fetch/decode/execute/memory access/branch라는 processor 기본 동작을 모두 보여줌.

- **Datapath**: data가 이동·연산되는 경로 (register, ALU, mux, bus, memory interface).
- **Control logic**: instruction decode → mux select, register write enable, memory R/W 제어 신호.

## 4. Instruction Set Design & RISC

고려: operand 개수(0/1/2/3-address), instruction type(data processing / data transfer / control flow), addressing mode, code density ↔ decode complexity.

| | CISC | RISC |
|---|---|---|
| instruction | 복잡·다양 | 단순·규칙적 |
| 실행 시간 | 명령마다 다름 | pipeline에 유리 |
| memory access | 다양한 명령에서 | **load-store 중심** |
| hardware | 복잡 | 단순 |

ARM은 RISC 철학 → 단순 instruction + load-store가 pipeline·low-power에 유리.

## 5. Pipeline & Low-Power

- pipeline hazard: **structural**(resource 충돌), **data**(이전 결과 미준비), **control**(branch로 PC 불확실). pipeline은 latency가 아니라 **throughput**을 높임.
- CMOS power = switching + short-circuit + leakage. low-power: VDD↓, clock frequency 조절, switching activity↓, capacitance↓, sleep mode / power management.

---

# Part 2. ARM Architecture

## 6. Architectural Inheritance

ARM은 Acorn RISC Machine 출발. RISC를 받아들이되 Berkeley RISC의 일부는 **비채택**: **register window 안 씀**, 모든 instruction의 strict single-cycle 안 고집 → embedded·low-power에 맞는 실용적 선택.

## 7. Visible Registers (16개)

| register | 역할 |
|---|---|
| `r0–r12` | general-purpose |
| `r13` | stack pointer |
| `r14` | link register (subroutine return address) |
| `r15` | **program counter** |

- 일부 processor mode에서 **banked register** → exception 처리 시 save/restore overhead↓.
- `r15`(PC): ARM state instruction은 32-bit, word aligned. **pipeline 때문에 읽히는 PC 값이 현재 instruction 주소와 다를 수 있음** (assembly에서 주의).

## 8. CPSR / SPSR

**CPSR** (Current Program Status Register): condition flag + control bit.

| flag | 의미 |
|---|---|
| `N` / `Z` / `C` / `V` | Negative / Zero / Carry / Overflow |

control bit: processor mode bits, interrupt disable bits, Thumb/ARM state bit.

**SPSR** (Saved PSR): exception mode에서 이전 CPSR 저장 → exception 복귀 시 상태 복원.

## 9. Memory & Modes

- memory = linear array of bytes. access size: byte / halfword / word.
- **byte ordering**: little endian(낮은 주소에 LSB) vs big endian(낮은 주소에 MSB).
- **Supervisor mode**: OS/privileged code 실행. user code가 privileged resource를 직접 못 건드리게 보호. exception 또는 supervisor call로 전환.

## 10. Load-Store & Instruction Categories

```text
memory 접근: LDR / STR
연산: register 안의 값끼리
```
| 범주 | 예 |
|---|---|
| Data processing | ADD, SUB, AND, ORR, MOV |
| Data transfer | LDR, STR |
| Control flow | B, BL |
| Software interrupt | SVC 계열 |

- **I/O**: memory-mapped I/O — device register가 memory address space에 배치되어 일반 load/store로 접근.

## 11. Exceptions

reset / undefined instruction / software interrupt / prefetch abort / data abort / **IRQ** / **FIQ**.
진입 시: **CPSR → SPSR 저장**, return address → link register, 해당 **exception vector로 이동**.

- development: cross development (host에서 compile/assemble/link/debug, target에서 실행). tools: ARM C compiler, assembler, linker, debugger, ARMulator.

---

# Part 3. ARM Assembly

## 12. Data Processing

```armasm
ADD r0, r1, r2      SUB r3, r4, r5     MOV r0, r1
AND r0, r1, r2      ORR r0, r1, r2    EOR r0, r1, r2
```
| 산술 | 의미 | | 논리/이동 | 의미 |
|---|---|---|---|---|
| `ADD` / `ADC` | 덧셈 / carry 포함 | | `AND` `ORR` `EOR` `BIC` | AND/OR/XOR/bit clear |
| `SUB` / `SBC` | 뺄셈 / borrow 포함 | | `MOV` / `MVN` | move / move NOT |
| `RSB` / `RSC` | reverse subtraction | | `CMP` / `TST` | 비교 / bit test (**flag만**, 결과 register 저장 X) |

- **`S` suffix** → 결과에 따라 CPSR condition code 갱신 (`ADDS r0, r1, r2`).

## 13. Operand

- **immediate**: `ADD r0, r0, #1` — encoding 제한으로 모든 32-bit 상수를 한 instruction으로 표현 못 함.
- **shifted register**: `ADD r0, r1, r2, LSL #2` → `r1 + (r2 << 2)`. 별도 shift instruction 없이 ×2ⁿ·주소 계산 효율화. shift: `LSL` / `LSR` / `ASR` / `ROR`.
- **multiply**: 일부 multiply는 결과 register가 첫 source register와 같으면 안 되는 제약 — instruction별 operand restriction 확인.

## 14. Load/Store Addressing

| mode | 예 | 동작 |
|---|---|---|
| register-indirect | `LDR r0, [r1]` | `[r1]` word → `r0` |
| base + offset | `LDR r0, [r1, #4]` | `[r1+4]` |
| post-indexed | `LDR r0, [r1], #4` | `[r1]` 읽고 나서 `r1 = r1+4` |
| byte access | `LDRB` / `STRB` | byte 단위 |

## 15. Multiple Register Transfer & Stack

```armasm
STMIA r9!, {r0, r1, r5}    ; 낮은 register 번호 → 낮은 memory address, !는 write-back
LDMIA r9!, {r0, r1, r5}
```

| stack 개념 | 의미 |
|---|---|
| Full stack | SP가 마지막 유효 data item을 가리킴 |
| Empty stack | SP가 빈 slot을 가리킴 |
| Ascending / Descending | push 시 address 증가 / 감소 |

일반적으로 **full descending** → `STMFD` / `LDMFD`:
```armasm
STMFD r13!, {r0-r2, r14}   ; prologue
LDMFD r13!, {r0-r2, pc}    ; epilogue (pc 복원 = return)
```

## 16. Branch & Conditional Execution

```armasm
B label            ; branch
BL subroutine      ; branch and link — return address → r14
CMP r0, #0
BEQ zero_case      ; CPSR flag 기반
```

**Conditional execution** — opcode 뒤 condition suffix로 branch 없이 짧은 if block:
```armasm
CMP r0, #0
ADDEQ r1, r1, #1   ; r0 == 0 이면
SUBNE r1, r1, #1   ; r0 != 0 이면
```
→ branch penalty·code size 절감.

**Jump table**:
```armasm
LDRLS pc, [r1, r0, LSL #2]   ; index r0로 SUBTAB[r0] 주소를 pc에 로드
SUBTAB  DCD SUB0
        DCD SUB1
        DCD SUB2
```
`DCD` = word constant를 배치하는 assembler directive. `ADR` = label 주소를 register에 적재.

## 복습 질문

- architecture와 organization의 차이, 그리고 같은 ARM ISA가 core마다 다른 부분은?
- CPSR의 flag·control bit, exception 진입 시 processor가 하는 세 가지(CPSR→SPSR, LR, vector)는?
- shifted register operand `ADD r0, r1, r2, LSL #2`가 계산하는 것과, 그 이점은?
- conditional execution(`ADDEQ` 등)이 branch 대비 이점은? full descending stack의 push/pop은?
{% endraw %}

---

다음: [02. Storage & I/O](02-storage-and-io.md)
