---
layout: page
title: "06. UART와 Memory Loopback (과제 2)"
permalink: /studies/arch/intelligent-system/06-uart-and-memory-loopback/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `08` 정리·보강
- **UART frame·과제 사양은 원문 그대로**입니다.

{% raw %}
## 개요

hardware communication과 **UART** Rx/Tx timing, 그리고 개인 과제 2 = **memory loopback**(UART Rx → BRAM → UART Tx).

## 1. 통신 분류

| | 설명 | 예 |
|---|---|---|
| **Serial** | 한 signal line으로 bit 순차 전송 | UART |
| **Parallel** | 여러 line으로 여러 bit 동시 | bus |

| | clock 공유 | 방식 |
|---|---|---|
| **Asynchronous** | 없음 | baud rate·data length·start/stop bit·parity를 미리 약속 |
| **Synchronous** | 있음 | 공유 clock으로 sampling |

## 2. UART Frame

**UART** = Universal **Asynchronous** Receiver Transmitter.
```text
IDLE(1) → Start bit(0) → Data bits (LSB → MSB) → Stop bit(1)
```
자료 기준: idle=1, start=0, stop=1, data 8-bit, LSB first, baud 115200 / 57600 / 38400 bps.

$$
\text{Baud rate} = \frac{\text{number of signals}}{\text{time in seconds}}
$$
clock frequency에서 baud tick divisor 계산 필요. baud rate가 communication bandwidth와 **timing mismatch error rate**를 결정.

## 3. UART Rx

| 신호 | 역할 |
|---|---|
| `CLK`, `RST` | 기준 clock, reset |
| `RxD` | 외부 serial input |
| `RxD_CLK_Rx` | 내부 clock에 동기화된 RxD |
| `Frm_ERR` | stop bit 미감지 시 high |
| `Rx_DATA[7:0]` | 수신 8-bit data |
| `Rx_DATA_rdy` | data valid |
| `Index[10:0]` | 수신 data 개수 |

**flow**: idle high → falling edge/low로 start bit 감지 → baud timing에 맞춰 8 data bits sampling → stop bit 확인 → `Rx_DATA_rdy` high → stop bit ≠ 1이면 frame error.
- **asynchronous input은 metastability 방지를 위해 clock domain synchronization** 필요.
- `Rx_DATA_rdy`가 여러 clock 동안 high일 수 있어 edge detection 필요할 수 있음.

## 4. UART Tx

| 신호 | 역할 |
|---|---|
| `Tx_Dout[7:0]` | 전송할 8-bit data |
| `Empty` | 전송할 data 없을 때 high |
| `Rd_en` | 다음 data 요청 1-clock pulse |
| `TxD` | serial output |

**flow**: idle high → data 있으면 `Rd_en`으로 요청 → start bit 0 → 8 data bits (LSB first) → stop bit 1 → 다음 data 요청.
- `Rd_en`이 **1 clock pulse**이므로 memory/FIFO controller는 그 순간 다음 data를 준비해야 함.

## 5. Assignment 2 — Memory Loopback

FPGA가 UART Rx로 image data를 받아 BRAM에 저장 → UART Tx로 다시 PC로.
- data: **128 × 128 image, 8-bit per pixel** (= 16384 byte).
```text
Tester TX → DUT UART_RX → Memory Controller → BRAM
BRAM → Memory Controller → UART_TX → Tester RX
```
**동작**:
1. `rx_switch` ON → PC의 UART data 대기
2. PC가 image data 전송
3. image size만큼 수신되면 LED high, 대기
4. `tx_switch` ON → BRAM에서 read해 UART Tx로 PC에 재전송
5. PC/testbench가 받은 data를 grading 기준과 비교

**설계 포인트**: Rx/Tx side를 별도 FSM(또는 mode FSM). Rx data ready pulse를 정확히 잡아 BRAM write enable로 변환. 16384 byte address 순차 증가. Tx `Rd_en` 시 BRAM read latency에 맞춰 data 공급. `rx_switch`/`tx_switch`/LED를 FSM state와 일관되게.

## 자주 틀리는 지점

- UART는 clock 공유가 없어 **baud timing이 핵심**.
- Rx: start bit·data bit sampling·stop bit validation. Tx: idle/start/data/stop sequence 정확히.
- **ready/valid 또는 empty/rd_en handshake를 놓치면 data loss**.
- async input의 metastability → 2-FF synchronizer.

## 복습 질문

- UART frame 구조와, baud rate가 error rate에 영향을 주는 이유는?
- Rx에서 start bit 감지 후 sampling을 어느 시점에 하는가 (baud tick 중앙)?
- Tx의 `Rd_en`이 1-clock pulse일 때 memory controller가 지켜야 할 것은?
- memory loopback에서 UART·BRAM·FSM을 어떻게 통합하는가?
{% endraw %}

---

이전: [05. FIFO와 Line Buffer](05-fifo-and-line-buffer.md) · 다음: [07. PS/PL · AXI · PYNQ · ILA](07-ps-pl-axi-pynq-and-ila.md)
