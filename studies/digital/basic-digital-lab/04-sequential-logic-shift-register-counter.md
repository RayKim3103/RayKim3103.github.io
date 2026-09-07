---
layout: page
title: "04. 순차논리, Shift Register, Counter"
permalink: /studies/digital/basic-digital-lab/04-sequential-logic-shift-register-counter/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/04%20Sequential%20Logic%20Shift%20Register%20Counter%20-%20%EC%88%9C%EC%B0%A8%EB%85%BC%EB%A6%AC%20%EC%8B%9C%ED%94%84%ED%8A%B8%EB%A0%88%EC%A7%80%EC%8A%A4%ED%84%B0%20%EC%B9%B4%EC%9A%B4%ED%84%B0.md)

{% raw %}
tags: #basic-digital-experiment #sequential-logic #flip-flop #shift-register #counter #verilog

관련 노트: [PYNQ GPIO, DEMUX, MUX, Decoder](03-pynq-gpio-demux-decoder.md), [FSM과 Traffic Light Controller](05-fsm-traffic-light.md)

## 핵심 요약

이 자료는 조합논리에서 순차논리로 넘어가는 주차이다. RS latch, edge-triggered D flip-flop, shift register, binary counter, BCD counter를 구현하면서 clock, reset, 상태 저장, blocking/nonblocking assignment의 차이를 학습한다.

## 순차논리의 의미

조합논리는 현재 입력만으로 출력이 결정된다. 반면 순차논리는 현재 입력뿐 아니라 과거 상태도 출력에 영향을 준다.

```text
next state = f(current state, input)
output     = g(current state, input)
```

따라서 순차논리에는 상태를 저장하는 latch나 flip-flop이 필요하다.

## RS Latch

NOR 기반 RS latch는 `R`, `S` 입력으로 1-bit 상태를 저장한다.

| S | R | 동작 |
|---:|---:|---|
| 0 | 0 | 유지 |
| 1 | 0 | Set |
| 0 | 1 | Reset |
| 1 | 1 | 금지 상태 |

`S=R=1`은 두 출력이 동시에 0이 되어 보수 관계가 깨지므로 피해야 한다.

## Edge-Triggered D Flip-Flop

D flip-flop은 clock edge에서만 입력 `D`를 출력 `Q`로 복사한다.

```verilog
always @(posedge clk or negedge resetn) begin
    if (!resetn)
        q <= 1'b0;
    else
        q <= d;
end
```

positive edge-triggered DFF는 상승 에지에서 동작하고, negative edge-triggered DFF는 하강 에지에서 동작한다.

## Shift Register

Shift register는 여러 flip-flop을 직렬로 연결해 데이터를 한 bit씩 이동시키는 회로이다.

| 종류 | 의미 |
|---|---|
| SISO | Serial In Serial Out |
| SIPO | Serial In Parallel Out |
| PISO | Parallel In Serial Out |
| PIPO | Parallel In Parallel Out |

### 실험 구현

- 10-bit LED 출력 `o_led[9:0]`를 shift register처럼 사용한다.
- 입력은 `clk`, `resetn`, `i_val`이다.
- `always @(negedge resetn or posedge clk)`에서 reset과 clock 동작을 정의한다.
- PYNQ 보드에서는 BTN0을 reset으로 사용하고, 스위치 입력값을 1초 clock마다 밀어 넣는다.

### 결과 해석

스위치를 켜면 LED가 한 칸씩 채워지고, 스위치를 끄면 LED가 한 칸씩 꺼지는 방향으로 이동한다. 이는 입력 bit가 매 clock마다 register chain을 따라 이동한다는 뜻이다.

## Binary Counter

Binary counter는 clock마다 값을 증가 또는 감소시키는 순차회로이다.

### 실험 동작

| push 입력 | 동작 |
|---:|---|
| 001 | +1 |
| 010 | -1 |
| 100 | -4 |

4-bit counter이므로 값 범위는 `0000`부터 `1111`까지이다. overflow와 underflow가 발생하면 2의 보수 표현처럼 wrap-around된다.

예:

```text
1111 + 1 = 0000
0000 - 1 = 1111
```

## BCD Counter

BCD counter는 0부터 9까지만 유효한 decimal digit counter이다. 4-bit를 사용하지만 `1010`부터 `1111`까지는 정상 BCD 숫자가 아니다.

### 실험 동작

| push 입력 | 동작 |
|---:|---|
| 001 | +1 |
| 010 | -1 |
| 100 | +2 |

값이 9를 넘으면 10을 빼고, 0보다 작아지는 경우에는 9로 돌아가도록 보정한다.

```text
9 + 2 -> 1
0 - 1 -> 9
```

## 1초 Clock Generator

FPGA의 기본 clock은 사람이 관찰하기에 너무 빠르다. 따라서 clock divider를 사용해 1초마다 toggle되는 느린 clock을 만든다.

원리는 다음과 같다.

```text
입력 clock edge를 count
목표 count에 도달하면 출력 clock toggle
counter reset
```

PYNQ 보드의 기준 clock을 이용해 half-period에 해당하는 count 값을 정하면 사람이 LED 변화를 볼 수 있는 1초 주기 신호를 만들 수 있다.

## Blocking과 Nonblocking

순차논리에서는 일반적으로 nonblocking assignment `<=`를 사용한다. 같은 clock edge에서 모든 register가 동시에 갱신되는 하드웨어 동작을 표현하기 좋기 때문이다.

```verilog
q1 <= d;
q2 <= q1;
```

blocking assignment `=`는 절차적으로 즉시 대입되는 것처럼 동작한다. BCD counter처럼 한 블록 안에서 값을 먼저 바꾸고 그 결과를 다시 보정하는 코드에서는 동작 차이를 주의해야 한다. 더 안전한 방법은 중간 변수나 명확한 next-state 계산을 두는 것이다.

## 시험ㆍ복습 체크포인트

- 조합논리와 순차논리의 차이를 설명할 수 있어야 한다.
- RS latch의 금지 상태를 이해해야 한다.
- `posedge`, `negedge`, asynchronous reset의 의미를 구분할 수 있어야 한다.
- binary counter와 BCD counter의 overflow 처리 차이를 설명할 수 있어야 한다.
- sequential logic에서 nonblocking assignment를 선호하는 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. 순차논리, Shift Register, Counter**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습의 핵심은 문법보다 hardware timing을 코드가 어떻게 표현하는지 이해하는 것이다.
- 조합논리와 순차논리는 always block sensitivity, assignment 방식, reset 설계에서 확실히 구분해야 한다.
- PYNQ/Zynq 실습에서는 PL 회로와 PS software가 주소맵, GPIO, interrupt로 연결된다.

### 문제 풀이 또는 구현 루틴

- RTL 작성 전 truth table 또는 state diagram을 만들고 testbench로 corner case를 먼저 고정한다.
- 보드에서 틀리면 simulation, constraints, clock/reset, IP address map, software driver 순서로 확인한다.
- 영상/오디오 실습은 sample clock, valid signal, buffer latency를 파형으로 추적한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- simulation 초기값에 기대면 FPGA 전원 인가 후 동작이 달라질 수 있다.
- latch inference는 대부분 빠진 default assignment에서 생긴다.
- MMIO 주소 offset과 bit mask를 잘못 쓰면 hardware가 맞아도 software에서 동작하지 않는다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 모듈은 combinational인가 sequential인가?
- reset 직후 모든 register 값이 정의되는가?
- 보드 출력이 틀릴 때 hardware와 software 중 어느 경계를 먼저 검증할 것인가?
- **04. 순차논리, Shift Register, Counter**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. PYNQ GPIO, DEMUX, MUX, Decoder](03-pynq-gpio-demux-decoder.md) · 다음: [05. FSM과 Traffic Light Controller](05-fsm-traffic-light.md)
