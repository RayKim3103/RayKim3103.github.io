---
layout: page
title: "03. FSM and Sequential System Design"
permalink: /studies/arch/intelligent-system/03-fsm-and-sequential-system-design/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Intelligent_System/lecture_notes/03%20FSM%20and%20Sequential%20System%20Design.md)

{% raw %}
﻿---
title: "03. FSM and Sequential System Design"
pages: 22
tags: [intelligent-system, lecture-note, FSM, sequential-logic, Verilog]
---

# 03. FSM and Sequential System Design

> 이전: [Verilog Basics and Logic Design](02-verilog-basics-and-logic-design.md)
> 다음: [Assignment 1 Vending Machine and Board Practice](04-assignment-1-vending-machine-and-board-practice.md)

## 학습 목표

Week2-2 자료는 조합논리와 순차논리의 차이를 FSM 관점에서 정리하고, Moore/Mealy machine과 실제 FSM 설계 예제를 다룬다.

## Logic Circuit의 두 명세

logic circuit은 입력, 출력, 그리고 두 종류의 specification으로 설명된다.

| 명세 | 의미 |
|---|---|
| functional specification | 입력과 출력 사이의 논리 관계 |
| timing specification | 입력 변화 후 출력이 응답하기까지의 delay |

하드웨어 설계에서는 기능이 맞는 것만으로 충분하지 않다. clock period, setup/hold, propagation delay까지 고려해야 한다.

## Combinational Logic과 Sequential Logic

| 구분 | Combinational | Sequential |
|---|---|---|
| 메모리 | 없음 | 있음 |
| 출력 의존성 | 현재 입력 | 현재 입력 + 과거 상태 |
| 예 | decoder, mux, adder | counter, FSM, processor controller |

순차논리는 “상태(state)”를 저장하기 때문에 입력 history가 출력에 영향을 준다.

## State의 의미

state는 시스템의 현재 상황을 요약한 snapshot이다.

예: 금고 lock sequence가 `R3 -> L20 -> R8`이면 상태는 다음처럼 나뉜다.

1. 아무 유효 동작도 수행하지 않은 locked 상태
2. R3까지 완료한 상태
3. R3-L20까지 완료한 상태
4. R3-L20-R8 완료로 unlocked 상태

같은 현재 입력이라도 과거 상태에 따라 다음 동작이 달라지므로 state가 필요하다.

## Clock과 State Transition

상태는 언제 바뀌는가?

- clock edge에서 state register가 next state를 latch한다.
- combinational next-state logic은 한 clock cycle 동안 다음 상태를 계산한다.
- clock period는 combinational path의 최대 delay를 감당할 수 있어야 한다.

FSM 구조:

```text
inputs -> next state logic -> next state -> state register -> current state
                               current state -> output logic -> outputs
```

## Finite State Machine

FSM은 stateful system의 discrete-time model이다.

구성 요소:

- finite number of states
- external inputs
- external outputs
- state transition rules
- output generation rules

응용:

- traffic light
- elevator
- vending machine
- microprocessor controller
- UART controller
- memory controller

## Moore Machine과 Mealy Machine

### Moore Machine

출력이 현재 state에만 의존한다.

$$
output = f(state)
$$

장점:

- 출력이 안정적이다.
- timing 분석이 상대적으로 단순하다.

단점:

- 입력 변화가 출력에 반영되려면 state transition을 기다려야 할 수 있다.

### Mealy Machine

출력이 현재 state와 현재 input에 모두 의존한다.

$$
output = f(state,input)
$$

장점:

- 입력 변화에 빠르게 반응할 수 있다.
- state 수가 줄어들 수 있다.

단점:

- input glitch가 출력에 바로 나타날 수 있어 timing 관리가 더 중요하다.

## Verilog FSM 기본 패턴

권장 구조는 state register, next-state logic, output logic을 분리하는 방식이다.

```verilog
always @(posedge clk or negedge resetn) begin
    if (!resetn) state <= IDLE;
    else state <= next_state;
end

always @(*) begin
    case (state)
        IDLE: next_state = ...;
        ...
        default: next_state = IDLE;
    endcase
end

always @(*) begin
    // output logic
end
```

## T-Bird Tail Lights 예제

자료는 자동차 방향지시등을 FSM 예제로 제시한다.

설계 절차:

1. state register 선언
2. state encoding 정의
3. reset 시 known state로 초기화
4. 현재 state와 input에 따라 next state 결정
5. state에 따라 LED output 결정
6. simulation에서 방향 전환과 reset 동작 확인

## FSM의 한계

state 수가 많아지면 가능한 transition 수가 급격히 늘어난다.

문제:

- maintainability: state 추가/삭제 시 관련 transition 수정 범위가 커진다.
- scalability: state diagram이 복잡해져 가독성이 떨어진다.
- reusability: behavior가 state 내부 조건에 강하게 묶여 재사용이 어렵다.

해결 방향:

- hierarchical FSM
- 작은 FSM 여러 개로 분리
- datapath와 controller 분리
- 공통 동작을 module화

## 체크포인트

- sequential logic에는 memory/state가 있다.
- state transition은 clock edge에서 일어난다.
- Moore는 출력이 state만의 함수, Mealy는 state와 input의 함수이다.
- FSM은 controller 설계의 기본이며 이후 memory, UART, accelerator 제어에 반복적으로 등장한다.
- 큰 FSM은 계층화하거나 여러 작은 FSM으로 나누는 것이 좋다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. FSM and Sequential System Design**를 다루며, FPGA/SoC 위에서 디지털 회로, 메모리, AXI, 영상/AI 하드웨어를 구현하는 흐름을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 디지털 구현 주제에서는 cycle 단위 timing, reset 상태, handshake 조건을 파형으로 검증하는 습관이 중요하다.
- 합성 가능한 RTL과 testbench 전용 문법을 구분해 실제 hardware 의미를 확인한다.
- Verilog 설계는 기능보다 clock, reset, enable, latency, valid 신호가 먼저 안정적이어야 한다.
- FPGA 시스템은 PS와 PL, AXI interconnect, BRAM, interrupt, ILA 디버깅이 하나의 데이터 경로로 이어진다.
- AI 하드웨어에서는 quantization, data reuse, memory bandwidth가 연산량만큼 중요하다.

### 문제 풀이 또는 구현 루틴

- 모듈을 만들기 전에 입출력 신호, cycle latency, reset 후 상태, testbench 관찰 포인트를 적는다.
- 보드 실험은 simulation, synthesis warning 확인, bitstream, MMIO/driver, ILA 순서로 좁혀 간다.
- 영상/메모리 경로는 address generation과 line buffer timing을 파형으로 검증한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- blocking/nonblocking assignment를 섞으면 simulation과 hardware 의미가 어긋난다.
- AXI handshake에서 valid와 ready가 동시에 참인 cycle만 transfer가 일어난다.
- 성능을 MAC 개수로만 계산하면 memory bandwidth와 buffering 비용을 놓친다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 상태는 어떤 clock edge에서 바뀌는가?
- data valid가 한 cycle 밀릴 때 downstream 모듈은 어떻게 반응하는가?
- PS-PL 경계에서 주소, cache, interrupt, register map을 모두 확인했는가?
- **03. FSM and Sequential System Design**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. Verilog Basics and Logic Design](02-verilog-basics-and-logic-design.md) · 다음: [04. Assignment 1 Vending Machine and Board Practice](04-assignment-1-vending-machine-and-board-practice.md)
