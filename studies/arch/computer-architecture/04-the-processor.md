---
layout: page
title: "04. The Processor"
permalink: /studies/arch/computer-architecture/04-the-processor/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/04%20The%20Processor.md)

{% raw %}
## 한눈에 보기
RISC-V instruction이 processor 내부 datapath와 control signal을 통해 어떻게 실행되는지 설명하는 자료이다. single-cycle 구현에서 출발해 pipeline, hazard, forwarding, branch 처리로 확장된다.

## 핵심 개념
- datapath
- control
- RISC-V processor
- single-cycle
- pipeline
- IF ID EX MEM WB
- data hazard
- control hazard
- forwarding
- stall
- branch prediction

## 체계적 정리
- 기본 RISC-V processor는 PC, instruction memory, register file, ALU, data memory, control, immediate generator, mux로 구성된다.
- ld/sd는 ALU로 address를 계산하고 data memory를 읽거나 쓴다.
- add/sub/and/or 같은 arithmetic-logical instruction은 register operand를 ALU에 넣고 결과를 destination register에 쓴다.
- beq 같은 branch instruction은 ALU 비교 결과에 따라 PC를 PC+4 또는 branch target으로 바꾼다.
- pipeline은 instruction 실행을 IF, ID, EX, MEM, WB stage로 나누어 throughput을 높인다.
- data hazard는 뒤 instruction이 앞 instruction의 결과를 아직 쓰기 전에 읽으려 할 때 발생한다.
- forwarding은 EX/MEM 또는 MEM/WB stage의 결과를 앞당겨 사용해 stall을 줄인다.
- control hazard는 branch 방향과 target을 알기 전 fetch가 진행되면서 생기며, stall, flush, branch prediction으로 완화한다.

## 중요한 수식과 관점
- Ideal pipeline CPI는 1에 가까워지지만 hazard와 miss가 stall cycle을 추가한다.
- Branch target = PC + shifted immediate 형태로 계산된다.
- Processor performance는 instruction count, CPI, clock rate의 곱으로 다시 연결된다.

## 구현과 학습 포인트
Kite simulator 과제의 핵심 배경이다. branch prediction 과제에서는 fetch stage에서 예측을 사용하고, execute/writeback stage에서 실제 branch outcome과 target을 확인해 predictor와 BTB를 update하며 잘못 가져온 instruction을 flush한다.

## 자주 헷갈리는 지점
- pipeline은 latency를 줄인다기보다 throughput을 높이는 기법이다.
- forwarding으로 해결할 수 없는 load-use hazard는 stall이 필요할 수 있다.
- branch가 틀리면 잘못된 path의 instruction과 dependency map을 함께 정리해야 한다.
- control signal이 instruction type별로 정확히 설정되지 않으면 datapath가 맞아도 결과가 틀린다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
- [Memory Hierarchy](05-memory-hierarchy.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. The Processor**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
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
- **04. The Processor**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. Arithmetic for Computers](03-arithmetic-for-computers.md) · 다음: [05. Memory Hierarchy](05-memory-hierarchy.md)
