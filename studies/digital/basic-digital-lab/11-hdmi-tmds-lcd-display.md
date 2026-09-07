---
layout: page
title: "11. HDMI, TMDS, LCD Display"
permalink: /studies/digital/basic-digital-lab/11-hdmi-tmds-lcd-display/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Basic_Digital_Experiment/lecture_notes/11%20HDMI%20TMDS%20LCD%20Display%20-%20HDMI%20TMDS%20LCD%20%EC%B6%9C%EB%A0%A5.md)

{% raw %}
tags: #basic-digital-experiment #hdmi #tmds #lcd #display-timing #verilog

관련 노트: [Audio IP, Digital Filters, Stream Delay](10-audio-ip-digital-filters-stream-delay.md), [Sprite, BRAM, FPGA Graphics](12-sprite-bram-graphics.md)

## 핵심 요약

이 자료는 HDMI 출력 구조와 TMDS encoding, LCD timing, test card image generation을 다룬다. Verilog로 timing generator, TMDS encoder, serializer, image pattern module을 연결해 1280x720 화면에 색상 패턴, gradient, square, circle 등을 출력한다.

## HDMI 개요

HDMI(High Definition Multimedia Interface)는 비압축 digital video/audio를 전송하는 interface이다. 일반 Type A HDMI connector는 19개 pin을 사용한다.

## HDMI Pin 기능

| Pin 범위 | 기능 |
|---|---|
| 1-3 | TMDS Data2, 주로 red data |
| 4-6 | TMDS Data1, 주로 green data |
| 7-9 | TMDS Data0, 주로 blue data |
| 10-12 | TMDS clock |
| 13 | CEC |
| 14 | HEAC |
| 15-16 | I2C/EDID용 SCL/SDA |
| 17 | Ground |
| 18 | VDD |
| 19 | Hot Plug Detect |

TMDS data와 clock은 differential pair로 전송되어 noise에 강하다.

## HDMI 송수신 흐름

```text
Source video
-> HDCP encryptor
-> TMDS encoder
-> Serializer
-> Differential driver
-> HDMI cable
-> Receiver CDR/deserializer
-> TMDS decoder
-> HDCP decryptor
-> Display
```

## HDCP

HDCP는 video content 보호를 위한 encryption/authentication 구조이다. 송신기와 수신기는 key exchange를 수행하고, 인증된 장치 사이에서만 video data를 복호화할 수 있다.

## TMDS Encoding

TMDS(Transition Minimized Differential Signaling)는 8-bit video data를 10-bit code로 바꿔 전송한다.

### 목적

- bit transition 수를 줄여 고속 전송 안정성을 높인다.
- DC balance를 맞춰 장시간 0 또는 1로 치우치지 않게 한다.
- differential signaling과 함께 noise에 강한 link를 만든다.

### Encoding 흐름

1. 8-bit 입력에서 1의 개수를 센다.
2. XOR 또는 XNOR 기반 누적 encoding 중 transition이 적은 방식을 선택한다.
3. 9번째 bit에 선택한 방식을 기록한다.
4. running disparity 또는 bias를 고려해 전체 code를 반전할지 결정한다.
5. 10번째 bit에 반전 여부를 기록한다.

## LCD Timing

LCD는 보이는 pixel뿐 아니라 sync와 porch interval을 포함한 일정한 timing으로 구동된다.

| 용어 | 의미 |
|---|---|
| HSYNC | 한 line 시작을 알리는 horizontal sync |
| VSYNC | 한 frame 시작을 알리는 vertical sync |
| HBP/VBP | sync 뒤의 back porch |
| HFP/VFP | 다음 sync 전의 front porch |
| HSLEN/VSLEN | sync pulse width |
| hactive/vactive | 실제 표시 영역 |

계산식:

```text
clocks per line = HSYNC + HBP + hactive + HFP + HSLEN
lines per frame = VSYNC + VBP + vactive + VFP + VSLEN
refresh rate = pixel clock / (clocks per line * lines per frame)
```

## `display_timings`

`display_timings` module은 현재 pixel 좌표와 sync/de 신호를 만든다.

주요 출력:

- `sx`, `sy`: 현재 pixel coordinate
- `hsync`, `vsync`: sync signal
- `de`: display enable, visible 영역 여부

자료의 1280x720 설정에서는 visible coordinate가 `sx >= 0`, `sy >= 0`, 그리고 해상도 범위 안일 때 active display가 된다.

## Test Card Image

### Simple Color Bar

화면을 여러 vertical region으로 나누고 각 region에 다른 RGB 값을 출력한다. HDMI 출력 경로가 정상인지 빠르게 확인하는 데 적합하다.

### Gradient

좌표 bit를 RGB 값에 연결해 자연스러운 색상 변화나 반복 패턴을 만든다. 예를 들어 `i_x`의 하위 bit 폭이 작으면 화면 가로 방향으로 같은 pattern이 여러 번 반복된다.

| x bit 폭 | 1280 화면에서 반복 느낌 |
|---:|---|
| 6 bit | 20회 반복 |
| 7 bit | 10회 반복 |
| 8 bit | 5회 반복 |

### Squares와 Circles

좌표 범위를 조건문으로 나누면 사각형을 만들 수 있다. 원은 중심 `(a, b)`와 반지름 `r`에 대해 다음 조건을 사용한다.

```text
(x - a)^2 + (y - b)^2 <= r^2
```

좌표 차이가 음수가 될 수 있으므로 signed 연산을 주의해야 한다.

## 버튼으로 이미지 선택

버튼 입력을 top module에서 받아 이미지 선택 신호로 사용한다.

| 버튼 | 이미지 |
|---|---|
| BTN3 | square pattern |
| BTN2 | gradient |
| BTN1 | simple color bar |
| BTN0 | reset |

버튼을 누른 순간만 바뀌고 떼어도 선택이 유지되도록 latch/register 형태의 선택 값을 둔다.

## Clock Generation

HDMI에는 pixel clock과 그보다 빠른 serialization clock이 필요하다. 자료에서는 Xilinx `MMCME2_BASE`와 `BUFG`를 이용해 필요한 clock을 만든다.

주요 parameter:

- `CLKFBOUT_MULT_F`
- `DIVCLK_DIVIDE`
- `CLKOUTx_DIVIDE`
- `CLKIN1_PERIOD`
- `CLKOUTx_PHASE`

`BUFG`는 FPGA 내부 clock network에 안정적으로 clock을 배포하기 위한 buffer이다.

## HDMI_TOP 구조

```text
image generator
-> dvi_generator
-> tmds_encoder
-> serializer
-> OBUFDS differential output
-> HDMI connector
```

`OBUFDS` 출력에는 TMDS differential pair용 I/O standard가 지정된다.

## 시험ㆍ복습 체크포인트

- TMDS가 8-bit를 10-bit로 바꾸는 이유를 설명할 수 있어야 한다.
- HSYNC, VSYNC, front porch, back porch, active 영역을 구분할 수 있어야 한다.
- 좌표 기반 image generator가 RGB 값을 만드는 방식을 이해해야 한다.
- serializer와 differential output이 HDMI 전송에서 필요한 이유를 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. HDMI, TMDS, LCD Display**를 다루며, Verilog와 FPGA 보드 실습을 통해 조합논리, 순차논리, SoC, 인터럽트, 디스플레이/오디오 IP를 구현한다.
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
- **11. HDMI, TMDS, LCD Display**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Audio IP, Digital Filters, Stream Delay](10-audio-ip-digital-filters-stream-delay.md) · 다음: [12. Sprite, BRAM, FPGA Graphics](12-sprite-bram-graphics.md)
