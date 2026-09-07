---
layout: page
title: "03. Arithmetic for Computers"
permalink: /studies/arch/computer-architecture/03-arithmetic-for-computers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/03%20Arithmetic%20for%20Computers.md)

{% raw %}
## 한눈에 보기
정수와 부동소수점 arithmetic이 hardware에서 어떻게 수행되는지 설명하는 자료이다. binary addition/subtraction, overflow 판정, adder 구조, floating-point representation과 연산 절차가 핵심이다.

## 핵심 개념
- binary addition
- subtraction
- overflow
- ALU
- ripple carry adder
- carry lookahead adder
- multiplication
- division
- IEEE 754
- floating point

## 체계적 정리
- 정수 덧셈은 bit별 sum과 carry propagation으로 수행된다.
- 뺄셈은 two's complement 표현에서 B의 보수를 더하는 방식으로 구현할 수 있다.
- overflow는 operand sign과 result sign의 관계로 판정한다. 같은 부호를 더했는데 결과 부호가 바뀌면 overflow다.
- ripple carry adder는 carry가 낮은 bit에서 높은 bit로 순차 전달되어 단순하지만 느리다.
- carry lookahead adder는 generate/propagate 신호로 carry를 병렬 계산해 지연을 줄인다.
- ALU는 add, sub, and, or, comparison, branch test 등 여러 연산을 control signal에 따라 수행한다.
- floating-point는 sign, exponent, fraction으로 실수를 근사 표현하며 IEEE 754 double precision은 1-bit sign, 11-bit exponent, 52-bit fraction을 사용한다.
- floating-point add/sub는 exponent alignment, significand 연산, normalization, rounding, exception handling 순서로 진행된다.

## 중요한 수식과 관점
- Two's complement subtraction: A - B = A + (~B + 1).
- Carry lookahead: carry는 generate와 propagate 조합으로 미리 계산한다.
- IEEE 754 normalized value: $(-1)^s x 1.fraction x 2^{exponent-bias}$.
- Double precision bias = 1023.

## 구현과 학습 포인트
floating-point 과제에서는 C++에서 double 값을 bit field로 분해해 sign, exponent, fraction을 직접 조작한다. hardware의 FP adder를 software로 흉내 내는 작업이므로 hidden bit, denormal, infinity, zero, rounding case를 명확히 나눠야 한다.

## 자주 헷갈리는 지점
- unsigned overflow와 signed overflow는 판정 기준이 다르다.
- floating-point 덧셈에서 exponent가 작은 수의 fraction을 shift할 때 sticky bit를 놓치면 rounding이 틀릴 수 있다.
- denormalized value에는 implicit leading 1이 없다.
- infinity, NaN, signed zero는 일반 normalized number와 다른 case로 처리해야 한다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Assignment 3 Floating-Point Numbers](08-assignment-3-floating-point-numbers.md)
- [The Processor](04-the-processor.md)
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. Arithmetic for Computers**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **03. Arithmetic for Computers**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. Instructions Language of the Computer](02-instructions-language-of-the-computer.md) · 다음: [04. The Processor](04-the-processor.md)
