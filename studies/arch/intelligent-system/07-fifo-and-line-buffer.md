---
layout: page
title: "07. FIFO and Line Buffer"
permalink: /studies/arch/intelligent-system/07-fifo-and-line-buffer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Intelligent_System/lecture_notes/07%20FIFO%20and%20Line%20Buffer.md)

{% raw %}
﻿---
title: "07. FIFO and Line Buffer"
pages: 35
tags: [intelligent-system, lecture-note, FIFO, line-buffer, CNN, image-processing]
---

# 07. FIFO and Line Buffer

> 이전: [FPGA BRAM and DPRAM Wrapper](06-fpga-bram-and-dpram-wrapper.md)
> 다음: [UART and Memory Loopback](08-uart-and-memory-loopback.md)

## 학습 목표

Week6-1 자료는 FIFO의 원리와 AI hardware에서의 역할, full/empty pointer 설계, 그리고 2D convolution을 위한 line buffer practice를 다룬다.

## FIFO란

FIFO는 First In First Out 구조의 queue 또는 memory buffer이다.

특징:

- data를 들어온 순서대로 저장한다.
- read도 저장된 순서대로 수행한다.
- random access가 아니라 sequential access이다.
- producer와 consumer 사이 속도 차이를 완충한다.

## AI Hardware에서 FIFO가 필요한 이유

여러 core를 pipeline으로 연결할 때 FIFO가 없으면 모든 core가 같은 속도로 동기적으로 동작해야 한다.

문제:

- 앞 core가 끝날 때까지 뒤 core가 기다림
- 전체 throughput이 가장 느린 stage에 묶임
- core 간 timing coupling이 강함

FIFO를 넣으면:

- core들이 독립적으로 동작 가능
- intermediate data를 buffer에 저장
- producer/consumer 속도 차이 흡수
- AI accelerator pipeline throughput 향상

Google TPU, MIT Eyeriss 같은 AI accelerator에서도 dataflow와 buffering은 핵심 설계 요소이다.

## FIFO 주요 I/O

| 신호 | 의미 |
|---|---|
| `full` | FIFO가 가득 차서 더 쓸 수 없음 |
| `empty` | FIFO가 비어 있어 읽을 data 없음 |
| `wr_en` | write enable |
| `rd_en` | read enable |
| `din` | write data |
| `dout` | read data |
| `w_ptr` | 다음 write 위치 |
| `r_ptr` | 다음 read 위치 |

## Pointer 기반 FIFO

depth가 $2^N$이면 pointer는 보통 $N+1$ bit를 사용한다.

- lower N bits: memory address
- MSB 1 bit: wrap-around 구분

empty 조건:

```text
w_ptr == r_ptr
```

즉 MSB와 lower address bits가 모두 같다.

full 조건:

```text
w_ptr[N] != r_ptr[N] &&
w_ptr[N-1:0] == r_ptr[N-1:0]
```

즉 lower address는 같지만 wrap bit가 다르면 한 바퀴 차이로 가득 찬 상태이다.

## Write Operation

`wr_en`이 assert되고 `full`이 아니면:

1. 현재 `w_ptr` 위치에 `din` 저장
2. clock edge에서 `w_ptr` 증가

full이면 write를 막아 overflow를 방지해야 한다.

## Read Operation

`rd_en`이 assert되고 `empty`가 아니면:

1. 현재 `r_ptr` 위치 data를 `dout`으로 제공
2. clock edge에서 `r_ptr` 증가

empty이면 read를 막아 invalid data를 읽지 않도록 해야 한다.

## Memory 선택

FIFO 내부 memory는 register file 또는 dual-port BRAM으로 구현할 수 있다.

권장:

- 작은 depth: register file 가능
- 큰 depth: simple dual-port BRAM 권장

주의:

- single port BRAM은 FIFO의 simultaneous read/write에 collision을 만들 수 있다.
- BRAM read latency가 2 cycles 이상이면 read pointer control이 더 복잡해진다.

## Line Buffer가 필요한 이유

이미지는 raster scan order로 memory에 저장된다.

```text
(0,0), (1,0), ..., (X-1,0),
(0,1), (1,1), ..., (X-1,1),
...
```

하지만 3x3 convolution을 수행하려면 같은 시점에 3개 row의 pixel이 필요하다. line buffer는 연속 입력 stream을 여러 row window로 재정렬해 multiplier에 병렬 공급한다.

## 2D Convolution과 Line Buffer

3x3 filter는 매 output pixel마다 9개 input pixel을 곱하고 더한다.

```text
3 rows x 3 columns -> 9 MAC operations
```

line buffer는 row 단위 data를 저장해 다음을 가능하게 한다.

- 3개 row를 병렬로 읽기
- memory bandwidth 부담 완화
- multiplier workload 균형화
- streaming image processing

## Practice: Line Buffer

요구사항:

- 3개의 FIFO로 line buffer 구성
- 각 FIFO 크기: 8 x 8
- 8-bit input data
- random data rate로 write 가능
- `ready = 0`일 때만 data 저장 가능
- 모든 FIFO가 full이면 `ready = 1`
- `ready = 1`일 때 data access 가능
- access 중 3개 FIFO가 병렬 pop되어 총 24-bit output
- 모든 FIFO가 empty이면 `ready = 0`

## BRAM 설정

Line buffer용 FIFO는 simple dual-port RAM 기반으로 구현한다.

설정:

- Native interface
- Simple Dual Port
- No ECC
- No Byte Write Enable
- Port A: width 8, depth 8, Read First, Enable Port
- Port B: width 8, Enable Port
- No primitive/core output register

## Output Concatenation

자료는 output을 다음 순서로 concatenate하라고 안내한다.

```verilog
data_out = {from_FIFO2, from_FIFO1, from_FIFO0};
```

순서가 testbench expectation과 맞아야 하므로 중요하다.

## Testbench 포인트

- random data rate로 write request 생성
- write request는 clock edge에서 약간 delay된 1-clock pulse로 생성
- 입력 memory는 random 초기화
- `ready` 감지 후 output 확인
- `mem_to_DUT.hex`: DUT에 쓴 data
- `mem_from_DUT.hex`: DUT에서 읽은 data

## 체크포인트

- FIFO full/empty는 N+1 bit pointer로 안정적으로 구분한다.
- overflow/underflow 방지 logic이 반드시 필요하다.
- AI hardware에서 FIFO는 core 간 decoupling과 pipeline throughput 향상에 핵심이다.
- line buffer는 2D convolution을 streaming hardware로 만들기 위한 기본 구조이다.
- output concatenate 순서와 BRAM latency를 testbench 기준에 맞춘다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. FIFO and Line Buffer**를 다루며, FPGA/SoC 위에서 디지털 회로, 메모리, AXI, 영상/AI 하드웨어를 구현하는 흐름을 익힌다.
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
- **07. FIFO and Line Buffer**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. FPGA BRAM and DPRAM Wrapper](06-fpga-bram-and-dpram-wrapper.md) · 다음: [08. UART and Memory Loopback](08-uart-and-memory-loopback.md)
