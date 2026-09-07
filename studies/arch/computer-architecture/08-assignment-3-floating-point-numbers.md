---
layout: page
title: "08. Assignment 3 Floating-Point Numbers"
permalink: /studies/arch/computer-architecture/08-assignment-3-floating-point-numbers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/08%20Assignment%203%20Floating-Point%20Numbers.md)

{% raw %}
## 한눈에 보기
double-precision floating-point add/subtract operator를 직접 구현하는 과제이다. C++의 `float64_t` class에서 IEEE 754 bit layout을 분해하고, hardware floating-point unit이 수행하는 절차를 software로 재현한다.

## 핵심 개념
- IEEE 754
- double precision
- sign
- exponent
- fraction
- denormalized number
- infinity
- NaN
- floating add
- floating subtract

## 체계적 정리
- IEEE 754 double precision은 8 bytes이며 sign 1 bit, exponent 11 bits, fraction 52 bits로 구성된다.
- normalized number는 hidden leading 1을 가진 significand로 해석한다.
- denormalized number는 exponent가 0이고 hidden leading 1이 없다.
- operator+와 operator- skeleton은 dummy value를 반환하므로 실제 sign/exponent/fraction 계산을 채워야 한다.
- 테스트는 일반 수, denormalized value, plus/minus infinity 등 edge case를 포함한다.
- 과제는 Makefile, float.h, float.cc, main.cc 구조로 이루어지고, 실질 구현은 float.cc의 operator 함수에 집중된다.

## 중요한 수식과 관점
- Normalized double: $(-1)^s x 1.f x 2^{e-1023}$.
- Exponent alignment 후 작은 exponent의 significand를 right shift한다.
- 부호가 같으면 significand를 더하고, 다르면 큰 magnitude에서 작은 magnitude를 뺀다.
- 연산 뒤 normalization과 rounding으로 IEEE 754 표현에 맞춘다.

## 구현과 학습 포인트
구현은 special case 처리, exponent alignment, signed significand addition/subtraction, leading bit 기준 normalization, rounding, result bit packing 순서로 작성하는 것이 안전하다. subtraction은 두 번째 operand의 sign을 뒤집은 addition으로 해석할 수 있지만, zero와 infinity case는 별도 검사가 필요하다.

## 자주 헷갈리는 지점
- denormal에 hidden 1을 붙이면 값이 틀린다.
- exponent 차이가 매우 클 때 작은 operand가 rounding bit로만 영향을 줄 수 있다.
- positive zero와 negative zero 처리, infinity끼리의 뺄셈, NaN propagation 같은 edge case를 빠뜨리기 쉽다.
- fraction field와 significand 전체를 혼동하면 bit shift와 normalization이 어긋난다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Arithmetic for Computers](03-arithmetic-for-computers.md)
- [Instructions Language of the Computer](02-instructions-language-of-the-computer.md)

{% endraw %}

---

이전: [07. Assignment 2 Functions](07-assignment-2-functions.md) · 다음: [09. Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
