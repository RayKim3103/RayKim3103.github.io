---
layout: page
title: "01. FPGA 개요와 Vivado 기본 흐름"
permalink: /studies/arch/intelligent-system/01-getting-started-fpga-and-vivado/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `00(Course Intro & FPGA Overview)` + `01(Vivado Basic Flow)` 통합·보강
- **강의**: EEE3551 지능형시스템설계및응용. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

이 과목의 목표는 **"AI 알고리즘을 실제 하드웨어로 설계하고 FPGA에서 검증"**하는 것. 프로젝트 중심이라 "simulation에서 맞는가"뿐 아니라 **"FPGA board에서 실제로 동작하는가"**가 중요하다.

## 1. 과목 구성

| 항목 | 비중 | 내용 |
|---|---:|---|
| 출석 | 10% | 3회 결석부터 감점 |
| 개인 프로젝트 | 30% | ① FSM vending machine ② UART TRx + memory loopback |
| 팀 프로젝트 | 40% | ① convolution accelerating core ② end-to-end CNN accelerator |
| 발표 | 20% | design review, final presentation |

- 팀 프로젝트: **convolution core는 CNN accelerator의 핵심 구성요소** — 여기서 막히면 다음이 어렵다.
- Honor code: 개념·요구사항·디버깅 방법·도구 사용법 논의는 OK. 타인/과거 본인/생성형 AI의 solution·Verilog code를 그대로 제출 금지. 도움받은 내용은 reference로 명시.

## 2. 배경 — VLSI / SoC / 설계 추상화

- **VLSI**(Very-Large-Scale Integration): 수천+ transistor를 한 chip에.
- **SoC**(System-on-Chip): processor + memory + accelerator + interface를 통합.
- 역사: ENIAC(진공관) → transistor(Bell Labs, 1948) → IC(Jack Kilby, 1958) → Intel 4004 → 현대 microprocessor/AI accelerator.

| 추상화 수준 | 의미 |
|---|---|
| System | 전체 기능·사용 시나리오 |
| **Architecture / algorithm** | 연산 구조, 데이터 흐름 |
| **Digital system** | datapath, controller, memory |
| **Logic / RTL** | gate, register, FSM |
| Electrical / Layout / Semiconductor | transistor, 물리 배치, 공정 |

이 과목은 주로 **architecture · digital system · RTL/logic** level.

## 3. FPGA란

**FPGA (Field-Programmable Gate Array)**: 사용자가 원하는 회로로 재구성 가능한 IC.

구성: **CLB**(Configurable Logic Block) · **LUT**(Look-Up Table) · **FF**(Flip-Flop) · **IOB**(I/O Block) · routing fabric.

- hard-wired ASIC과 달리 **재프로그램 가능**. 병렬 하드웨어를 직접 구성 → 특정 workload에서 CPU/GPU보다 빠르고 효율적일 수 있다.
- 쓰는 이유: 설계 변경 쉬움, ASIC보다 초기 비용↓, dedicated 병렬 연산, 검증·교육 적합, **AI accelerator prototype 빠르게 구현**.
- 도구: HDL = Verilog/VHDL, board = Xilinx Arty A7/S7·PYNQ-Z2, tool = **Vivado**.

## 4. Vivado 설치·프로젝트 생성

- Vivado 2021.1 계열 (Vivado ML Standard), Spartan-7 support, cable drivers. AMD/Xilinx 계정(대학 이메일) 필요.
- **설치 경로·프로젝트 경로에 한글/공백 금지**.
- 새 프로젝트: Create Project → RTL Project → "Do not specify sources at this time" → Boards 탭에서 target board.
- board가 안 보이면: Digilent board file(`Arty-s7-50.zip`) 압축 해제 → `data/boards/board_files` 아래 복사 → TCL console에서 board repository path 지정.

## 5. Vivado Design Flow

| 단계 | 의미 |
|---|---|
| **Simulation** | testbench로 RTL **기능** 검증 |
| **Synthesis** | RTL → gate-level netlist. 이후 실제 hardware resource·timing 문제가 드러남 |
| **Implementation** | target FPGA에 맞게 placement / routing |
| **Generate Bitstream** | FPGA programming용 `.bit` 생성 (있어야 board에 올림) |

## 6. Source 종류

- **Design sources**: 합성 가능한 Verilog RTL
- **Simulation sources**: testbench (합성 대상 아님)
- **Constraints (`.xdc`)**: pin mapping, clock constraint

### 예: 2-2 AOI
$$
Q = \overline{AB + CD}
$$
```verilog
assign Q = ~((A & B) | (C & D));
```
- `module` = input/output port를 가진 하드웨어 component.
- `assign` = combinational logic을 wire로 연결.
- **HDL은 순차 프로그램이 아니라 회로 구조를 기술**한다.

## 7. Testbench & Simulation

testbench 역할: DUT(Design Under Test) instance화 → 입력 stimulus → 출력 관찰 → (필요시) checker.
절차: Add Sources → simulation sources → testbench 작성(DUT instance + 입력 시나리오) → Run Behavioral Simulation → waveform 확인.
- testbench 입력은 보통 `reg`, DUT가 drive하는 출력은 `wire`로 받음.
- 예상 truth table과 waveform 일치 확인.

## 복습 질문

- ISA/architecture/RTL 추상화 수준에서 이 과목이 다루는 범위는?
- FPGA의 CLB/LUT/FF/IOB 각각의 역할과, ASIC 대비 장단점은?
- Vivado design flow 4단계와, 각 단계에서 새로 드러나는 문제는?
- design source / simulation source / constraint의 차이는?
{% endraw %}

---

다음: [02. Verilog와 FSM](02-verilog-and-fsm.md)
