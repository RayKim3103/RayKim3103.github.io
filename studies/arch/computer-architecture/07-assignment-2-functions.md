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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. Assignment 2 Functions**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
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
- **07. Assignment 2 Functions**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md) · 다음: [08. Assignment 3 Floating-Point Numbers](08-assignment-3-floating-point-numbers.md)
