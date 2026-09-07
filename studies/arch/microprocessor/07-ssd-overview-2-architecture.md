---
layout: page
title: "07. SSD Overview 2 - SSD Architecture"
permalink: /studies/arch/microprocessor/07-ssd-overview-2-architecture/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/07%20SSD%20Overview%202%20-%20SSD%20Architecture.md)

{% raw %}
tags: #micro-processor #ssd #architecture #ssd-controller #sram #dram #nand-flash #ahb

관련 노트: [SSD Overview 1 - Solid State Disk Basics](06-ssd-overview-1-solid-state-disk-basics.md), [SSD Hardware 4 - NAND Flash Memory](08-ssd-hardware-4-nand-flash-memory.md)

## 핵심 요약

이 자료는 SSD 내부 architecture를 설명한다. SSD는 host interface, SSD controller, microcontroller, internal bus, SRAM/DRAM buffer, NAND flash controller, NAND flash memory chips로 구성된다. 핵심은 SSD controller가 host의 block I/O 요청을 NAND flash의 page/block 동작으로 변환한다는 점이다.

## SSD 전체 구조

SSD는 크게 다음으로 구성된다.

- external host interface
- SSD controller
- microcontroller
- internal bus
- SRAM
- DRAM cache buffer
- NAND flash controller
- NAND flash memory chips

Host는 SSD를 block device로 보지만, 내부에서는 flash page read/program과 block erase 단위로 동작한다.

## SSD Controller

SSD controller는 SSD subsystem의 중심이다.

역할:

- host command 처리
- address translation
- garbage collection
- wear-leveling
- bad block management
- ECC
- NAND channel scheduling
- cache/buffer 관리

## Microcontroller

Microcontroller는 SSD firmware를 실행하는 두뇌 역할을 한다. 자료에서는 ARM7, ARM9 같은 embedded processor 예를 든다.

Microcontroller는 internal bus를 통해 SRAM, DRAM controller, NAND controller, host interface를 제어한다.

## Internal Bus

SSD controller 내부 bus로 AMBA AHB 같은 high-performance bus를 사용할 수 있다.

Internal bus가 연결하는 대상:

- microcontroller
- SRAM controller
- DRAM controller
- NAND flash controller
- host interface block

## SRAM과 SRAM Controller

SRAM은 가장 빠른 on-chip memory로, 작은 table이나 firmware working data를 저장하는 데 쓰인다.

특징:

- 빠른 access
- refresh 불필요
- 면적이 큼
- 용량은 제한적

SRAM controller는 bus transaction을 SRAM read/write로 변환한다.

## External Interface

SSD는 host와 ATA, SATA, USB, PCIe 같은 interface로 통신한다.

| Interface | 특징 |
|---|---|
| PATA | parallel ATA, 구형 |
| SATA | serial ATA, HDD/SSD에서 널리 사용 |
| USB | 범용 외부 storage |
| PCIe | 고속, 낮은 latency |

고속 interface는 NAND 내부 parallelism과 controller 성능을 충분히 끌어낼 수 있어야 한다.

## Cache Buffer와 DRAM

DRAM은 cache buffer와 mapping table 저장에 사용된다.

역할:

- host read/write data buffering
- write coalescing
- mapping table cache
- garbage collection 중 임시 data 저장

DRAM controller는 refresh, timing, burst transfer 등을 관리한다.

## NAND Flash Memory

NAND flash는 SSD의 실제 non-volatile storage이다.

특징:

- page 단위 read/program
- block 단위 erase
- erase-before-write
- SLC, MLC 등 cell당 저장 bit 수에 따라 성능/수명 차이

## NAND Flash Controller

NAND controller는 NAND command, address, data timing을 생성한다.

주요 기능:

- page read
- page program
- block erase
- ECC encode/decode
- bad block table 관리
- multiple chip/channel control

## SLC와 MLC

| 종류 | Cell당 bit | 장점 | 단점 |
|---|---:|---|---|
| SLC | 1 bit | 빠르고 endurance 높음 | bit당 비용 높음 |
| MLC | 2 bit 이상 | 저장 밀도 높음 | 속도와 endurance 불리 |

## 시험ㆍ복습 체크포인트

- SSD controller가 host block I/O와 NAND flash operation 사이에서 하는 일을 설명할 수 있어야 한다.
- SRAM과 DRAM이 SSD controller 안에서 각각 왜 필요한지 말할 수 있어야 한다.
- NAND flash의 page/program과 block/erase 단위 차이를 이해해야 한다.
- SLC와 MLC의 성능/수명/비용 tradeoff를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. SSD Overview 2 - SSD Architecture**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 저장장치 주제에서는 logical 주소와 physical 위치가 언제 어떻게 mapping되는지 추적한다.
- FTL/파일시스템 계층은 성능뿐 아니라 crash consistency와 metadata 복구 경로가 중요하다.
- processor 관점에서는 register, instruction encoding, addressing mode, exception 흐름을 함께 보아야 한다.
- SSD 관점에서는 NAND flash의 물리 제약이 FTL, mapping table, wear leveling, garbage collection 설계를 만든다.
- hardware와 software 경계에서 latency hiding, buffering, metadata consistency가 핵심 설계 문제가 된다.

### 문제 풀이 또는 구현 루틴

- assembly를 읽을 때는 각 instruction이 register와 memory 중 무엇을 바꾸는지 한 줄씩 주석으로 적는다.
- I/O 구조는 request path, queue, interrupt, DMA, completion 순서로 추적한다.
- FTL 함수는 logical page, physical page, block, superblock metadata가 언제 갱신되는지 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- NAND flash는 overwrite가 불가능하므로 HDD처럼 제자리 갱신한다고 생각하면 안 된다.
- 성능 문제에서 CPU 계산보다 I/O latency와 queueing이 병목일 수 있다.
- metadata update 순서를 잘못 잡으면 전원 장애 상황에서 mapping consistency가 깨진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 동작은 processor core 내부 문제인가, memory/I/O subsystem 문제인가?
- 상태가 바뀌는 metadata는 어디에 있고 crash 후 어떻게 복구되는가?
- latency를 줄이는 방법과 throughput을 높이는 방법이 어떻게 다른가?
- **07. SSD Overview 2 - SSD Architecture**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. SSD Overview 1 - Solid State Disk Basics](06-ssd-overview-1-solid-state-disk-basics.md) · 다음: [08. SSD Hardware 4 - NAND Flash Memory](08-ssd-hardware-4-nand-flash-memory.md)
