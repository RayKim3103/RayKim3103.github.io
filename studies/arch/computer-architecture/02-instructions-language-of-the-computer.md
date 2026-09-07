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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. Instructions Language of the Computer**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.
- 성능은 instruction count, CPI, clock period의 곱으로 나누어 보아야 병목이 명확해진다.
- pipeline, cache, branch prediction은 평균 성능을 높이지만 hazard, miss, misprediction이라는 예외 비용을 만든다.

### 문제 풀이 또는 구현 루틴

- 명령어를 보면 operand 위치, control signal, datapath 경로, writeback 대상을 순서대로 추적한다.
- 성능 문제는 baseline cycle을 세고 stall/miss penalty를 더해 CPI로 환산한다.
- assembly 과제에서는 calling convention, stack frame, register 보존 규칙을 표로 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- clock frequency만 보고 성능을 판단하면 CPI와 instruction count 효과를 놓친다.
- pipeline은 개별 명령어 latency를 크게 줄이기보다 throughput을 높이는 기법이다.
- cache hit rate가 높아도 miss penalty가 크면 전체 실행 시간이 크게 늘 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제는 ISA 수준 설명인가, microarchitecture 구현인가?
- 병목이 계산, 메모리, branch, synchronization 중 어디에 있는가?
- 같은 프로그램을 더 빠르게 만들 때 compiler, ISA, hardware 중 어느 층을 바꿀 수 있는가?
- **02. Instructions Language of the Computer**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. Computer Abstractions and Technology](01-computer-abstractions-and-technology.md) · 다음: [03. Arithmetic for Computers](03-arithmetic-for-computers.md)
