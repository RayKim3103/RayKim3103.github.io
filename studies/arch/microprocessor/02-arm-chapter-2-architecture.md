---
layout: page
title: "02. ARM Chapter 2 - ARM Architecture"
permalink: /studies/arch/microprocessor/02-arm-chapter-2-architecture/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/02%20ARM%20Chapter%202%20-%20ARM%20Architecture.md)

{% raw %}
tags: #micro-processor #arm #architecture #programmers-model #cpsr #exception #load-store

관련 노트: [ARM Chapter 1 - Processor Design Introduction](01-arm-chapter-1-processor-design-introduction.md), [ARM Chapter 3 - ARM Assembly Language Programming](03-arm-chapter-3-assembly-language-programming.md)

## 핵심 요약

이 자료는 ARM architecture의 역사, ARM이 Berkeley RISC와 다른 선택을 한 이유, ARM programmer's model, visible registers, CPSR/SPSR, memory system, load-store architecture, exceptions, development tools를 설명한다. 핵심은 programmer에게 보이는 ARM 상태와 exception 처리 모델을 이해하는 것이다.

## ARM의 역사와 강점

ARM은 Acorn RISC Machine에서 출발했다. 초기 RISC의 단순한 instruction과 효율적 hardware 구현을 바탕으로, 낮은 전력과 작은 면적을 강점으로 embedded market에서 성장했다.

ARM의 강점:

- simple RISC instruction set
- code density를 위한 Thumb 지원
- low power core 설계
- 다양한 market segment에 맞춘 core family
- SoC에 통합하기 쉬운 IP model

## Architectural Inheritance

ARM은 RISC 철학을 받아들였지만 Berkeley RISC의 일부 기능은 채택하지 않았다.

예:

- register window를 채택하지 않음
- 모든 instruction의 strict single-cycle 실행을 고집하지 않음
- embedded와 low power에 맞게 실용적 선택을 함

## ARM Programmer's Model

Programmer's model은 assembly programmer나 compiler가 볼 수 있는 processor 상태이다.

포함 요소:

- general-purpose registers
- program counter
- current program status register
- processor modes
- memory organization
- exception model

## Visible Registers

ARM은 16개의 주요 register를 programmer에게 보인다.

| Register | 역할 |
|---|---|
| `r0-r12` | general-purpose registers |
| `r13` | stack pointer로 자주 사용 |
| `r14` | link register, subroutine return address |
| `r15` | program counter |

일부 processor mode에서는 banked register를 사용해 exception 처리 시 register save/restore overhead를 줄인다.

## Program Counter

`r15`는 program counter이다. ARM state에서 instruction은 32-bit이고 word aligned이다. Pipeline 때문에 읽히는 PC 값이 현재 instruction 주소와 정확히 같지 않을 수 있으므로 assembly programming에서 주의해야 한다.

## CPSR

CPSR(Current Program Status Register)는 condition flag와 processor control bit를 저장한다.

주요 flag:

| Flag | 의미 |
|---|---|
| `N` | Negative |
| `Z` | Zero |
| `C` | Carry |
| `V` | Overflow |

주요 control bit:

- processor mode bits
- interrupt disable bits
- Thumb/ARM state bit

## SPSR

SPSR(Saved Program Status Register)는 exception mode에서 이전 CPSR 값을 저장한다. Exception에서 돌아올 때 원래 상태를 복원하는 데 사용된다.

## Memory System

ARM memory는 linear array of bytes로 본다. Data size는 byte, halfword, word 등으로 접근할 수 있다.

Byte ordering:

- Little endian: 낮은 주소에 least significant byte 저장
- Big endian: 낮은 주소에 most significant byte 저장

## Supervisor Mode

Supervisor mode는 OS나 privileged code가 실행되는 mode이다. User code가 직접 privileged resource를 조작하지 못하게 보호한다.

Exception이나 supervisor call을 통해 user mode에서 supervisor mode로 전환할 수 있다.

## Load-Store Architecture

ARM은 load-store architecture이다.

```text
memory 접근: LDR/STR
연산: register 안의 값끼리 수행
```

산술/논리 instruction은 memory operand를 직접 사용하지 않고 register operand를 사용한다. 이 구조는 datapath와 pipeline을 단순하게 만든다.

## ARM Instruction Categories

| 범주 | 예 |
|---|---|
| Data processing | ADD, SUB, AND, ORR, MOV |
| Data transfer | LDR, STR |
| Control flow | B, BL |
| Software interrupt | SVC 계열 |

## I/O System

I/O는 processor가 device register를 읽고 쓰는 방식으로 처리된다. Memory-mapped I/O에서는 device register가 memory address space에 배치되어 일반 load/store instruction으로 접근된다.

## Exceptions

Exception은 정상 program flow를 깨고 exception handler로 이동하는 사건이다.

예:

- reset
- undefined instruction
- software interrupt
- prefetch abort
- data abort
- IRQ
- FIQ

Exception 진입 시 processor는 CPSR을 SPSR에 저장하고, return address를 link register에 저장하며, 해당 exception vector로 이동한다.

## Development Tools

ARM 개발 환경은 cross development가 일반적이다. Host PC에서 compile, assemble, link, debug를 수행하고 target board에서 실행한다.

도구:

- ARM C compiler
- ARM assembler
- linker
- debugger
- ARMulator 같은 simulator

## 시험ㆍ복습 체크포인트

- ARM visible register `r13`, `r14`, `r15`의 역할을 설명할 수 있어야 한다.
- CPSR의 condition flag와 mode bit를 구분할 수 있어야 한다.
- SPSR이 exception return에 필요한 이유를 말할 수 있어야 한다.
- Load-store architecture의 장점을 설명할 수 있어야 한다.
- ARM exception 진입/복귀의 큰 흐름을 이해해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. ARM Chapter 2 - ARM Architecture**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 저장장치 주제에서는 logical 주소와 physical 위치가 언제 어떻게 mapping되는지 추적한다.
- FTL/파일시스템 계층은 성능뿐 아니라 crash consistency와 metadata 복구 경로가 중요하다.
- processor 관점에서는 register, instruction encoding, addressing mode, exception 흐름을 함께 보아야 한다.
- SSD 관점에서는 NAND flash의 물리 제약이 FTL, mapping table, wear leveling, garbage collection 설계를 만든다.
- hardware와 software 경계에서 latency hiding, buffering, metadata consistency가 핵심 설계 문제가 된다.

### 문제 풀이 또는 구현 루틴

- assembly를 읽을 때는 각 instruction이 register와 memory 중 무엇을 바꾸는지 한 줄씩 주석으로 적는다.
- I/O 구조는 request path, queue, interrupt, DMA, completion 순서로 추적한다.
- FTL 함수는 logical page, physical page, block, superblock metadata가 언제 갱신되는지 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- NAND flash는 overwrite가 불가능하므로 HDD처럼 제자리 갱신한다고 생각하면 안 된다.
- 성능 문제에서 CPU 계산보다 I/O latency와 queueing이 병목일 수 있다.
- metadata update 순서를 잘못 잡으면 전원 장애 상황에서 mapping consistency가 깨진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 동작은 processor core 내부 문제인가, memory/I/O subsystem 문제인가?
- 상태가 바뀌는 metadata는 어디에 있고 crash 후 어떻게 복구되는가?
- latency를 줄이는 방법과 throughput을 높이는 방법이 어떻게 다른가?
- **02. ARM Chapter 2 - ARM Architecture**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. ARM Chapter 1 - Processor Design Introduction](01-arm-chapter-1-processor-design-introduction.md) · 다음: [03. ARM Chapter 3 - ARM Assembly Language Programming](03-arm-chapter-3-assembly-language-programming.md)
