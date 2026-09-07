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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Assignment 3 Floating-Point Numbers**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
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
- **08. Assignment 3 Floating-Point Numbers**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. Assignment 2 Functions](07-assignment-2-functions.md) · 다음: [09. Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
