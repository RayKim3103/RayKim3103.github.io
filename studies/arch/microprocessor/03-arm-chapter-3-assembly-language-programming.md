---
layout: page
title: "03. ARM Chapter 3 - ARM Assembly Language Programming"
permalink: /studies/arch/microprocessor/03-arm-chapter-3-assembly-language-programming/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/03%20ARM%20Chapter%203%20-%20ARM%20Assembly%20Language%20Programming.md)

{% raw %}
tags: #micro-processor #arm #assembly #ldr #str #branch #stack #conditional-execution

관련 노트: [ARM Chapter 2 - ARM Architecture](02-arm-chapter-2-architecture.md), [Computer Organization Chapter 6 - Storage and I/O Topics](04-computer-organization-chapter-6-storage-and-io-topics.md)

## 핵심 요약

이 자료는 ARM assembly programming의 기본 instruction을 설명한다. Data processing, data transfer, control flow instruction을 중심으로 register operand, immediate, shifted register operand, load/store addressing, multiple register transfer, stack, branch, conditional execution, subroutine call을 다룬다.

## Assembly Programming 관점

Assembly에서는 개별 machine instruction 수준에서 생각해야 한다. ARM instruction은 기본적으로 32-bit이며, register와 condition code, addressing mode를 직접 다룬다.

## Data Processing Instructions

Data processing instruction은 register 안의 값을 연산하고 결과를 register에 저장한다.

예:

```armasm
ADD r0, r1, r2
SUB r3, r4, r5
MOV r0, r1
AND r0, r1, r2
ORR r0, r1, r2
EOR r0, r1, r2
```

결과는 32-bit register에 저장된다.

## Arithmetic Instructions

| 명령 | 의미 |
|---|---|
| `ADD` | 덧셈 |
| `ADC` | carry 포함 덧셈 |
| `SUB` | 뺄셈 |
| `SBC` | carry/borrow 포함 뺄셈 |
| `RSB` | reverse subtraction |
| `RSC` | reverse subtraction with carry |

`S` suffix를 붙이면 연산 결과에 따라 CPSR condition code가 갱신된다.

```armasm
ADDS r0, r1, r2
```

## Logical and Move Instructions

| 명령 | 의미 |
|---|---|
| `AND` | bitwise AND |
| `ORR` | bitwise OR |
| `EOR` | bitwise XOR |
| `BIC` | bit clear |
| `MOV` | move |
| `MVN` | move NOT |
| `CMP` | 비교, condition code만 설정 |
| `TST` | bit test |

`CMP`는 내부적으로 subtraction을 수행하지만 결과 register를 저장하지 않고 flag만 바꾼다.

## Immediate Operand

Immediate operand는 instruction 안에 직접 들어가는 상수이다.

```armasm
ADD r0, r0, #1
MOV r1, #10
```

ARM immediate는 encoding 제한이 있으므로 모든 32-bit 상수를 한 instruction으로 표현할 수 있는 것은 아니다.

## Shifted Register Operand

ARM data processing instruction은 두 번째 register operand에 shift를 결합할 수 있다.

```armasm
ADD r0, r1, r2, LSL #2
```

이 명령은 `r1 + (r2 << 2)`를 계산한다. 별도 shift instruction 없이 multiply by power of two와 address 계산을 효율적으로 수행할 수 있다.

Shift 종류:

- `LSL`: logical shift left
- `LSR`: logical shift right
- `ASR`: arithmetic shift right
- `ROR`: rotate right

## Multiply

ARM은 multiplication instruction을 제공한다. 일부 multiply instruction에서는 결과 register가 첫 번째 source register와 같으면 안 되는 제약이 있을 수 있다. Assembly 작성 시 instruction별 operand restriction을 확인해야 한다.

## Data Transfer Instructions

ARM은 load-store 구조이므로 memory 접근은 `LDR`, `STR` 계열이 담당한다.

```armasm
LDR r0, [r1]
STR r0, [r2]
```

여기서 `r1`, `r2`는 base register로 memory address를 담는다.

## Addressing Modes

### Register-Indirect

```armasm
LDR r0, [r1]
```

`r1`이 가리키는 주소에서 word를 읽어 `r0`에 저장한다.

### Base Plus Offset

```armasm
LDR r0, [r1, #4]
```

`r1 + 4` 주소에서 읽는다.

### Post-Indexed

```armasm
LDR r0, [r1], #4
```

먼저 `[r1]`에서 읽고, 이후 `r1 = r1 + 4`로 갱신한다.

### Byte Access

```armasm
LDRB r0, [r1]
STRB r0, [r2]
```

Byte 단위 접근에 사용한다.

## Multiple Register Transfer

여러 register를 한 번에 memory로 저장하거나 읽을 수 있다.

```armasm
STMIA r9!, {r0, r1, r5}
LDMIA r9!, {r0, r1, r5}
```

낮은 register 번호가 낮은 memory address에 대응된다. `!`는 base register write-back을 의미한다.

## Stack Addressing

ARM stack은 ascending/descending, full/empty 개념으로 설명된다.

| 개념 | 의미 |
|---|---|
| Full stack | SP가 마지막 유효 data item을 가리킴 |
| Empty stack | SP가 비어 있는 slot을 가리킴 |
| Ascending | push 시 address 증가 |
| Descending | push 시 address 감소 |

일반적으로 full descending stack을 많이 사용하며, `STMFD`, `LDMFD` pseudo instruction으로 표현한다.

```armasm
STMFD r13!, {r0-r2, r14}
LDMFD r13!, {r0-r2, pc}
```

## Branch와 Conditional Execution

### Branch

```armasm
B label
BL subroutine
```

`BL`은 branch and link로, return address를 link register `r14`에 저장한다.

### Conditional Branch

```armasm
CMP r0, #0
BEQ zero_case
BNE nonzero_case
```

Condition code는 CPSR flag를 기반으로 판단된다.

### Conditional Execution

ARM은 opcode 뒤에 condition suffix를 붙여 짧은 if block을 branch 없이 실행할 수 있다.

```armasm
CMP r0, #0
ADDEQ r1, r1, #1
SUBNE r1, r1, #1
```

짧은 조건부 sequence에서는 branch penalty를 줄이고 code size를 줄일 수 있다.

## Jump Table

여러 subroutine 중 하나를 index로 호출하려면 jump table을 사용할 수 있다.

```armasm
LDRLS pc, [r1, r0, LSL #2]
SUBTAB
    DCD SUB0
    DCD SUB1
    DCD SUB2
```

`DCD`는 word constant를 배치하는 assembler directive이다.

## Hello World 예제

자료는 primitive version과 block copy version의 Hello World program을 통해 문자열을 memory에서 복사하고 byte 단위로 출력하는 흐름을 보여준다.

핵심 instruction:

- `ADR`: label 주소를 register에 적재
- `LDR`: word load
- `STR`: word store
- `LDRB`: byte load
- `CMP`: 종료 문자 검사
- conditional branch: loop 제어

## 시험ㆍ복습 체크포인트

- `LDR`, `STR`, `LDRB`, `STRB`의 차이를 설명할 수 있어야 한다.
- Pre/post-indexed addressing과 write-back의 의미를 이해해야 한다.
- `S` suffix와 `CMP`가 CPSR flag에 미치는 영향을 말할 수 있어야 한다.
- `BL`, `r14`, `pc`를 이용한 subroutine call/return을 설명할 수 있어야 한다.
- `STMFD`/`LDMFD`를 stack save/restore와 연결할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. ARM Chapter 3 - ARM Assembly Language Programming**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- 저장장치 주제에서는 logical 주소와 physical 위치가 언제 어떻게 mapping되는지 추적한다.
- FTL/파일시스템 계층은 성능뿐 아니라 crash consistency와 metadata 복구 경로가 중요하다.
- processor 관점에서는 register, instruction encoding, addressing mode, exception 흐름을 함께 보아야 한다.

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
- **03. ARM Chapter 3 - ARM Assembly Language Programming**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. ARM Chapter 2 - ARM Architecture](02-arm-chapter-2-architecture.md) · 다음: [04. Computer Organization Chapter 6 - Storage and I/O Topics](04-computer-organization-chapter-6-storage-and-io-topics.md)
