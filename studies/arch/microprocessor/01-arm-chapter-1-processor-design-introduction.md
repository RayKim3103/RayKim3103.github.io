---
layout: page
title: "01. ARM Chapter 1 - Processor Design Introduction"
permalink: /studies/arch/microprocessor/01-arm-chapter-1-processor-design-introduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/01%20ARM%20Chapter%201%20-%20Processor%20Design%20Introduction.md)

{% raw %}
tags: #micro-processor #arm #embedded-system #processor-design #risc #low-power

관련 노트: [ARM Chapter 2 - ARM Architecture](02-arm-chapter-2-architecture.md)

## 핵심 요약

이 자료는 ARM과 embedded system을 소개하고, processor architecture와 organization, abstraction, MU0라는 단순 processor, instruction set design, processor design tradeoff, RISC, low-power design의 큰 흐름을 설명한다. 핵심은 processor를 instruction set, datapath, control logic, memory interface의 계층으로 나누어 이해하는 것이다.

## ARM 개요

ARM은 Advanced RISC Machine을 의미하며, low power와 small area를 강점으로 embedded system과 SoC에서 널리 쓰인다. ARM은 자체 chip을 대량 생산하기보다 processor core와 architecture를 license하는 방식으로 발전했다.

ARM이 많이 쓰이는 이유:

- RISC 기반의 단순하고 효율적인 instruction set
- 전력 소모가 낮아 mobile/embedded에 적합
- SoC 통합에 유리한 IP core 생태계
- Thumb 같은 compressed instruction format 지원
- 다양한 성능/전력 target에 맞춘 core family 제공

## Embedded System

Embedded system은 특정 목적을 수행하기 위해 제품 안에 내장된 computing system이다.

공통 특징:

- 특정 기능에 최적화된 single-function system
- 비용, 전력, 크기 제약이 큼
- 외부 sensor, actuator, I/O와 밀접하게 연결
- real-time constraint가 존재할 수 있음

Hard real-time system은 deadline을 놓치면 system failure로 이어진다. Soft real-time system은 deadline miss가 품질 저하를 만들지만 즉시 치명적 failure가 되지는 않는다.

## Processor Architecture와 Organization

| 구분 | 의미 |
|---|---|
| Architecture | programmer에게 보이는 instruction set, register, memory model |
| Organization | architecture를 실제 hardware로 구현하는 datapath, control, pipeline 구조 |

같은 architecture도 서로 다른 organization으로 구현할 수 있다. 예를 들어 같은 ARM instruction set을 쓰더라도 core마다 pipeline depth, cache, branch predictor, execution unit 구성이 다를 수 있다.

## Abstraction

Hardware design에서는 낮은 계층의 복잡도를 추상화해 높은 계층에서 설계한다.

```text
transistor -> gate -> register/ALU -> datapath -> processor -> system
```

NAND gate 하나도 transistor와 layout 관점에서는 복잡하지만, logic design에서는 하나의 Boolean operator로 다룬다. 이런 추상화가 큰 processor 설계를 가능하게 한다.

## MU0 Processor

MU0는 processor design 개념을 설명하기 위한 매우 단순한 processor이다.

구성 요소:

- Program counter
- Instruction register
- Accumulator
- ALU
- Memory
- Control logic

MU0 instruction set은 단순하지만, instruction fetch, decode, execute, memory access, branch라는 processor 기본 동작을 모두 보여준다.

## Datapath와 Control

Datapath는 data가 이동하고 연산되는 hardware 경로이다. Register, ALU, mux, bus, memory interface가 포함된다.

Control logic은 현재 instruction을 decode해 datapath의 mux select, register write enable, memory read/write 같은 제어 신호를 만든다.

```text
instruction -> control logic -> datapath control signals
```

## Instruction Set Design

Instruction set은 processor가 실행할 수 있는 명령의 집합이다.

고려 요소:

- operand 개수: 0-address, 1-address, 2-address, 3-address
- instruction type: data processing, data transfer, control flow
- addressing mode: immediate, register, base+offset, indirect
- code density와 decode complexity

Instruction set은 software 편의성과 hardware 단순성 사이의 tradeoff를 만든다.

## CISC와 RISC

| 구분 | CISC | RISC |
|---|---|---|
| Instruction | 복잡하고 다양한 명령 | 단순하고 규칙적인 명령 |
| 실행 시간 | 명령마다 다를 수 있음 | pipeline에 유리 |
| Memory access | 다양한 명령에서 가능 | load-store 중심 |
| Hardware | 복잡 | 상대적으로 단순 |

ARM은 RISC 철학에 기반한다. 단순한 instruction과 load-store 구조는 pipeline과 low-power 구현에 유리하다.

## Pipeline과 Hazard

Pipeline은 instruction 실행 단계를 겹쳐 throughput을 높이는 방법이다.

대표 hazard:

- Structural hazard: hardware resource 충돌
- Data hazard: 이전 instruction 결과가 아직 준비되지 않음
- Control hazard: branch로 다음 PC가 불확실함

Pipeline은 latency를 줄인다기보다 단위 시간당 instruction 처리량을 늘린다.

## Low-Power 설계

Embedded processor에서 low power는 핵심 요구사항이다.

CMOS power 구성:

- switching power
- short-circuit power
- leakage power

Low-power strategies:

- VDD 최소화
- clock frequency 조절
- switching activity 감소
- capacitance 감소
- sleep mode와 power management

## 시험ㆍ복습 체크포인트

- Architecture와 organization의 차이를 설명할 수 있어야 한다.
- Embedded system의 real-time constraint를 hard/soft로 구분할 수 있어야 한다.
- MU0를 datapath와 control logic 관점에서 설명할 수 있어야 한다.
- RISC의 핵심 특징과 ARM이 embedded에 적합한 이유를 말할 수 있어야 한다.
- Pipeline hazard 세 종류를 구분할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. ARM Chapter 1 - Processor Design Introduction**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **01. ARM Chapter 1 - Processor Design Introduction**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [02. ARM Chapter 2 - ARM Architecture](02-arm-chapter-2-architecture.md)
