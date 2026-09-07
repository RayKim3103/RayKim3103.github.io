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

{% endraw %}

---

이전: [03. Arithmetic for Computers](03-arithmetic-for-computers.md) · 다음: [05. Memory Hierarchy](05-memory-hierarchy.md)
