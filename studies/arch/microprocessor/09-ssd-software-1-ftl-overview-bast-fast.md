---
layout: page
title: "09. SSD Software 1 - FTL Overview, BAST, FAST"
permalink: /studies/arch/microprocessor/09-ssd-software-1-ftl-overview-bast-fast/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/09%20SSD%20Software%201%20-%20FTL%20Overview%20BAST%20FAST.md)

{% raw %}
tags: #micro-processor #ssd #ftl #address-translation #garbage-collection #bast #fast

관련 노트: [SSD Hardware 4 - NAND Flash Memory](08-ssd-hardware-4-nand-flash-memory.md), [SSD Software 2 - Superblock, LAST, FTL Functions](10-ssd-software-2-superblock-last-and-ftl-functions.md)

## 핵심 요약

이 자료는 SSD software의 핵심인 Flash Translation Layer를 설명한다. FTL은 host가 보는 logical block interface를 NAND flash의 physical page/block operation으로 변환한다. Address translation, garbage collection, bad block management, wear-leveling을 다루고, block-mapped FTL의 대표 방식인 BAST와 FAST를 비교한다.

## FTL의 역할

Flash Translation Layer는 host의 LBA(Logical Block Address)를 NAND flash의 physical address로 변환한다.

FTL이 필요한 이유:

- NAND는 erase-before-write 특성을 가진다.
- Program은 page 단위, erase는 block 단위이다.
- Bad block이 존재한다.
- Cell endurance가 제한되어 wear-leveling이 필요하다.
- Legacy file system은 HDD와 같은 block device interface를 기대한다.

## FTL과 File System

Legacy file system은 logical sector를 overwrite할 수 있다고 가정한다. FTL은 실제 flash에서는 새 physical page에 out-of-place update를 수행하고 mapping table을 갱신한다.

```text
File system LBA -> FTL mapping -> physical flash page/block
```

## Address Translation

### Page-Mapped FTL

Page 단위 mapping을 사용한다.

장점:

- update flexibility가 높다.
- random write 처리에 강하다.
- garbage collection 비용을 줄일 수 있다.

단점:

- mapping table이 매우 크다.

예를 들어 64 GB SSD에서 4 KB page마다 4 B mapping entry를 두면 table 크기가 커진다.

### Block-Mapped FTL

Block 단위 mapping을 사용한다.

장점:

- mapping table이 작다.
- SRAM/DRAM 요구량이 줄어든다.

단점:

- page-level update flexibility가 낮다.
- random write에서 merge 비용이 커질 수 있다.

자료에서는 block-mapped FTL을 중심으로 다룬다.

## Garbage Collection

Out-of-place update가 반복되면 invalid page가 쌓이고 free block이 부족해진다. Garbage collection은 victim block을 선택하고 valid page를 복사한 뒤 block을 erase해 free block으로 만든다.

GC 비용은 FTL 성능에 큰 영향을 준다.

## Merge Operation

Block-mapped FTL에서는 data block과 log block을 합치는 merge가 중요하다.

| Merge | 비용 | 설명 |
|---|---|---|
| Switch merge | 가장 빠름 | log block이 새 data block으로 바로 전환 가능 |
| Partial merge | 중간 | 일부 valid page만 복사 |
| Full merge | 가장 느림 | data block과 log block 모두에서 valid page를 모아 새 block 생성 |

Full merge는 erase와 copy가 많아 성능 저하가 크다.

## Bad Block Management

FTL은 어떤 block이 bad block인지 파악하고, bad block을 mapping 대상에서 제외해야 한다. Factory bad block과 runtime bad block 모두 관리 대상이다.

## Wear-Leveling

Flash cell은 program/erase 횟수 수명이 제한되어 있다. 특정 logical range만 자주 write되면 일부 block이 빨리 마모된다.

Wear-leveling은 erase count가 고르게 분포되도록 physical block을 재배치한다.

## BAST

BAST는 Block-level Associative Sector Translation이다.

특징:

- block-mapped FTL
- 각 data block에 log block을 associatively 연결
- incoming LBA의 LSB를 page offset으로 사용
- MSB를 block-level mapping table index로 사용
- switch merge와 full merge를 지원

### BAST의 문제

Random update가 여러 data block에 흩어지면 log block이 부족해지고 full merge가 자주 발생한다. Thrashing과 frequent full merge가 성능 문제이다.

## FAST

FAST는 Fully Associative Sector Translation이다.

목표:

- BAST의 log block utilization 문제 완화
- switch/partial merge 기회 증가
- log block을 더 유연하게 사용

FAST는 sequential log block과 random log block을 구분한다.

### Sequential Log Block

Sequential write pattern이 조건을 만족하면 switch merge가 가능하다. 조건이 부족하면 partial merge가 필요할 수 있다.

### Random Log Block

Random update를 fully associative하게 수용한다. 하지만 random log block merge 시 여러 data block과 관련된 page를 처리해야 하므로 full merge가 여러 번 발생할 수 있다.

자료의 예에서는 1 page update 때문에 3번 full merge가 필요한 상황을 보여주며, 이것이 FAST의 큰 overhead가 될 수 있음을 설명한다.

## BAST와 FAST 비교

| 항목 | BAST | FAST |
|---|---|---|
| Log block 연결 | data block과 associative | fully associative |
| 장점 | 구조 단순 | log block utilization 개선 |
| 약점 | thrashing, full merge 빈번 | random log merge 비용 큼 |
| Merge | switch/full 중심 | sequential/random log에 따라 다양 |

## 시험ㆍ복습 체크포인트

- FTL이 erase-before-write 문제를 어떻게 숨기는지 설명할 수 있어야 한다.
- Page mapping과 block mapping의 memory/performance tradeoff를 비교해야 한다.
- Switch, partial, full merge의 비용 차이를 이해해야 한다.
- BAST에서 full merge가 자주 발생하는 이유를 말할 수 있어야 한다.
- FAST가 BAST의 어떤 문제를 해결하려고 하는지 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. SSD Software 1 - FTL Overview, BAST, FAST**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **09. SSD Software 1 - FTL Overview, BAST, FAST**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. SSD Hardware 4 - NAND Flash Memory](08-ssd-hardware-4-nand-flash-memory.md) · 다음: [10. SSD Software 2 - Superblock, LAST, FTL Functions](10-ssd-software-2-superblock-last-and-ftl-functions.md)
