---
layout: page
title: "02. Verilog와 FSM"
permalink: /studies/arch/intelligent-system/02-verilog-and-fsm/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `02(Verilog Basics)` + `03(FSM)` 통합·보강
- 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

Verilog HDL의 기본 문법과 조합/순차 회로 설계, 그리고 그 위에서 **FSM (Finite State Machine)** 설계. FSM은 이후 memory·UART·accelerator 제어에 반복 등장한다.

---

# Part 1. Verilog 기본

## 1. HDL은 프로그래밍 언어가 아니다

코드를 "실행 순서"로만 읽으면 안 되고 **어떤 회로·연결이 만들어지는지** 생각해야 한다.
```verilog
z[3] <= a[3] + b[3];
z[2] <= a[2] + b[2];
z[1] <= a[1] + b[1];
z[0] <= a[0] + b[0];
```
→ 네 bit 연산이 clock edge에서 **동시에** 일어나는 hardware 동작.

## 2. Module & Instantiation

```verilog
module adder (
    output reg [4:0] y,
    input wire [3:0] a,
    input wire [3:0] b
);
    // logic
endmodule
```
| Python | Verilog |
|---|---|
| function | module |
| parameter | port |
| variable | wire / reg |
| call | instantiation |

- 위치 기반 `adder m1(y1, a1, b1);` vs **이름 기반** `adder m2(.a(a2), .b(b2), .y(c2));` — 이름 기반 권장(순서 헷갈려도 의도 명확).

## 3. wire vs reg

| | 용도 |
|---|---|
| `wire` | continuous assignment(`assign`), module 간 연결 |
| `reg` | procedural assignment(`always`/`initial` 내부), 값 유지 |

- input port는 보통 wire. output은 wire/reg 둘 다 가능.
- **`reg`라고 반드시 flip-flop이 되는 건 아니다** — always block의 sensitivity와 할당 방식에 따라 조합논리도 됨.

## 4. Blocking `=` vs Non-blocking `<=`

- **blocking `=`**: 문장이 순차 평가(앞 결과가 뒤에 영향). 조합논리용.
- **non-blocking `<=`**: clock edge에서 **동시 update**되는 register 동작. **sequential logic 표준**.
```verilog
always @(posedge clk) begin C <= B; B <= A; A <= D; end  // 3-stage shift
```

## 5. 기타

- `parameter`(instance 시 override 가능) vs `localparam`(내부 고정).
- number: `<width>'<base><value>` → `4'b1111`, `4'd15`, `4'hF`. logic value: `0`, `1`, `x`(unknown), `z`(high-Z).
- operator: arithmetic, bitwise(`~ & | ^ ^~`), shift, equality, relational, logical, **reduction**(`&a`, `|a`, `^a`), **concatenation**(`{a,b}`), **replication**(`{4{a}}`), conditional(`sel ? a : b`).

## 6. 조합 vs 순차 코딩

- 조합: `assign out = ~in;` 또는 `always @(*)` + `if`/`case`. **모든 branch에서 모든 출력에 값 할당**해야 latch inference 방지.
- 순차: `always @(posedge clk)` + non-blocking. async active-low reset:
```verilog
always @(posedge clk or negedge rst)
    if (!rst) q <= 0;
    else q <= d;
```
- ripple-carry adder: 1-bit full adder(`S = A^B^Cin`, `Cout = (A&B)|((A^B)&Cin)`)를 cascade.

---

# Part 2. FSM

## 7. 두 명세

| 명세 | 의미 |
|---|---|
| functional specification | 입력↔출력 논리 관계 |
| timing specification | 입력 변화 후 출력 응답까지 delay (clock period, setup/hold, propagation delay) |

## 8. Combinational vs Sequential

| | Combinational | Sequential |
|---|---|---|
| 메모리 | 없음 | **있음 (state)** |
| 출력 의존 | 현재 입력 | 현재 입력 + 과거 상태 |
| 예 | decoder, mux, adder | counter, FSM, processor controller |

**state** = 시스템의 현재 상황 snapshot. 같은 현재 입력이라도 과거 상태에 따라 다음 동작이 달라지므로 필요(금고 lock sequence `R3→L20→R8` 예).

## 9. FSM 구조

```text
inputs ─► next-state logic ─► next state ─► state register ─► current state
                                             current state ─► output logic ─► outputs
```
- state register가 clock edge에서 next state를 latch.
- clock period ≥ combinational next-state path의 최대 delay.

## 10. Moore vs Mealy

| | 출력 | 장점 | 단점 |
|---|---|---|---|
| **Moore** | $\text{output} = f(\text{state})$ | 출력 안정, timing 분석 단순 | 입력 반영에 transition 대기 |
| **Mealy** | $\text{output} = f(\text{state}, \text{input})$ | 입력에 빠르게 반응, state 수↓ | input glitch가 출력에 바로 → timing 관리 중요 |

## 11. Verilog FSM 3-block 패턴 (권장)

```verilog
// 1) state register
always @(posedge clk or negedge resetn)
    if (!resetn) state <= IDLE;
    else state <= next_state;

// 2) next-state logic (조합)
always @(*)
    case (state)
        IDLE: next_state = ...;
        ...
        default: next_state = IDLE;
    endcase

// 3) output logic (조합 또는 순차)
```

## 12. FSM의 한계와 확장

state 수↑ → transition 수 급증 → maintainability·scalability·reusability↓.
→ **hierarchical FSM**, 작은 FSM 여러 개로 분리, **datapath와 controller 분리**, 공통 동작 module화.

## 복습 질문

- blocking `=`과 non-blocking `<=`를 언제 각각 쓰며, 3-stage shift register를 두 방식으로 쓰면 결과 차이는?
- 조합논리 always block에서 latch inference를 피하는 조건은?
- Moore와 Mealy의 출력 정의·장단점, 그리고 3-block FSM 패턴의 각 block 역할은?
- 큰 FSM을 어떻게 분해하는가?
{% endraw %}

---

이전: [01. FPGA 개요와 Vivado 기본 흐름](01-getting-started-fpga-and-vivado.md) · 다음: [03. 과제 1 — Vending Machine](03-assignment-1-vending-machine.md)
