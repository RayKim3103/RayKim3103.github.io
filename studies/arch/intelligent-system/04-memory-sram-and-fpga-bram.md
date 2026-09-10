---
layout: page
title: "04. 메모리 — SRAM & FPGA BRAM"
permalink: /studies/arch/intelligent-system/04-memory-sram-and-fpga-bram/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `05(SRAM & Memory Controller)` + `06(FPGA BRAM & DPRAM Wrapper)` 통합·보강
- **practice 설정·수치는 원문 그대로**입니다.

{% raw %}
## 개요

RAM 기본, **6T SRAM cell**과 read/write, controller FSM에서의 **read latency**, 그리고 FPGA 내부 **Block RAM(BRAM)** IP 생성과 dual-port wrapper.

---

# Part 1. SRAM

## 1. RAM 종류

| | 특징 |
|---|---|
| **SRAM** | 전원 유지 + 재기록 전까지 데이터 유지, refresh 불필요, 빠름, cell 큼 |
| **DRAM** | leakage로 수 ms마다 refresh 필요, 밀도 높음 |
| **NVRAM/EEPROM** | 전원 꺼져도 유지 |

## 2. SRAM 내부

array = 행/열 구조. **address → decoder → wordline(WL, row 선택)**, **bitline(BL/BLB, column data path)**.

- **12T SRAM**: latch + bitline, 교육용, 면적 큼.
- **6T SRAM**: cross-coupled inverter 2개(1 bit 저장) + access transistor 2개(BL/BLB 연결). array 면적의 대부분이 cell → layout scaling 중요.

### 6T Read
1. BL, BLB를 VDD로 **precharge** → floating
2. WL↑ → cell을 bitline에 연결
3. 저장값에 따라 한쪽 bitline이 내려감
4. **sense amplifier**가 차이 감지
→ read 중 내부 node가 뒤집히면 안 됨 → **read stability**를 위한 transistor sizing.

### 6T Write
1. BL/BLB에 서로 보수인 data를 **강하게 drive**
2. WL↑
3. bitline driver가 기존 latch 값을 이기고 새 값 저장
→ **writeability**: write driver/access transistor가 feedback inverter를 이겨야. read stability ↔ writeability는 sizing trade-off.

### Decoder
N-bit address → $$2^N$$ WL 중 하나. $$2^N$$개의 N-input gate가 필요 → N이 크면 큰 NAND/AND가 느려 **다단계 작은 gate**로 분해.

## 3. Verilog SRAM model (simulation용)

```verilog
reg [BW-1:0] mem [0:AMAX-1];   // BW: data bitwidth, AMAX: depth
```
| 신호 | 역할 |
|---|---|
| `clk` | 동기화 |
| `en` | read/write 활성화 |
| `we` | write enable (high=write) |
| `addr` | 주소 |
| `din` / `dout` | write / read data |

- write: `en=1, we=1, addr·din 유효` → `tWRITE` 후 저장 완료.
- read: `en=1, we=0, addr 유효` → `tREAD` 후 `dout` 제공.
- **read latency를 모르면 controller FSM이 한 cycle 빠르거나 늦는 버그**가 난다.
- 이 register-array 방식은 simulation엔 편하지만 FPGA에선 resource 낭비 → 실제론 BRAM.

## 4. Practice — SRAM Controller

두 SRAM 사이 data 이동 controller.
- SRAM1: **16-bit × 256**, SRAM2: **32-bit × 192**
- **Mapping 1** (SRAM1 `0x00–0x7F`): 16-bit 두 개를 32-bit word로 concat (lower address → MSB), SRAM2 `0x60–0xBF`에 reversed order 저장.
- **Mapping 2** (SRAM1 `0x80–0xFF`): 16-bit + 16-bit zero concat (even address → LSB, odd address → MSB), SRAM2 `0x00–0x5F` 저장.
- 설계: top module instance 이름 유지, `start`는 single clock, 완료 시 `done` high. SRAM1 read data를 SRAM2 write timing에 맞게 잡는 register 필요. address mapping·data packing을 FSM state별로 분리.
- testbench: `$readmemh`로 SRAM1 초기화(경로 수정), 완료 후 SRAM2를 solution hex와 비교, 오류 시 `$writememh` dump. (`$readmemh`는 simulation용 — 실제 FPGA BRAM 초기화는 COE/MEM 파일.)

---

# Part 2. FPGA BRAM & DPRAM

## 5. Block RAM

FPGA fabric 안에 물리적으로 구현된 on-chip RAM. bus 없이 native port 접근, 보통 **18 Kb / 36 Kb unit**, dual-port 구성 가능. 큰 memory는 LUT/FF 대신 BRAM.

## 6. Vivado BRAM Generator 옵션

| 항목 | 선택 |
|---|---|
| Interface | Native / AXI |
| Memory Type | Single Port / **Simple Dual Port** / True Dual Port / ROM |
| Byte Write Enable | byte 단위 write mask |
| Operating Mode | Write First / Read First / No Change |
| Output Register | timing 개선용 (latency↑) |

| 타입 | port 특성 |
|---|---|
| Single Port | 한 port에서 read/write |
| **Simple Dual Port** | 한 port write, 다른 port read (FIFO·동시 R/W에 적합) |
| True Dual Port | 두 port 모두 R/W |

- **Byte Write Enable** 예: 기존 `0x00000000`, write `0x11111111`, `we=4'b1100` → 결과 `0x11110000`. byte ordering 확인.

### Operating Mode (같은 address 동시 R/W)
| | 동작 | 적합 |
|---|---|---|
| **Write First** | 새로 쓴 data가 즉시 output | pipeline register처럼 |
| **Read First** | 이전 data가 output | read-modify-write |
| **No Change** | output이 이전 값 유지 | glitch 방지 |

### Output Register & Timing
BRAM `tREAD` + combinational `tCOMB` → setup margin 부족 → violation. 해결: primitive/core output register, long routing path에 fetch FF. 대신 **read latency↑ → controller FSM에 반영**.

## 7. Memory Initialization / OOC

- board 구현: Vivado BRAM IP의 initialization file. COE 예:
```text
memory_initialization_radix=16;
memory_initialization_vector=
0001,
0001,
...
;
```
- **Global** synthesis(기본) vs **OOC per IP**(IP를 별도 합성, top에선 black box) — OOC는 큰 프로젝트·재사용 IP에서 합성 시간↓.

## 8. Practice — DPRAM Memory Wrapper

- config: Native, Simple Dual Port, No ECC, No Byte WE. Port A: **width 16 / depth 256 / Read First / Enable Port**. Port B: **width 16 / Primitive Output Register**. init: `initialize_memory.coe`.
```text
Dual Port RAM
Port B: 순차 read → Accumulation register → Port A: 누적값을 연속 address에 write
```
- 초기 RAM 모든 address = `0x0001`. 예상 결과: addr 0→`0x0001`, addr 1→`0x0002`, …, addr 255→`0x0100`.
- testbench: reset 후 `start`, 모든 address rewrite 시 `done` high, 이후 testbench가 RAM control을 가져가 read·비교. **read latency 2 clock cycles** 고려. (BRAM은 `$writememh` debug가 어려워 testbench가 address별 read로 검증.)

## 자주 틀리는 지점

- **read latency를 controller FSM에 반영** (SRAM `tREAD`, BRAM output register 시 2 cycle).
- 16→32-bit packing 시 MSB/LSB 위치 혼동.
- `$readmemh`(simulation) ↔ COE initialization(board) 구분.
- `done` 이후 memory control ownership(testbench로 넘어감) 고려.
- single-port BRAM은 동시 R/W collision.

## 복습 질문

- 6T SRAM의 read stability와 writeability가 sizing에서 trade-off인 이유는?
- Write First / Read First / No Change 모드의 차이와 각 용도는?
- output register를 쓰면 timing과 latency가 각각 어떻게 되는가?
- DPRAM practice에서 read latency 2 cycle을 FSM에 어떻게 반영하는가?
{% endraw %}

---

이전: [03. 과제 1 — Vending Machine](03-assignment-1-vending-machine.md) · 다음: [05. FIFO와 Line Buffer](05-fifo-and-line-buffer.md)
