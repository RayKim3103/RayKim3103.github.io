---
layout: page
title: "03. PYNQ GPIO, DEMUX, MUX, Decoder"
permalink: /studies/digital/basic-digital-lab/03-pynq-gpio-demux-decoder/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/03%20PYNQ%20GPIO%20DEMUX%20Decoder%20-%20GPIO%20DEMUX%20Decoder.md)

{% raw %}
tags: #basic-digital-experiment #pynq #gpio #demux #mux #decoder #rgb-led

관련 노트: [가산기, 2의 보수, Verilog 디버깅](02-adders-two-complement-debugging.md), [순차논리, Shift Register, Counter](04-sequential-logic-shift-register-counter.md)

## 핵심 요약

이 자료는 PYNQ 보드의 GPIO 입출력을 사용해 DEMUX, MUX, decoder, RGB LED decoder를 구현하는 실험이다. 조합논리 회로는 현재 입력만으로 출력이 결정되며, `always @(*)`, `case`, `if` 문을 통해 선택 논리를 표현한다.

## PYNQ GPIO 구성

### 버튼과 스위치

| 장치 | 핀 |
|---|---|
| BTN0 | D19 |
| BTN1 | D20 |
| BTN2 | L20 |
| BTN3 | L19 |
| SW0 | M20 |
| SW1 | M19 |

### 일반 LED

| LED | 핀 |
|---|---|
| LD0 | R14 |
| LD1 | P14 |
| LD2 | N16 |
| LD3 | M14 |

일반 LED는 보호용 저항을 거쳐 FPGA 핀과 연결된다.

### RGB LED

PYNQ 보드에는 tri-color LED가 있으며, 각 LED는 R/G/B 채널을 개별적으로 제어한다. 두 RGB LED를 동시에 사용하면 총 6-bit 출력으로 색을 표현할 수 있다.

## 조합논리

조합논리는 현재 입력만으로 출력이 결정되는 회로이다.

```text
output = f(current inputs)
```

이전 상태나 clock을 저장하지 않으므로 latch나 flip-flop이 필요하지 않다. Verilog에서는 `assign` 또는 `always @(*)`로 작성한다.

## DEMUX

DEMUX는 하나의 입력을 control signal에 따라 여러 출력 중 하나로 보낸다.

```text
1 input + n control bits -> 2^n outputs
```

1-to-4 DEMUX에서는 2-bit control이 필요하다.

| control | 출력 |
|---:|---|
| 00 | y[0] = din |
| 01 | y[1] = din |
| 10 | y[2] = din |
| 11 | y[3] = din |

### 구현 포인트

- 출력 벡터는 절차문 안에서 대입되므로 `output reg [3:0]` 형태가 자연스럽다.
- 선택되지 않은 출력은 반드시 0으로 지정해야 한다.
- control 폭을 `[2:0]`처럼 과하게 선언하면 사용하지 않는 상태가 생긴다. 1-to-4 DEMUX에는 `[1:0]`이 적절하다.

## MUX

MUX는 여러 입력 중 하나를 선택해 하나의 출력으로 보낸다.

```text
2^n inputs + n control bits -> 1 output
```

DEMUX가 한 입력을 여러 출력으로 분배한다면, MUX는 여러 입력을 하나로 모은다.

## Decoder

Decoder는 n-bit 입력을 최대 `2^n`개의 one-hot 출력으로 변환한다.

3-to-8 decoder에서는 입력값 하나에 대해 8개 출력 중 하나만 1이 된다.

| 입력 | 출력 |
|---:|---|
| 000 | 00000001 |
| 001 | 00000010 |
| 010 | 00000100 |
| 011 | 00001000 |
| 100 | 00010000 |
| 101 | 00100000 |
| 110 | 01000000 |
| 111 | 10000000 |

Enable 신호를 두면 enable이 꺼졌을 때 모든 출력을 0으로 만들 수 있다.

## RGB Decoder

RGB decoder는 입력값에 따라 두 RGB LED의 색을 정하는 회로이다. 실험에서는 두 RGB LED에 같은 색을 표시하도록 3-bit 색 패턴을 두 번 반복해 6-bit 출력으로 만들었다.

| 입력 | 색 | 6-bit 출력 |
|---:|---|---:|
| 000 | Magenta | 101101 |
| 001 | Red | 100100 |
| 010 | Yellow | 110110 |
| 011 | Green | 010010 |
| 100 | Cyan | 011011 |
| 101 | Blue | 001001 |
| 110 | White | 111111 |
| 111 | Off | 000000 |

출력 bit 순서는 top module과 constraint에서 정의한 RGB 채널 순서와 반드시 일치해야 한다.

## Testbench 작성

테스트벤치는 입력을 10 ns 간격으로 바꿔 모든 경우를 확인한다.

```verilog
initial begin
    control = 2'b00; din = 1'b1; #10;
    control = 2'b01;             #10;
    control = 2'b10;             #10;
    control = 2'b11;             #10;
    $stop;
end
```

모듈 연결은 이름 기반으로 작성하면 포트 순서 실수를 줄일 수 있다.

## 실험 결과 해석

DEMUX, MUX, decoder, RGB decoder 모두 waveform과 FPGA 보드 결과가 이론값과 일치했다. 특히 decoder에서 입력 `011`의 one-hot 출력은 `00001000`이어야 하며, bit 위치를 착각하면 보드 LED와 waveform이 어긋난다.

## 설계상 주의점

- `case` 문은 선택지가 명확한 조합논리에 적합하다.
- `default`를 두면 지정하지 않은 입력에서 latch가 생기거나 이전 값이 유지되는 위험을 줄일 수 있다.
- 단순 조합식은 `assign`으로도 구현 가능하지만, 출력 경우가 많은 decoder/RGB decoder는 `case`가 읽기 쉽다.
- 버튼 입력을 `[0:0] btn`처럼 최소 폭으로 받을 수도 있지만, `[3:0] btn`으로 두면 이후 확장에 유리하다.

## 시험ㆍ복습 체크포인트

- MUX, DEMUX, decoder의 입출력 개수 관계를 설명할 수 있어야 한다.
- one-hot 출력의 bit 위치를 정확히 계산할 수 있어야 한다.
- `always @(*)`와 `case` 문으로 조합논리를 작성할 수 있어야 한다.
- RGB LED의 bit mapping이 실제 색상과 어떻게 연결되는지 해석할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. PYNQ GPIO, DEMUX, MUX, Decoder**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
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
- **03. PYNQ GPIO, DEMUX, MUX, Decoder**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. 가산기, 2의 보수, Verilog 디버깅](02-adders-two-complement-debugging.md) · 다음: [04. 순차논리, Shift Register, Counter](04-sequential-logic-shift-register-counter.md)
