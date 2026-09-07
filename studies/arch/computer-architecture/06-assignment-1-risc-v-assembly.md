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

{% endraw %}

---

이전: [05. Memory Hierarchy](05-memory-hierarchy.md) · 다음: [07. Assignment 2 Functions](07-assignment-2-functions.md)
