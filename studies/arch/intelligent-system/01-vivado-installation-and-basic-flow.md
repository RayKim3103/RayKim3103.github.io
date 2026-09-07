---
layout: page
title: "01. Vivado Installation and Basic Flow"
permalink: /studies/arch/intelligent-system/01-vivado-installation-and-basic-flow/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Intelligent_System/lecture_notes/01%20Vivado%20Installation%20and%20Basic%20Flow.md)

{% raw %}
﻿---
title: "01. Vivado Installation and Basic Flow"
pages: 23
tags: [intelligent-system, lecture-note, Vivado, FPGA, Verilog, simulation]
---

# 01. Vivado Installation and Basic Flow

> 이전: [Course Introduction and FPGA Overview](00-course-introduction-and-fpga-overview.md)
> 다음: [Verilog Basics and Logic Design](02-verilog-basics-and-logic-design.md)

## 자료의 목적

이 자료는 Vivado 설치, FPGA 프로젝트 생성, source/testbench 추가, simulation 실행까지의 기본 흐름을 안내한다.

## Vivado 설치 요점

설치 대상:

- Vivado 2021.1 계열
- Windows self-extracting installer
- Vivado ML Standard
- Spartan-7 support
- Cable drivers

주의사항:

- AMD/Xilinx 계정 생성과 이메일 인증 필요
- 대학 이메일 사용
- 설치 경로에 한글이 없어야 한다.
- 설치 시간이 길 수 있다.

## 새 프로젝트 생성

기본 절차:

1. Vivado 실행
2. Create Project
3. 프로젝트 이름과 위치 지정
4. RTL Project 선택
5. Do not specify sources at this time 체크
6. Boards 탭에서 target board 선택
7. Finish

권장:

- 프로젝트 경로에 한글/공백을 피한다.
- board file이 안 보이면 Digilent board file을 설치한다.

## Arty S7-50 Board File 설정

board가 Vivado에 보이지 않는 경우:

1. `Arty-s7-50.zip` 압축 해제
2. Vivado 설치 경로의 `data/boards/board_files` 아래에 복사
3. Vivado TCL console에서 board repository path 지정
4. board 선택 과정을 다시 진행

## Vivado Design Flow

| 단계 | 의미 |
|---|---|
| Simulation | testbench로 RTL 동작 검증 |
| Synthesis | RTL을 gate-level netlist로 변환 |
| Implementation | target FPGA에 맞게 placement/routing |
| Generate Bitstream | FPGA programming용 `.bit` 생성 |

중요한 관점:

- simulation은 기능 검증이다.
- synthesis 이후에는 실제 hardware resource와 timing 문제가 나타난다.
- bitstream이 있어야 FPGA에 회로를 올릴 수 있다.

## Source Code 추가

Vivado source 종류:

- Design sources: 합성 가능한 Verilog RTL
- Simulation sources: testbench
- Constraints: pin mapping, clock constraint 등을 담는 `.xdc`

Verilog source를 새로 만들거나 기존 `.v` 파일을 import할 수 있다.

## Source Code Example: AOI

자료 예시는 2-2 AOI 회로를 다룬다.

논리식:

$$
Q = \overline{AB + CD}
$$

Verilog 표현:

```verilog
assign Q = ~((A & B) | (C & D));
```

핵심:

- module은 input/output port를 가진 하드웨어 component이다.
- assign은 combinational logic을 wire로 연결하는 방식이다.
- HDL은 C/Python 같은 순차 프로그램이 아니라 회로 구조를 기술한다.

## Testbench와 Simulation

Simulation에는 testbench가 필요하다.

testbench 역할:

- DUT(Design Under Test)를 instance화한다.
- 입력 stimulus를 준다.
- 출력 response를 관찰한다.
- 필요하면 result checker를 둔다.

기본 절차:

1. Add Sources
2. Add or create simulation sources
3. testbench 파일 생성
4. DUT instance 작성
5. 입력 시나리오 작성
6. Run Behavioral Simulation
7. waveform에서 동작 확인

## Debug 포인트

- 입력은 testbench에서 보통 `reg`로 선언한다.
- 출력은 DUT가 drive하므로 testbench에서는 보통 `wire`로 받는다.
- waveform에서 시간에 따른 신호 변화를 확인한다.
- 예상 truth table과 simulation waveform이 일치하는지 확인한다.

## 체크리스트

- 프로젝트 경로에 한글이 없는가?
- target board를 정확히 선택했는가?
- design source와 simulation source를 구분했는가?
- testbench에서 DUT port를 올바르게 연결했는가?
- simulation 후 waveform을 충분히 zoom/fit해서 확인했는가?
- synthesis/implementation/bitstream 흐름을 구분해서 이해했는가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Vivado Installation and Basic Flow**를 다루며, FPGA/SoC 위에서 디지털 회로, 메모리, AXI, 영상/AI 하드웨어를 구현하는 흐름을 익힌다.
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
- **01. Vivado Installation and Basic Flow**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. Course Introduction and FPGA Overview](00-course-introduction-and-fpga-overview.md) · 다음: [02. Verilog Basics and Logic Design](02-verilog-basics-and-logic-design.md)
