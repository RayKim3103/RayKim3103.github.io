---
layout: page
title: "05. FIFO와 Line Buffer"
permalink: /studies/arch/intelligent-system/05-fifo-and-line-buffer/
sitemap: false
---

- **원본**: [GitHub — Intelligent System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Intelligent_System) · 강의 노트 `07` 정리·보강
- **practice 설정·수치는 원문 그대로**입니다.

{% raw %}
## 개요

**FIFO** = First-In-First-Out queue/buffer. producer/consumer 속도 차이를 완충하고 core 간 pipeline을 decouple한다. **line buffer**는 raster-scan stream을 2D convolution용 row window로 재정렬한다.

## 1. FIFO 개념

- data를 들어온 순서대로 저장·read (**sequential access**, random access 아님).
- **AI hardware에서 필요한 이유**: FIFO 없이 여러 core를 pipeline으로 연결하면 모든 core가 동기적으로 같은 속도로 동작해야 하고 throughput이 가장 느린 stage에 묶임. FIFO를 넣으면 core들이 독립 동작, intermediate data buffering, 속도 차이 흡수 → throughput↑. (Google TPU, MIT Eyeriss에서도 dataflow·buffering이 핵심.)

## 2. Pointer 기반 FIFO

| 신호 | 의미 |
|---|---|
| `full` / `empty` | 가득 참 / 빔 |
| `wr_en` / `rd_en` | write / read enable |
| `din` / `dout` | write / read data |
| `w_ptr` / `r_ptr` | 다음 write / read 위치 |

depth $$= 2^N$$ → pointer는 **$$N+1$$ bit** (lower $$N$$ = memory address, MSB 1 bit = wrap-around 구분).

$$
\text{empty}: \quad w\_ptr == r\_ptr \quad(\text{MSB·lower bits 모두 같음})
$$
$$
\text{full}: \quad w\_ptr[N] \ne r\_ptr[N] \;\wedge\; w\_ptr[N{-}1{:}0] == r\_ptr[N{-}1{:}0]
$$
(lower address는 같지만 wrap bit가 다르면 한 바퀴 차이 = 가득 참.)

### Write / Read
- `wr_en & !full` → `w_ptr` 위치에 `din` 저장, clock edge에 `w_ptr++`. full이면 write 차단(**overflow 방지**).
- `rd_en & !empty` → `r_ptr` 위치 data를 `dout`, clock edge에 `r_ptr++`. empty이면 read 차단(**underflow 방지**).

## 3. Memory 선택

작은 depth → register file 가능. 큰 depth → **simple dual-port BRAM** 권장(single-port는 simultaneous R/W collision). BRAM read latency ≥ 2 cycle이면 read pointer control이 복잡해짐.

## 4. Line Buffer

이미지는 **raster-scan order**로 저장:
```text
(0,0), (1,0), ..., (X-1,0),   (0,1), (1,1), ..., (X-1,1),   ...
```
3×3 convolution은 같은 시점에 **3개 row의 pixel**이 필요 → line buffer가 연속 입력 stream을 여러 row window로 재정렬해 multiplier에 병렬 공급.

$$
\text{3×3 filter} \Rightarrow \text{output pixel마다 9 MAC operations}
$$
효과: 3 row 병렬 read, memory bandwidth 부담 완화, multiplier workload 균형화, **streaming image processing**.

## 5. Practice — Line Buffer

- **3개 FIFO**로 line buffer, 각 FIFO **8 × 8**, 8-bit input data.
- random data rate로 write 가능. `ready = 0`일 때만 저장.
- **모든 FIFO full → `ready = 1`** (data access 가능). access 중 3개 FIFO 병렬 pop → 총 **24-bit output**. 모든 FIFO empty → `ready = 0`.
- BRAM config (FIFO용 simple dual-port): Native, Simple Dual Port, No ECC, No Byte WE. Port A: **width 8 / depth 8 / Read First / Enable Port**. Port B: **width 8 / Enable Port**. **No output register**.
- output concat 순서 (testbench expectation과 일치해야):
```verilog
data_out = {from_FIFO2, from_FIFO1, from_FIFO0};
```
- testbench: random data rate write request(clock edge에서 약간 delay된 1-clock pulse), 입력 memory random 초기화, `ready` 감지 후 output 확인. `mem_to_DUT.hex` / `mem_from_DUT.hex`.

## 자주 틀리는 지점

- full/empty는 **$$N+1$$ bit pointer**로 안정적으로 구분.
- overflow/underflow 방지 logic 필수.
- output concatenate **순서**와 BRAM latency를 testbench 기준에 맞춤.

## 복습 질문

- $$N+1$$ bit pointer로 full과 empty를 구분하는 조건식은?
- AI accelerator pipeline에서 FIFO가 없으면 무엇이 문제이고, FIFO가 어떻게 해결하는가?
- line buffer가 raster-scan stream을 어떻게 3×3 convolution에 맞게 재정렬하는가?
- FIFO 내부 memory로 single-port BRAM이 부적합한 이유는?
{% endraw %}

---

이전: [04. 메모리 — SRAM & FPGA BRAM](04-memory-sram-and-fpga-bram.md) · 다음: [06. UART와 Memory Loopback](06-uart-and-memory-loopback.md)
