---
layout: page
title: "03. 과제 1 — Vending Machine & Board Practice"
permalink: /studies/arch/intelligent-system/03-assignment-1-vending-machine/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `04` 정리·보강
- **과제 요구사항·board I/O 매핑은 원문 그대로**입니다.

{% raw %}
## 개요

첫 개인 과제 = **FSM 기반 vending machine** + FPGA board 구현. 부수적으로 **seven-segment display, button debouncer, clock divider** 를 익힌다.

## 1. Assignment 1 — Vending Machine

| item | 가격 | | coin | value |
|---|---:|---|---|---:|
| Item 1 | \$3 | | coin 1 | \$1 |
| Item 2 | \$5 | | coin 2 | \$5 |
| Item 3 | \$7 | | coin 3 | \$10 |

**사용 절차**: turn on/reset → item stock 채움(최대 5) → coin 투입해 balance 증가(최대 50) → item 선택 시 stock·balance 감소.

가능한 state 예: `OFF`, `IDLE`, `ITEM_FILLING`, `COIN_INSERTING`, `SELLING`, `ERROR/WAIT`.

### Board I/O 매핑
| 신호 | 역할 |
|---|---|
| SW3 | on/off (active high) |
| SW1–SW2 | mode control |
| SW0 | reset (active high) |
| BTN1–BTN3 | mode별 task: filling / coin inserting / selling |
| LED5 | 동작 중 표시 |
| LED2–LED4 | out-of-stock / recently filled 표시 |
| SSD | 최근 채운 stock 또는 balance |

> Active High = 입력 1일 때 활성, Active Low = 0일 때 활성.

## 2. 구성 요소

### Seven-Segment Display (SSD)
- segment A–G (+ 선택적 decimal point). 권장 동작 clock **50 Hz**.
- 숫자 0: A,B,C,D,E,F = 1, G = 0.
- board의 common anode/cathode에 따라 active level이 달라짐 → constraint·board manual 확인.

### Button Debouncing
기계식 버튼은 누르는 순간 bounce → 한 번이 여러 번으로 보임.
```text
raw button → DFF → DFF → debounced signal
```
- 이 수업에선 **2 DFF** 권장. 필요하면 edge detector로 1-clock pulse화.

### Clock Divider
board는 보통 **100 MHz** → SSD·버튼 처리엔 훨씬 낮은 clock 필요 (100 MHz → 50 Hz for SSD, → 1 Hz for visible counter).
- counter가 목표 divide count 도달 시 output tick을 toggle/assert.
- **내부 clock을 직접 새로 만들기보다 enable tick 방식이 timing 관리에 안전**.

## 3. Practice 1 — SSD & GPIO Counter

- 두 버튼으로 counter 증가/감소, SSD 표시, 범위 **00–09 saturation**, 50 Hz, resetn active low.
- I/O 예: `CLK100MHZ→clk`, `SW0→resetn`, `BTN[0]→in_up`, `BTN[1]→in_down`, `jc[0:3]→aa-ad`, `jd[0:2]→ae-ag`, `jd[3]→cat`.
- 포인트: debouncer 필수, clock divider 필수, 0/9 경계 saturation.

## 4. Practice 2 — FSM Controller

- 2 switch로 state: `IDLE`, `UP`, `DOWN`, `READY`. SSD는 counting number, LED[0]=UP·LED[1]=DOWN 표시.
- UP: 1초마다 +1, DOWN: 1초마다 −1, READY: 값 유지. 범위 **0–15 saturation**.
```text
clk_divider → 50 Hz, 1 Hz tick
fsm_ctrl    → state/control
ssd_ctrl    → display
top module  → board I/O 연결
```

## 5. 제출

- Week3 두 practice에 대한 report 1개. Week4에 board implementation(코드+report), **출석 필수**.

## 자주 틀리는 지점

- 버튼 입력은 반드시 debouncing/edge detection.
- SSD는 사람이 보는 장치라 느린 refresh clock/enable 필요.
- board implementation에서는 **`.xdc` pin mapping이 코드만큼 중요**.
- counter/FSM practice는 vending machine의 작은 구성요소로 이해.

## 복습 질문

- vending machine의 state와 mode를 어떻게 나누는가?
- 2-DFF debouncer가 bounce를 제거하는 원리와, edge detector가 추가로 필요한 경우는?
- 100 MHz에서 50 Hz tick을 만드는 divide count 계산, 그리고 "직접 clock 생성" 대신 enable tick을 쓰는 이유는?
- board 구현에서 `.xdc`가 왜 중요한가?
{% endraw %}

---

이전: [02. Verilog와 FSM](02-verilog-and-fsm.md) · 다음: [04. 메모리 — SRAM & FPGA BRAM](04-memory-sram-and-fpga-bram.md)
