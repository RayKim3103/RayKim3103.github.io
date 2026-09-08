---
layout: page
title: "07. PS/PL · AXI · PYNQ · ILA"
permalink: /studies/arch/intelligent-system/07-ps-pl-axi-pynq-and-ila/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `09` 정리·보강
- **register map·practice·block design 절차는 원문 그대로**입니다.

{% raw %}
## 개요

FPGA 단독 RTL에서 **ZYNQ 기반 PS/PL system**으로 확장. software(PS)가 **AXI**로 custom hardware(PL)를 **memory-mapped control/status register**로 제어하고, **ILA**로 board 내부 신호를 관측, **PYNQ**로 Python에서 overlay를 제어한다.

## 1. PS와 PL

| 블록 | 역할 |
|---|---|
| **PS** (Processing System) | ARM processor, software 제어 |
| **PL** (Programmable Logic) | Verilog/VHDL custom hardware |
| Interconnect | PS↔PL data/control (AXI) |

PS가 control register write/start → PL이 실제 연산.

## 2. AXI Protocol

**AXI** (Advanced eXtensible Interface, ARM AMBA). 특징: memory-mapped transaction, **address phase와 data phase 분리**, read/write channel 분리, **ready/valid handshake**, synchronized clock.

| Channel | 역할 |
|---|---|
| AR / R | Read Address / Read Data |
| AW / W / B | Write Address / Write Data / Write Response |

### Ready/Valid Handshake
$$
\text{transfer} = \text{valid} \;\wedge\; \text{ready}
$$
sender `valid`와 receiver `ready`가 동시에 1일 때 전송 → producer/consumer 속도 차이 흡수, channel별 독립 동작.

## 3. Control/Status Register (CSR)

PS가 PL 내부 wire를 직접 못 건드리므로 register interface.

| register | 방향 | 예 |
|---|---|---|
| **control** | PS → PL | mode, `start`, stride, channel 수 |
| **status** | PL → PS | `done`, `busy`, `error` |

### Memory-mapped 예 (calculator)
1. operand0 → `0x0000` write
2. operand1 → `0x0004` write
3. operator → `0x1000` write
4. `start` → `0x1004`에 1 write 후 0 복귀
5. `done` → `0x1008` polling
6. output → `0x0008` read

AXI address는 **byte address** → 32-bit word는 주소가 4씩 증가.

### Accelerator register map 예
`core_start`, `core_status`, `input_ch`, `output_ch`, `tile_width`, `tile_height`, `stride`, `shamt` — register 효율 ↔ software 편의 사이에서 bit packing 선택.

## 4. ILA (Integrated Logic Analyzer)

Vivado on-chip debugging IP. 필요한 이유: simulation testbench는 실제 board behavior를 완전히 반영 못 하고, FPGA 내부 신호는 외부 pin으로 못 봄.

**동작**: 내부 signal을 probe로 연결 → trigger condition 설정 → FPGA 내부 BRAM **ring buffer**에 waveform 저장 → **JTAG**으로 host Vivado Hardware Manager에서 확인. ring buffer는 trigger index 기준 앞뒤 cycle 보존.

용도 예: `start`가 single-cycle pulse인지, `done` timing, SRAM/BRAM enable·we·addr·data 동작 확인.

## 5. PYNQ

AMD Xilinx platform용 **Jupyter 기반 framework**. PS에서 PYNQ-Linux 실행, Python API로 PL overlay 제어 (bitstream + hardware handoff file 사용) → FPGA overlay를 Python library처럼.

**PYNQ-Z2 자원**: ARM Cortex-A9 dual-core (ARMv7 32-bit), PL slices, BRAM, DSP slices, DDR3, MicroSD/USB/Ethernet.

## 6. Practice — PYNQ Driver for SRAM Controller

```text
PYNQ Python → AXI → Control/Status Register → SRAM Controller
PYNQ Python → AXI → SRAM1/SRAM2 memory
```
Python driver: `__init__`에서 SRAM1·SRAM2·CSR 객체 선언 → input data를 SRAM1에 저장 → control register로 `start` → status register `done` polling → SRAM2에서 output load → answer array와 비교.

### CSR 설계 요구
- Python register write는 **수백 cycle 동안 유지**될 수 있음 → PL의 `start`는 **single-cycle pulse**가 필요.
- start control register는 activation 직후 **0으로 restore** (malfunction 방지). `done`은 status register에 반영.

## 7. IP Packaging & Block Design

**IP packaging**: PL synthesis로 RTL 검증 → top module을 IP로 package → CSR을 AXI peripheral로 package → IP repository path 등록 → block design에 추가.

**Block design 흐름**: PYNQ-Z2 project 생성 → IP repo 등록 → ZYNQ PS 추가 → custom top 추가 → CSR IP 추가 → **AXI BRAM controller 2개** 추가 → **AXI SmartConnect** 추가 → PS/CSR/BRAM/custom top 연결 → address editor에서 range 설정 → **ILA IP** 추가·probe 연결 → Validate Design → HDL wrapper 생성 → bitstream 생성.

### Address / Data Width 조정
AXI BRAM controller는 **byte address**, BRAM native는 **word index**일 수 있음 → 예: 32-bit AXI data는 주소 4씩, BRAM address는 1씩 → **LSB 제거 또는 slice IP**로 address bit 조정. data width가 다르면 **slice/concat IP**.

### Bitstream Upload
PYNQ overlay에 필요: `.bit`(bitstream) + `.hwh`(hardware handoff). **두 파일 이름 동일**, Jupyter에 upload.

## 자주 틀리는 지점

- PS write는 오래 유지 → PL `start`는 **single-cycle pulse로 변환**하고 즉시 0 복귀.
- AXI address는 **byte address** (word당 +4).
- BRAM latency를 controller state에 반영.
- ILA는 board에서만 드러나는 timing/control 문제를 잡는 필수 도구.

## 복습 질문

- PS와 PL의 역할 분담, 그리고 AXI의 ready/valid handshake 조건은?
- control register와 status register의 방향·용도, memory-mapped 제어 순서는?
- PS write가 수백 cycle 유지될 때 PL의 `start`를 어떻게 처리해야 하는가?
- AXI BRAM controller의 byte address ↔ BRAM word address 불일치를 어떻게 맞추는가?
{% endraw %}

---

이전: [06. UART와 Memory Loopback](06-uart-and-memory-loopback.md) · 다음: [08. Computer Vision과 CNN](08-computer-vision-and-cnn.md)
