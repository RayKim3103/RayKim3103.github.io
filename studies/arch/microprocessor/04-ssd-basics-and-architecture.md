---
layout: page
title: "04. SSD — 기본 개념과 내부 아키텍처"
permalink: /studies/arch/microprocessor/04-ssd-basics-and-architecture/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · `06 SSD Overview 1 — Solid State Disk Basics` + `07 SSD Overview 2 — SSD Architecture` **통합·보강**

{% raw %}
## 개요

SSD는 moving part가 없는 **non-volatile memory 기반 저장장치**라서 random access latency가 HDD보다 훨씬 낮다. 이 장은 SSD의 정의·특성·interface, HDD와의 차이, 성능 비교, 그리고 내부 아키텍처(host interface → SSD controller → microcontroller → internal bus → SRAM/DRAM → NAND controller → NAND chips)를 다룬다.

---

# Part 1. SSD 기본

## 1. SSD 정의

**SSD** = Solid-State Disk / Solid-State Drive. magnetic platter가 아니라 **non-volatile memory chip**(주로 NAND flash)에 data를 저장.

"**Solid-state**"의 의미:
- vacuum tube / gas-discharge tube 아님
- relay, 기계식 switch처럼 **움직이는 electro-mechanical device 아님**
- transistor, microprocessor, DRAM, flash memory처럼 **고체 소자 기반**

## 2. SSD 특성

| 장점 | 한계 |
|---|---|
| random access latency 낮음 (mechanical seek 없음) | flash cell의 **program/erase cycle 수명 제한** (endurance) |
| 충격에 강함, 무소음 | **erase-before-write** — 제자리 덮어쓰기 불가 |
| 전력 소모 낮을 수 있음 | **write amplification** & garbage collection overhead |
| 내부 parallelism으로 높은 throughput | controller·FTL 설계가 성능을 좌우 |

## 3. Interface

| Interface | 특징 |
|---|---|
| **SATA** | HDD 호환 storage interface에서 출발, 널리 보급, ~6 Gbps로 대역폭 상한 |
| **PCI Express (PCIe/NVMe)** | 훨씬 높은 bandwidth, 낮은 latency, 큐 병렬성 |
| rackmount / enterprise interface | 데이터센터용 |

고속 interface는 NAND 내부 parallelism과 controller 성능을 충분히 끌어낼 수 있어야 의미가 있다.

## 4. Hybrid SSD

DRAM + NAND flash + magnetic disk(또는 다른 계층)를 조합해 성능·비용 절충. 자주 접근하는 hot data는 빠른 media에, 대용량 cold data는 저비용 media에.

## 5. SSD vs HDD

| 항목 | SSD | HDD |
|---|---|---|
| 저장 매체 | NAND flash | magnetic platter |
| 움직이는 부품 | 없음 | spindle, head arm |
| Random access | 빠름 (전기적 접근) | seek + rotational latency 필요 |
| Sequential access | interface·내부 channel parallelism에 의존 | platter transfer rate에 의존 |
| 내구성 | write endurance 관리 필요 | mechanical failure 가능 |

### 성능 비교 상세
- **Access time**: HDD는 head 이동 + rotational latency(수 ms). SSD는 cell/page 전기적 접근(수십~수백 µs).
- **Sequential R/W**: HDD도 비교적 강함. SSD는 여러 NAND channel 병렬로 높은 throughput.
- **Random read**: SSD 압도적.
- **Random write**: erase-before-write, GC, write amplification 때문에 **controller/FTL 설계에 크게 좌우**됨 ([06](06-flash-translation-layer.md)).

## 6. NAND Flash 시장 · "War of the Disks"

NAND density 증가 + 가격 하락 → SSD 보급의 핵심 배경. SSD는 latency·random I/O에서, HDD는 GB당 비용에서 강점이었고, 시간이 지나며 SSD 적용 범위가 넓어졌다.

---

# Part 2. SSD 내부 아키텍처

## 7. 전체 구조

```text
[Host] --SATA/PCIe/USB--> [Host Interface]
                               |
                       [SSD Controller] --- [Microcontroller (ARM7/ARM9)]
                               |  (internal bus: AMBA AHB)
        +----------+-----------+-----------+------------------+
     [SRAM]   [DRAM controller]      [NAND flash controller]
                    |                        |
              [DRAM cache buffer]     [NAND flash memory chips] (multi-channel)
```

Host는 SSD를 **block device**로 보지만, 내부는 **flash page read/program + block erase** 단위로 동작한다. 이 간극을 메우는 것이 FTL.

## 8. SSD Controller — SSD의 심장

역할:
- **host command 처리** (block I/O 요청 해석)
- **address translation** (LBA → physical, = FTL)
- **garbage collection**
- **wear-leveling**
- **bad block management**
- **ECC** encode/decode
- **NAND channel scheduling** (여러 channel·die 병렬)
- **cache/buffer 관리** (write coalescing, read cache)

## 9. Microcontroller

SSD **firmware를 실행하는 두뇌**. 강의 예시: **ARM7, ARM9** 급 embedded processor. internal bus로 SRAM, DRAM controller, NAND controller, host interface block을 제어.

## 10. Internal Bus

**AMBA AHB** 같은 high-performance on-chip bus 사용. 연결 대상: microcontroller, SRAM controller, DRAM controller, NAND flash controller, host interface block.

## 11. SRAM & SRAM Controller

가장 빠른 on-chip memory. 작은 mapping table, firmware working data 저장.

| 특징 | |
|---|---|
| access | 매우 빠름 |
| refresh | 불필요 |
| 면적 | 큼 (6T/cell) → 용량 제한 |

SRAM controller = bus transaction ↔ SRAM read/write 변환.

## 12. External Interface

| Interface | 특징 |
|---|---|
| **PATA** | parallel ATA, 구형 |
| **SATA** | serial ATA, HDD/SSD 공통 |
| **USB** | 범용 외장 storage |
| **PCIe** | 고속·저지연 |

## 13. DRAM Cache Buffer

DRAM 용도:
- host read/write **data buffering**
- **write coalescing** (작은 write를 모아 page 단위로)
- **mapping table cache** (page-mapped FTL의 큰 table 일부를 DRAM에)
- garbage collection 중 임시 data 저장

DRAM controller가 refresh·timing·burst transfer 관리. (DRAM-less SSD는 host memory buffer(HMB)나 SRAM만 사용.)

## 14. NAND Flash Memory & NAND Flash Controller

- **NAND flash**: 실제 non-volatile storage. page 단위 read/program, **block 단위 erase**, erase-before-write. SLC/MLC 등 cell당 bit 수로 성능·수명 차이. (상세 → [05. NAND Flash Memory](05-nand-flash-memory.md))
- **NAND flash controller**: NAND command / address / data timing 생성. page read, page program, block erase, ECC encode/decode, bad block table 관리, multiple chip/channel control.

## 15. SLC vs MLC

| 종류 | cell당 bit | 장점 | 단점 |
|---|---:|---|---|
| **SLC** | 1 bit | 빠름, endurance 높음 | bit당 비용 높음 |
| **MLC** | 2 bit+ | 저장 밀도 높음 | 속도·endurance 불리, 강한 ECC 필요 |

---

## 복습 질문

- SSD가 HDD 대비 random read에서 압도적인 이유, random write는 왜 controller/FTL에 좌우되는가?
- Host가 보는 SSD의 단위와 NAND 내부 동작 단위의 차이는? 그 간극을 메우는 것은?
- SSD controller의 7가지 역할을 나열하면?
- DRAM cache buffer가 SSD에서 하는 네 가지 역할은? SRAM과 DRAM의 역할 분담은?
{% endraw %}

---

이전: [03. Multiprocessors](03-multiprocessors.md) · 다음: [05. NAND Flash Memory](05-nand-flash-memory.md)
