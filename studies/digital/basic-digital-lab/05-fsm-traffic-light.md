---
layout: page
title: "05. FSM과 Traffic Light Controller"
permalink: /studies/digital/basic-digital-lab/05-fsm-traffic-light/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/05%20FSM%20Traffic%20Light%20-%20%EC%9C%A0%ED%95%9C%EC%83%81%ED%83%9C%EB%A8%B8%EC%8B%A0%20%EC%8B%A0%ED%98%B8%EB%93%B1.md)

{% raw %}
tags: #basic-digital-experiment #fsm #moore #mealy #traffic-light #verilog

관련 노트: [순차논리, Shift Register, Counter](04-sequential-logic-shift-register-counter.md), [Zynq SoC, ARM, PL/PS, MMIO](06-soc-arm-pl-ps-mmio-zynq.md)

## 핵심 요약

이 자료는 FSM(Finite State Machine)을 이용해 신호등 제어기를 구현한다. Moore machine과 Mealy machine의 차이를 배우고, 상태 전이표를 바탕으로 reset, button input, timer에 따라 RGB LED 신호등 상태가 바뀌도록 설계한다.

## FSM 기본 개념

FSM은 유한한 개수의 상태 중 하나에 머물며, 입력과 현재 상태에 따라 다음 상태로 이동하는 모델이다.

```text
현재 상태 + 입력 -> 다음 상태
상태 또는 상태+입력 -> 출력
```

디지털 회로에서는 register가 현재 상태를 저장하고, 조합논리가 다음 상태와 출력을 계산한다.

## Moore Machine과 Mealy Machine

| 구분 | Moore | Mealy |
|---|---|---|
| 출력 의존성 | 현재 상태만 | 현재 상태와 입력 |
| 출력 변화 시점 | 상태가 바뀌는 clock edge 이후 | 입력 변화에 즉시 반응 가능 |
| 장점 | 안정적이고 glitch 위험이 작음 | 상태 수가 적고 반응이 빠름 |
| 단점 | 반응이 한 clock 늦을 수 있음 | 입력 glitch가 출력에 바로 반영될 수 있음 |

신호등처럼 안정적인 출력이 중요한 회로는 Moore 방식이 읽기 쉽고 안전하다.

## 상태 전이표

상태 전이표는 FSM 설계의 중심이다.

| 항목 | 의미 |
|---|---|
| Present State | 현재 상태 |
| Input | 버튼, reset, timer 등 외부 조건 |
| Next State | 다음 clock에서 이동할 상태 |
| Output | LED 색상, 제어 신호 |

Mealy machine에서는 같은 상태라도 입력에 따라 출력이 달라질 수 있으므로 출력 열에 입력 조건까지 반영해야 한다.

## Traffic Light 1

### 상태 구성

| 상태 | 의미 | RGB LED 출력 |
|---|---|---|
| S0 | Reset | 두 신호등 white |
| S1 | Vehicle pass | 차량 green, 보행자 red |
| S2 | Pedestrian pass | 차량 red, 보행자 green |
| S3 | Flasher | yellow/off 점멸 |

### 버튼 동작

- BTN0: S1과 S2 사이를 전환한다.
- BTN1: flasher 상태 S3으로 들어가거나 S3에서 S1로 돌아온다.
- reset 입력은 초기 상태로 되돌린다.

### 출력 해석

두 RGB LED를 차량 신호와 보행자 신호로 사용한다. RGB bit mapping에 따라 red, green, yellow, white, off가 표현된다.

## Traffic Light 2

Traffic Light 2는 단순 버튼 전환에 timer 조건을 추가한다. 상태가 바뀐 뒤 일정 시간이 지나야 다음 상태로 넘어가도록 설계해 실제 신호등과 비슷한 시간 흐름을 만든다.

### Timer 구현 포인트

- board clock에서는 1초 또는 5초에 해당하는 큰 count 값을 사용한다.
- simulation에서는 빠른 검증을 위해 count 목표값을 작게 둔다.
- count가 0부터 시작하므로 N cycle을 세려면 비교값은 보통 `N - 1`이 된다.

## Traffic Light 3

Traffic Light 3은 중간 상태와 blinking 상태를 더해 현실적인 전환을 구현한다.

| 상태 | 의미 |
|---|---|
| S1 | 차량 green, 보행자 red |
| S2 | 차량 red, 보행자 green |
| S3 | yellow/off flasher |
| S4 | 차량 yellow 전환 상태 |
| S5 | 보행자 green blinking 또는 종료 전환 상태 |

S2에서 일정 시간 보행자 green을 유지하고, S5에서 점멸 후 S1로 돌아가는 구조이다. 신호등에서 green에서 바로 red로 바뀌지 않고 yellow 또는 blinking 상태를 거치는 이유를 회로로 표현한 실험이다.

## 버튼 처리

사람이 버튼을 누르는 시간은 clock 기준으로 매우 길고 불규칙하다. 따라서 단순히 버튼 신호를 그대로 상태 전이에 사용하면 한 번 누른 동안 여러 번 전이될 수 있다.

실험에서는 `push`, `push_reg`, `push_button` 같은 신호를 사용해 버튼 눌림과 떼어짐을 안정적으로 감지한다. 핵심은 이전 버튼 상태를 저장하고 현재 상태와 비교해 한 번의 이벤트만 발생시키는 것이다.

## 상태와 출력 always 블록

FSM은 보통 다음처럼 분리해 작성한다.

```verilog
always @(posedge clk or negedge resetn) begin
    if (!resetn)
        state <= S0;
    else
        state <= next_state;
end

always @(*) begin
    case (state)
        ...
    endcase
end
```

자료에서는 state transition과 output operation을 별도 always block으로 두었다. nonblocking assignment 때문에 같은 clock edge에서 상태를 바꾸고 출력을 계산하면 출력이 이전 상태 기준으로 한 clock 늦게 보일 수 있다. 이 동작은 Verilog의 동시 갱신 모델을 이해하는 좋은 예이다.

## 주의할 점

- 같은 `reg`를 여러 always block에서 동시에 대입하면 multiple driver 문제가 생길 수 있다.
- reset edge와 clock edge에서 같은 값을 서로 다른 블록이 갱신하면 합성 오류나 예측 어려운 동작이 생긴다.
- timer counter는 상태가 바뀔 때 적절히 초기화해야 한다.
- simulation용 count와 FPGA board용 count를 구분해야 검증 시간을 줄일 수 있다.

## 시험ㆍ복습 체크포인트

- Moore와 Mealy machine의 출력 의존성 차이를 설명할 수 있어야 한다.
- 상태 전이표에서 Verilog FSM 코드로 옮기는 과정을 이해해야 한다.
- button edge detection이 필요한 이유를 설명할 수 있어야 한다.
- timer의 비교값에 `-1`이 들어가는 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. FSM과 Traffic Light Controller**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 디지털 구현 주제에서는 cycle 단위 timing, reset 상태, handshake 조건을 파형으로 검증하는 습관이 중요하다.
- 합성 가능한 RTL과 testbench 전용 문법을 구분해 실제 hardware 의미를 확인한다.
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
- **05. FSM과 Traffic Light Controller**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. 순차논리, Shift Register, Counter](04-sequential-logic-shift-register-counter.md) · 다음: [06. Zynq SoC, ARM, PL/PS, MMIO](06-soc-arm-pl-ps-mmio-zynq.md)
