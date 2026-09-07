---
layout: page
title: "08. UART and Memory Loopback"
permalink: /studies/arch/intelligent-system/08-uart-and-memory-loopback/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Intelligent_System/lecture_notes/08%20UART%20and%20Memory%20Loopback.md)

{% raw %}
﻿---
title: "08. UART and Memory Loopback"
pages: 24
tags: [intelligent-system, lecture-note, UART, communication, BRAM, assignment]
---

# 08. UART and Memory Loopback

> 이전: [FIFO and Line Buffer](07-fifo-and-line-buffer.md)
> 다음: [PS/PL, AXI, PYNQ, and ILA](09-ps-pl-axi-pynq-and-ila.md)

## 학습 목표

Week6-2 자료는 hardware communication과 UART Rx/Tx timing을 설명하고, Assignment 2인 memory loopback 구조를 제시한다.

## Hardware Communication

일반적인 hardware system은 binary scale로 동작하므로 data movement도 binary rule에 따라 수행된다.

용어:

- TX: transmitter, data를 보내는 쪽
- RX: receiver, data를 받는 쪽

## Serial vs Parallel Communication

| 방식 | 설명 | 예 |
|---|---|---|
| Serial | 하나의 signal line으로 bit를 순차 전송 | UART |
| Parallel | 여러 signal line으로 여러 bit를 동시에 전송 | bus |

Serial communication은 배선 수가 적지만 시간에 따라 bit를 해석해야 한다. Parallel bus는 bandwidth가 높을 수 있지만 wiring과 timing이 복잡하다.

## Asynchronous vs Synchronous

### Asynchronous

TX와 RX가 clock을 공유하지 않는다. 대신 다음 rule을 미리 약속한다.

- baud rate
- data length
- start bit
- stop bit
- parity 여부

### Synchronous

TX와 RX가 clock signal을 공유한다. clock 기준으로 data를 sampling한다.

## UART 기본 규칙

UART(Universal Asynchronous Receiver Transmitter)의 기본 frame:

```text
IDLE(1) -> Start bit(0) -> Data bits(LSB to MSB) -> Stop bit(1)
```

자료 기준:

- idle value: 1
- start bit: 0
- stop bit: 1
- data: 8-bit
- bit order: LSB to MSB
- baud rate: 115200 bps, 57600 bps, 38400 bps 등

## Baud Rate

baud rate는 초당 signal event 수이다.

$$
\text{Baud rate}=\frac{\text{number of signals}}{\text{time in seconds}}
$$

중요성:

- communication bandwidth 결정
- timing mismatch에 따른 error rate에 직접 영향

clock frequency에서 baud tick을 만들 때 divisor 계산이 필요하다.

## UART Rx Design

주요 신호:

| 신호 | 역할 |
|---|---|
| `CLK` | 내부 기준 clock |
| `RST` | reset |
| `RxD` | 외부 serial input |
| `RxD_CLK_Rx` | 내부 clock에 동기화된 RxD |
| `Frm_ERR` | stop bit가 감지되지 않았을 때 high |
| `Rx_DATA[7:0]` | 수신된 8-bit data |
| `Rx_DATA_rdy` | 수신 data valid |
| `Index[10:0]` | 수신 data 개수 |

Rx flow:

1. idle high 상태 유지
2. falling edge 또는 low level로 start bit 감지
3. baud timing에 맞춰 8 data bits sampling
4. stop bit 확인
5. data valid 시 `Rx_DATA_rdy` high
6. stop bit가 1이 아니면 frame error

주의:

- asynchronous input은 metastability 방지를 위해 clock domain synchronization이 필요하다.
- `Rx_DATA_rdy`가 여러 clock 동안 high일 수 있으므로 edge detection이 필요할 수 있다.

## UART Tx Design

주요 신호:

| 신호 | 역할 |
|---|---|
| `Tx_Dout[7:0]` | 전송할 8-bit input data |
| `Empty` | 전송할 data가 없을 때 high |
| `Rd_en` | Tx가 다음 data를 요청하는 1-clock pulse |
| `TxD` | serial output |

Tx flow:

1. idle high 유지
2. 전송 data가 있으면 `Rd_en`으로 data 요청
3. start bit 0 전송
4. 8 data bits를 LSB부터 전송
5. stop bit 1 전송
6. 다음 data 요청

`Rd_en`이 1 clock pulse이므로 memory/FIFO controller는 그 순간 다음 data를 준비해야 한다.

## Assignment 2: Memory Loopback

목표:

FPGA가 UART Rx로 image data를 받아 BRAM에 저장한 뒤, UART Tx로 다시 PC/tester에 보내는 구조를 설계한다.

data:

- 128 x 128 image
- 8-bit per pixel

구조:

```text
Tester TX -> DUT UART_RX -> Memory Controller -> BRAM
BRAM -> Memory Controller -> UART_TX -> Tester RX
```

## Memory Loopback 동작

1. `rx_switch`를 켜면 module이 PC에서 들어오는 UART data를 기다린다.
2. PC가 image data를 UART Rx로 전송한다.
3. image size만큼 data가 수신되면 LED가 high가 되고 대기한다.
4. `tx_switch`를 켜면 BRAM에서 data를 읽어 UART Tx로 PC에 다시 보낸다.
5. PC/testbench가 받은 data를 grading 기준과 비교한다.

## Controller 설계 포인트

- Rx side와 Tx side를 별도 FSM으로 나누거나 mode FSM으로 관리한다.
- Rx data ready pulse를 정확히 잡아 BRAM write enable로 변환한다.
- 128x128 = 16384 byte address를 순차 증가시킨다.
- Tx가 `Rd_en`을 줄 때 BRAM read latency에 맞춰 data를 공급한다.
- `rx_switch`, `tx_switch`, LED 상태를 FSM state와 일관되게 연결한다.

## 체크포인트

- UART는 clock 공유가 없으므로 baud timing이 핵심이다.
- Rx는 start bit, data bit sampling, stop bit validation이 필요하다.
- Tx는 idle, start, data, stop sequence를 정확히 만든다.
- ready/valid 또는 empty/rd_en handshake를 놓치면 data loss가 생긴다.
- memory loopback은 UART, BRAM, FSM controller를 통합하는 과제이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. UART and Memory Loopback**를 다루며, FPGA/SoC 위에서 디지털 회로, 메모리, AXI, 영상/AI 하드웨어를 구현하는 흐름을 익힌다.
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
- **08. UART and Memory Loopback**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. FIFO and Line Buffer](07-fifo-and-line-buffer.md) · 다음: [09. PS/PL, AXI, PYNQ, and ILA](09-ps-pl-axi-pynq-and-ila.md)
