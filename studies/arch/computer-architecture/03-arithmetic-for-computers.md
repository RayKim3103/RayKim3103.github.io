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

{% endraw %}

---

이전: [02. Instructions Language of the Computer](02-instructions-language-of-the-computer.md) · 다음: [04. The Processor](04-the-processor.md)
