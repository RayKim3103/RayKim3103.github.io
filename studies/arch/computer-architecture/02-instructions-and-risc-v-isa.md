---
layout: page
title: "02. Instructions & RISC-V ISA"
permalink: /studies/arch/computer-architecture/02-instructions-and-risc-v-isa/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `02` + 과제 `06(RISC-V Assembly)` + `07(Functions)` 통합·보강
- 교재: Patterson & Hennessy ch 2. **과제 세부(Kite 설정)는 원문 그대로**입니다.

{% raw %}
## 개요

컴퓨터의 언어 = **instruction**. C 코드가 register·memory·branch·function call·instruction encoding으로 어떻게 번역되는지. ISA는 **RISC-V**(RV64I).

## 1. 배경

- **Von Neumann architecture**: program과 data가 같은 memory에 저장되고 processor가 instruction을 순차 fetch/execute.
- **CISC vs RISC**: CISC(x86)는 복잡한 instruction으로 code size↓; RISC(RISC-V, ARM)는 단순 instruction 조합으로 hardware 단순화·pipeline 용이.
- **load-store architecture**: arithmetic은 **register 사이**에서만, memory 접근은 `ld`/`sd`로 분리.

## 2. Register (RV64I, 64-bit ×32)

| register | 용도 |
|---|---|
| `x0` | hard-wired **zero** (쓰기 무시) |
| `x1` (ra) | return address |
| `x2` (sp) | stack pointer |
| `x8` (fp/s0) | frame pointer |
| `x10–x17` (a0–a7) | function argument / return value |
| `x18–x27` (s2–s11) | saved registers (**callee-saved**) |
| `x5–x7, x28–x31` (t0–t6) | temporaries (**caller-saved**) |

## 3. Instruction Formats (32-bit word)

| format | 필드 | 예 |
|---|---|---|
| **R** | funct7 · rs2 · rs1 · funct3 · rd · opcode | `add`, `sub`, `and`, `or`, `slt` |
| **I** | imm[11:0] · rs1 · funct3 · rd · opcode | `addi`, `ld`, `jalr` |
| **S** | imm[11:5] · rs2 · rs1 · funct3 · imm[4:0] · opcode | `sd` |
| **SB** | imm(재배치) · rs2 · rs1 · funct3 · imm · opcode | `beq`, `bne`, `blt` |
| **U** | imm[31:12] · rd · opcode | `lui`, `auipc` |
| **UJ** | imm(재배치) · rd · opcode | `jal` |

- **effective address** (load/store): `base register + sign-extended immediate`.
- **branch target**: `PC + (sign-extended immediate << 1)` (PC-relative).
- immediate는 bit width 제한 → 큰 상수/먼 branch는 `lui`+`addi`, `auipc`+`jalr` 조합.

## 4. 제어 흐름

- **if-else / loop** → conditional branch (`beq`, `bne`, `blt`, `bge` …). signed vs unsigned branch 구분(`blt` vs `bltu`).
- **switch** → branch chain 또는 **branch table**(jump table).
- **function call** → `jal rd, label` (rd=x1에 PC+4 저장 후 jump), 복귀는 `jalr x0, 0(x1)`.

## 5. Calling Convention & Stack

- **stack**: 높은 주소 → 낮은 주소로 성장. push = `sp -= 8; sd reg, 0(sp)`, pop = `ld reg, 0(sp); sp += 8`.
- **caller-saved (t*, a*)**: 호출 전에 필요하면 caller가 저장.
- **callee-saved (s*, ra, fp)**: 함수가 쓰면 진입 시 저장, 탈출 시 복원.
- **frame pointer x8**: 함수 frame 내부 local variable을 sp 변동과 무관하게 참조.

---

## 과제 A1 — RISC-V Assembly (Kite)

- **Kite** = RISC-V 5-stage pipeline architecture simulator.
- 입력 3파일: **program code**, **register state**, **data memory state**.
- program code: instruction은 **PC=4부터 4-byte 간격**으로 저장 (`RISC-V instruction size = 4 bytes`, 다음 sequential PC = PC+4).
- 지원 type: R, I, S, SB, U, UJ, no-type.
- **pseudo instruction·ABI 이름 미지원** — `mv`, `not`, `sp`, `a0` 대신 `x`-number를 써야 함.
- register state: x0–x31 32개 64-bit integer register 초기값. `x0`는 non-zero를 넣어도 버려짐.
- memory state: **8-byte aligned** address에 doubleword 저장.
- branch/jump가 없으면 위→아래 순차 실행, 있으면 PC가 target으로.

## 과제 A2 — Functions: Tower of Hanoi (재귀)

- 이동 횟수 $$2^n - 1$$.
- Kite 설정: global `num_moves` → `x9`; A/B/C 배열 base address → `x18`, `x19`, `x20`; `x1`=return address, `x2`=stack pointer, `x8`=frame pointer; `x10–x13`=`TowerOfHanoi` 인자.
- `x18–x27` 등 **saved register는 callee가 보존**.
- 재귀 호출마다: 현재 인자 + return address + 필요한 saved register를 **stack에 저장** → base case에서 복귀하며 복원.
- `TowerOfHanoi(n, A, C, B)` 를 assembly로: 첫 재귀 호출 / 실제 disk 이동 / 두 번째 재귀 호출 사이에서 `x10–x13`이 계속 바뀌므로 원래 값을 stack/saved register에 보관.
- 채점: 최종 register state + memory state가 요구 출력과 일치하는지.

## 자주 틀리는 지점

- pseudo instruction ↔ 실제 instruction, register alias ↔ x-number 혼동.
- immediate bit width 초과 → `lui`/`jalr` 조합 필요.
- signed ↔ unsigned branch 혼동 → 음수 비교 오류.
- **재귀에서 `x1`(ra)를 저장 안 하면** nested call 뒤 return address 소실.
- `x18–x27`을 보존 안 하면 채점 register state가 틀어짐; sp를 진입/탈출에서 대칭 복구.
- memory address가 8의 배수가 아니면 doubleword alignment 위반.

## 복습 질문

- load-store architecture와 6가지 instruction format의 필드 차이는?
- branch target과 load/store effective address는 각각 어떻게 계산되는가?
- caller-saved와 callee-saved register의 차이, 재귀에서 무엇을 stack에 저장해야 하는가?
- Tower of Hanoi 이동 횟수와, `TowerOfHanoi(n,A,C,B)` 재귀 구조는?
{% endraw %}

---

이전: [01. Computer Abstractions & Performance](01-computer-abstractions-and-performance.md) · 다음: [03. Computer Arithmetic](03-computer-arithmetic.md)
