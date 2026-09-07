---
layout: page
title: "06. Assignment 1 RISC-V Assembly"
permalink: /studies/arch/computer-architecture/06-assignment-1-risc-v-assembly/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/06%20Assignment%201%20RISC-V%20Assembly.md)

{% raw %}
## 한눈에 보기
Kite RISC-V architecture simulator를 설치하고, RISC-V assembly program, register state, memory state를 입력으로 실행하는 첫 과제이다. ISA와 simulator 입출력 형식을 익히는 것이 목표다.

## 핵심 개념
- Kite simulator
- RISC-V assembly
- program_code
- reg_state
- mem_state
- PC
- instruction type
- x0
- memory alignment

## 체계적 정리
- Kite는 RISC-V instruction set의 five-stage pipeline을 모델링하는 architecture simulator다.
- 시뮬레이션 입력은 program code, register state, data memory state 세 파일이다.
- program code의 instruction은 PC=4부터 4-byte 간격으로 저장된다. 예를 들어 첫 instruction이 PC=4이면 다음은 PC=8이다.
- 지원 instruction은 R, I, S, SB, U, UJ, no type으로 분류된다.
- pseudo instruction과 ABI register name은 지원되지 않는다. `mv`, `not`, `sp`, `a0` 같은 이름을 그대로 쓰면 안 된다.
- register state는 x0부터 x31까지 32개 64-bit integer register의 초기값을 담는다.
- x0는 hard-wired zero이므로 non-zero 값을 넣어도 버려진다.
- memory state는 8-byte aligned address에 doubleword data를 저장한다.

## 중요한 수식과 관점
- RISC-V instruction size = 4 bytes.
- 다음 sequential PC = PC + 4.
- Data memory access는 이 과제 설정에서 8-byte alignment를 따라야 한다.

## 구현과 학습 포인트
과제의 핵심은 assembly instruction, register 초기값, memory 초기값이 simulator에서 어떻게 실행 결과로 이어지는지 추적하는 것이다. branch나 jump가 없으면 instruction은 위에서 아래로 순차 실행되고, branch/jump가 있으면 PC가 target으로 바뀐다.

## 자주 헷갈리는 지점
- register alias 대신 x-number를 써야 한다.
- memory address가 8의 배수가 아니면 doubleword alignment 규칙에 어긋난다.
- instruction은 대소문자를 구분하지 않지만 operand format은 simulator가 요구하는 형식을 맞춰야 한다.
- program 종료는 유효한 code segment를 벗어나거나 exit instruction을 만나는 방식으로 결정된다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
- [Assignment 2 Functions](07-assignment-2-functions.md)
- [The Processor](04-the-processor.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. Assignment 1 RISC-V Assembly**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.

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
- **06. Assignment 1 RISC-V Assembly**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. Memory Hierarchy](05-memory-hierarchy.md) · 다음: [07. Assignment 2 Functions](07-assignment-2-functions.md)
