---
layout: page
title: "02. Instructions Language of the Computer"
permalink: /studies/arch/computer-architecture/02-instructions-language-of-the-computer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/02%20Instructions%20Language%20of%20the%20Computer.md)

{% raw %}
## 한눈에 보기
컴퓨터가 실행하는 언어인 instruction과 RISC-V ISA를 다루는 핵심 강의이다. high-level C 코드가 register, memory, branch, function call, instruction encoding으로 어떻게 번역되는지 설명한다.

## 핵심 개념
- RISC-V
- ISA
- Von Neumann architecture
- CISC vs RISC
- registers
- load-store
- instruction format
- branch
- function call
- stack

## 체계적 정리
- Von Neumann architecture에서는 program과 data가 memory에 저장되고 processor가 instruction을 순차적으로 fetch/execute한다.
- CISC는 복잡한 instruction으로 code size를 줄이는 방향이고, RISC는 단순한 instruction을 조합해 hardware를 단순화하는 방향이다.
- RISC-V는 load-store architecture다. arithmetic instruction은 register 사이에서 동작하고, memory 접근은 load와 store로 분리된다.
- register x0는 항상 0이며, x10-x17은 function argument와 return value에 자주 쓰인다.
- R-type, I-type, S-type, SB-type, U-type, UJ-type format은 opcode, rd, rs1, rs2, funct, immediate field 배치가 다르다.
- if-else와 loop는 conditional branch로, switch는 branch chain 또는 branch table로, function call은 jal/jalr과 stack frame으로 구현된다.
- stack pointer x2는 stack top을 추적하고, frame pointer x8은 function frame 내부 local variable을 안정적으로 참조한다.

## 중요한 수식과 관점
- Branch target은 대개 PC-relative addressing으로 PC + immediate 기반으로 계산한다.
- Load/store effective address = base register + sign-extended immediate.
- Instruction encoding은 32-bit instruction word를 field별로 나눠 hardware control signal로 해석한다.

## 구현과 학습 포인트
이 강의는 assembly 과제와 functions 과제의 직접적인 이론 기반이다. C 코드를 RISC-V로 옮길 때는 변수의 register allocation, memory alignment, branch label, caller/callee-saved register 보존, stack push/pop 순서를 한 줄씩 추적해야 한다.

## 자주 헷갈리는 지점
- pseudo instruction과 실제 RISC-V instruction을 구분해야 한다.
- immediate field는 bit width가 제한되어 있어 큰 상수나 먼 branch는 lui, jalr 같은 조합이 필요할 수 있다.
- signed branch와 unsigned branch를 혼동하면 음수 비교가 틀린다.
- callee-saved register를 저장하지 않으면 재귀 함수에서 caller state가 깨진다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md)
- [Assignment 2 Functions](07-assignment-2-functions.md)
- [The Processor](04-the-processor.md)

{% endraw %}

---

이전: [01. Computer Abstractions and Technology](01-computer-abstractions-and-technology.md) · 다음: [03. Arithmetic for Computers](03-arithmetic-for-computers.md)
