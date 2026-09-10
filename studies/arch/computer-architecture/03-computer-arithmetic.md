---
layout: page
title: "03. Computer Arithmetic"
permalink: /studies/arch/computer-architecture/03-computer-arithmetic/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `03` + 과제 `08(Floating-Point)` 통합·보강
- 교재: Patterson & Hennessy ch 3. **과제 세부는 원문 그대로**입니다.

{% raw %}
## 개요

정수·부동소수점 arithmetic이 hardware에서 어떻게 수행되는가. binary add/sub, overflow, adder 구조, ALU, IEEE 754.

## 1. 정수 덧셈/뺄셈

- 덧셈: bit별 sum + carry propagation.
- 뺄셈 (2's complement): $$A - B = A + (\sim B + 1)$$.
- **overflow 판정 (signed)**: **같은 부호**를 더했는데 결과 부호가 바뀌면 overflow. (carry_in ⊕ carry_out of MSB 로도 판정.)
- unsigned overflow는 판정 기준이 다르다 (MSB에서 carry-out 발생).

## 2. Adder 구조

| | 동작 | 지연 |
|---|---|---|
| **Ripple Carry Adder (RCA)** | carry가 LSB→MSB 순차 전달 | $$O(n)$$, 느림 |
| **Carry Lookahead Adder (CLA)** | generate/propagate로 carry 병렬 계산 | $$O(\log n)$$, 빠름 |

$$
g_i = a_i \cdot b_i,\qquad p_i = a_i \oplus b_i,\qquad
c_{i+1} = g_i + p_i \cdot c_i
$$
전개하면 $$c_{i+1}$$ 을 $$c_0$$ 로부터 직접(병렬로) 계산 → carry chain 지연 제거.

## 3. 곱셈/나눗셈

- 곱셈: shift-and-add (partial product 누적). hardware는 Wallace tree 등으로 partial product를 병렬 합산.
- 나눗셈: shift-subtract-restore (또는 non-restoring, SRT).

## 4. ALU

control signal에 따라 add / sub / and / or / comparison / branch test 등을 수행. RISC-V datapath에서 주소 계산·연산·branch 비교를 모두 담당.

## 5. IEEE 754 Floating-Point

$$
\text{value} = (-1)^s \times 1.f \times 2^{\,e - \text{bias}}
\quad(\text{normalized})
$$

| precision | 크기 | sign | exponent | fraction | bias |
|---|---|---|---|---|---|
| single | 32-bit | 1 | 8 | 23 | 127 |
| **double** | **64-bit** | **1** | **11** | **52** | **1023** |

- **normalized**: hidden(implicit) leading 1을 가진 significand.
- **denormalized**: exponent = 0, **hidden 1 없음** (0 근처 gradual underflow).
- 특수값: exponent 全1 → fraction=0이면 ±∞, fraction≠0이면 NaN. signed zero(±0) 존재.

### FP add/sub 절차
1. **special case 처리** (0, ∞, NaN)
2. **exponent alignment**: 작은 exponent의 significand를 right shift (sticky bit 유지)
3. **significand 연산**: 부호 같으면 더하고, 다르면 큰 magnitude − 작은 magnitude
4. **normalization**: leading bit 기준 shift, exponent 조정
5. **rounding** (round-to-nearest-even 등)
6. **result bit packing**

---

## 과제 A3 — Double-precision FP add/subtract 구현

- C++ `float64_t` class에서 IEEE 754 bit layout(sign 1 / exponent 11 / fraction 52)을 분해, hardware FP unit의 절차를 software로 재현.
- `operator+` / `operator-` skeleton은 dummy를 반환 → sign/exponent/fraction 계산을 채운다.
- 구조: `Makefile`, `float.h`, `float.cc`, `main.cc` — 실질 구현은 `float.cc`의 operator 함수.
- 테스트: 일반 수 + **denormalized** + ±**infinity** 등 edge case.
- 구현 순서: special case → exponent alignment → signed significand add/sub → leading bit 기준 normalization → rounding → bit packing.
- subtraction = 두 번째 operand의 sign을 뒤집은 addition으로 볼 수 있으나 **zero/infinity case는 별도 검사**.

## 자주 틀리는 지점

- signed overflow와 unsigned overflow의 판정 기준이 다르다.
- FP 덧셈에서 exponent 차이가 클 때 작은 operand가 **rounding bit(sticky)로만** 영향 → 이를 놓치면 rounding 오류.
- **denormal에 hidden 1을 붙이면 값이 틀린다.**
- ±0 처리, ∞−∞, NaN propagation 같은 edge case 누락.
- fraction field(52-bit) ↔ significand 전체(53-bit with hidden 1) 혼동 → shift/normalization 어긋남.

## 복습 질문

- 2's complement 뺄셈 식과 signed overflow 판정 기준은?
- CLA의 generate/propagate 정의와, 왜 RCA보다 빠른가?
- IEEE 754 double의 필드 구성과 normalized value 식, bias는?
- FP add/sub 6단계와, denormal·특수값에서 주의점은?
{% endraw %}

---

이전: [02. Instructions & RISC-V ISA](02-instructions-and-risc-v-isa.md) · 다음: [04. The Processor: Datapath & Pipeline](04-processor-datapath-and-pipeline.md)
