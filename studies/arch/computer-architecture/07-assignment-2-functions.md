---
layout: page
title: "07. Assignment 2 Functions"
permalink: /studies/arch/computer-architecture/07-assignment-2-functions/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/07%20Assignment%202%20Functions.md)

{% raw %}
## 한눈에 보기
RISC-V assembly로 Tower of Hanoi 재귀 함수를 작성하는 과제이다. 함수 인자, return address, stack frame, saved register 보존을 직접 관리해야 하므로 calling convention 이해가 핵심이다.

## 핵심 개념
- recursive function
- Tower of Hanoi
- RISC-V calling convention
- stack pointer
- frame pointer
- return address
- callee-saved registers
- saved registers

## 체계적 정리
- global variable `num_moves`는 x9 register로 표현된다.
- A, B, C 배열의 base address는 각각 x18, x19, x20에 저장된다.
- x1은 return address, x2는 stack pointer, x8은 frame pointer 역할을 한다.
- x10-x13은 TowerOfHanoi 함수의 입력 인자를 전달한다.
- x18-x27 같은 saved register는 callee가 보존해야 한다.
- Tower of Hanoi는 n개 원반을 source에서 destination으로 옮기되 auxiliary peg를 사용한다.
- 재귀 호출마다 현재 인자와 return address, 필요한 saved register를 stack에 저장하고, base case에서 돌아오며 복원해야 한다.
- 채점은 최종 register state와 memory state가 요구 출력과 일치하는지를 본다.

## 중요한 수식과 관점
- Tower of Hanoi 이동 횟수: $2^n - 1$.
- Stack push는 보통 sp 감소 후 store, pop은 load 후 sp 증가로 구현한다.
- Function call: `jal`이 x1에 return address를 저장하고 target label로 jump한다.

## 구현과 학습 포인트
구현할 때는 C의 `TowerOfHanoi(n, A, C, B)` 호출을 assembly 수준으로 풀어야 한다. 첫 번째 재귀 호출, 실제 disk 이동, 두 번째 재귀 호출 사이에서 x10-x13 인자가 계속 바뀌므로, 원래 값을 stack 또는 saved register에 보관해야 한다.

## 자주 헷갈리는 지점
- 재귀 함수에서 x1을 저장하지 않으면 nested call 뒤 return address가 사라진다.
- x18-x27을 보존하지 않으면 채점 기준 register state가 틀어진다.
- stack pointer를 함수 진입/탈출에서 대칭적으로 복구해야 한다.
- base case와 recursive case branch condition이 바뀌면 이동 순서와 num_moves가 모두 틀린다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)
- [Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md)
- [Arithmetic for Computers](03-arithmetic-for-computers.md)

{% endraw %}

---

이전: [06. Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md) · 다음: [08. Assignment 3 Floating-Point Numbers](08-assignment-3-floating-point-numbers.md)
