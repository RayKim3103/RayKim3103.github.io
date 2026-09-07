---
layout: page
title: "00. Course Introduction and FPGA Overview"
permalink: /studies/arch/intelligent-system/00-course-introduction-and-fpga-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Intelligent_System/lecture_notes/00%20Course%20Introduction%20and%20FPGA%20Overview.md)

{% raw %}
﻿---
title: "00. Course Introduction and FPGA Overview"
pages: 34
tags: [intelligent-system, lecture-note, course-intro, FPGA, VLSI, AI-SoC]
---

# 00. Course Introduction and FPGA Overview

> 다음: [Vivado Installation and Basic Flow](01-vivado-installation-and-basic-flow.md)

## 강의의 위치

이 자료는 EEE3551 지능형시스템설계및응용의 운영 방식과 큰 기술 배경을 소개한다. 과목의 중심은 “AI 알고리즘을 실제 하드웨어로 설계하고 FPGA에서 검증하는 것”이다.

## 담당 및 선수 지식

- 담당: Prof. Kyuho Lee, Intelligent Systems Laboratory, Yonsei EE
- 연구 키워드: AI SoC, neuromorphic processor, processing-in-memory/computing-in-memory, embedded AI systems
- 선수 과목:
  - Basic Circuit Theory
  - Digital Logic
  - Introductory Digital Labs

## 평가 구조

| 항목 | 비중 | 내용 |
|---|---:|---|
| 출석 | 10% | 3회 결석부터 감점 |
| 개인 프로젝트 | 30% | HDL 기반 디지털 회로 설계 및 FPGA 구현 |
| 팀 프로젝트 | 40% | convolution core, end-to-end CNN accelerator |
| 발표 | 20% | design review, final presentation |

프로젝트 중심 강의이므로 “코드가 simulation에서 맞는가”뿐 아니라 “FPGA board에서 실제로 동작하는가”가 중요하다.

## 프로젝트 구성

### 개인 프로젝트

1. FSM 기반 vending machine
2. UART TRx + memory loopback

개인 프로젝트는 Verilog, FSM, memory, UART, board implementation을 익히는 단계이다.

### 팀 프로젝트

1. Convolution accelerating core
2. End-to-end CNN accelerator

convolution core는 CNN accelerator의 핵심 구성 요소이다. 첫 팀 과제에서 convolution core를 제대로 이해하지 못하면 다음 CNN accelerator 구현이 어려워진다.

## Honor Code와 협업 기준

허용되는 협업은 개념, 요구사항, 일반적인 디버깅 방법, 도구 사용법에 대한 논의이다. 금지되는 것은 타인의 solution, Verilog code, 과거 본인 코드, 생성형 AI가 만든 코드를 그대로 제출하는 것이다.

과제 제출물에는 도움받은 내용을 reference로 명시해야 한다.

## 강의 일정 큰 흐름

| 주차 | 주제 |
|---|---|
| Week 1 | Course introduction |
| Week 2 | Verilog basics, combinational/sequential logic |
| Week 3 | 7-segment RTL/SYN, FSM RTL/SYN |
| Week 4 | Board implementation for assignment 1 |
| Week 5 | Memory, SRAM/BRAM control |
| Week 6 | FIFO, interface |
| Week 7 | UART TRx, assignment 2 board implementation |
| Week 9 | PS/PL, AXI, PYNQ, board implementation |
| Week 10-11 | Neural network basics, CNN, AI hardware |
| Week 12-15 | Team assignment, RTL/SYN/SIM, design review, final presentation |

## Industry Trend: AI와 Big Data

자료는 hyper-connected society, big data, AI를 주요 산업 흐름으로 제시한다. AI workload는 막대한 연산량과 메모리 bandwidth를 요구하므로, GPU/FPGA/ASIC 같은 하드웨어 가속이 중요해진다.

예시로 NVIDIA Blackwell GPU 같은 AI superchip이 언급된다.

## VLSI와 SoC

VLSI(Very-Large-Scale Integration)는 수천 개 이상의 transistor를 하나의 chip에 집적하는 기술이다. SoC(System-on-Chip)는 processor, memory, accelerator, interface 등을 하나의 chip 또는 시스템으로 통합한다.

역사적 흐름:

- ENIAC: 진공관 기반 초기 전자식 컴퓨터
- transistor: Bell Labs, 1948
- integrated circuit: Jack Kilby, 1958
- Intel 4004: 초기 microprocessor
- 현대 microprocessor와 AI accelerator

## Design Abstraction Levels

칩 설계는 여러 추상화 수준을 거친다.

| 수준 | 의미 |
|---|---|
| System level | 전체 기능과 사용 시나리오 |
| Architecture/algorithm level | 연산 구조, 데이터 흐름 |
| Digital system level | datapath, controller, memory |
| Logic level | gate, register, FSM |
| Electrical level | transistor 회로 |
| Layout level | 물리 배치와 배선 |
| Semiconductor level | 공정/소자 |

이 과목은 주로 architecture, digital system, RTL/logic level을 다룬다.

## FPGA란 무엇인가

FPGA(Field-Programmable Gate Array)는 사용자가 원하는 회로로 재구성할 수 있는 집적회로이다.

기본 구성:

- CLB(Configurable Logic Block)
- LUT(Look-Up Table)
- FF(Flip-Flop)
- IOB(I/O Block)
- routing fabric

FPGA는 hard-wired ASIC과 달리 재프로그램 가능하다. 병렬 하드웨어를 직접 구성할 수 있어 CPU/GPU보다 특정 workload에서 더 빠르고 효율적일 수 있다.

## 왜 FPGA를 쓰는가

- 설계 변경이 쉽다.
- ASIC보다 초기 비용이 낮다.
- dedicated hardware 구조로 병렬 연산 가능
- 검증과 교육에 적합하다.
- AI accelerator prototype을 빠르게 구현할 수 있다.

## 강의에서 사용하는 도구

- HDL: Verilog/VHDL
- FPGA board: Xilinx Arty A7/S7, PYNQ-Z2 등
- Tool: Xilinx Vivado

## 핵심 정리

- 이 강의는 Verilog로 하드웨어를 설계하고 FPGA에서 실제 동작을 검증하는 프로젝트형 수업이다.
- 초반에는 digital logic과 FPGA flow를 익히고, 중반에는 memory/FIFO/UART/interface를 구현한다.
- 후반에는 CNN과 AI accelerator 구조를 이해하고, convolution core와 end-to-end accelerator로 확장한다.
- AI 시스템 설계에서 중요한 것은 알고리즘뿐 아니라 data movement, memory bandwidth, hardware parallelism이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **00. Course Introduction and FPGA Overview**를 다루며, FPGA/SoC 위에서 디지털 회로, 메모리, AXI, 영상/AI 하드웨어를 구현하는 흐름을 익힌다.
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
- **00. Course Introduction and FPGA Overview**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

다음: [01. Vivado Installation and Basic Flow](01-vivado-installation-and-basic-flow.md)
