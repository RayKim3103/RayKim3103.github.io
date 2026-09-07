---
layout: page
title: "06. SSD Overview 1 - Solid State Disk Basics"
permalink: /studies/arch/microprocessor/06-ssd-overview-1-solid-state-disk-basics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/06%20SSD%20Overview%201%20-%20Solid%20State%20Disk%20Basics.md)

{% raw %}
tags: #micro-processor #ssd #flash-memory #storage #hdd #sata #pcie

관련 노트: [Computer Organization Chapter 7 - Multiprocessors](05-computer-organization-chapter-7-multiprocessors.md), [SSD Overview 2 - SSD Architecture](07-ssd-overview-2-architecture.md)

## 핵심 요약

이 자료는 SSD의 정의, HDD와의 차이, interface, hybrid SSD, SSD 내부 구조 개요, 성능 비교, NAND flash 시장 흐름을 소개한다. 핵심은 SSD가 moving part가 없는 non-volatile memory 기반 저장장치라서 random access latency가 HDD보다 훨씬 낮다는 점이다.

## SSD 정의

SSD는 Solid-State Disk 또는 Solid-State Drive의 약자로, magnetic disk가 아니라 non-volatile memory chip을 사용해 data를 저장하는 장치이다.

Solid-state의 의미:

- vacuum tube나 gas-discharge tube가 아님
- relay, switch처럼 움직이는 electro-mechanical device가 아님
- transistor, microprocessor, DRAM, flash memory처럼 고체 물질 기반

## SSD 특징

장점:

- random access latency가 낮음
- mechanical seek가 없음
- 충격에 강함
- 소음이 없음
- 전력 소모가 낮을 수 있음

한계:

- flash cell의 program/erase cycle 수명 제한
- erase-before-write 특성
- write amplification과 garbage collection overhead
- controller와 FTL 설계가 성능에 큰 영향

## SSD Interface

대표 interface:

- SATA
- PCI Express
- rackmount storage interface

SATA는 HDD와 호환되는 storage interface로 출발했고, PCIe 기반 SSD는 더 높은 bandwidth와 낮은 latency를 제공한다.

## Hybrid SSD

Hybrid SSD는 DRAM, NAND flash, magnetic disk 또는 다른 storage 계층을 조합해 성능과 비용을 절충한다. 자주 접근하는 data는 빠른 memory에 두고, 대용량 data는 비용이 낮은 media에 둔다.

## SSD와 HDD 내부 차이

| 항목 | SSD | HDD |
|---|---|---|
| 저장 매체 | NAND flash memory | magnetic platter |
| 움직이는 부품 | 없음 | spindle, head 존재 |
| Random access | 빠름 | seek와 회전 대기 필요 |
| Sequential access | interface/controller 영향 큼 | platter transfer rate 영향 |
| 내구성 | write endurance 관리 필요 | mechanical failure 가능 |

## 성능 비교

### Access Time

HDD는 head 이동과 rotational latency가 필요하다. SSD는 전기적으로 cell과 page에 접근하므로 random access time이 훨씬 작다.

### Sequential Read/Write

Sequential access에서는 interface bandwidth와 내부 parallelism이 중요하다. HDD도 sequential transfer에서는 비교적 강하지만, SSD는 여러 NAND channel을 병렬로 사용해 높은 throughput을 낼 수 있다.

### Random Read/Write

SSD는 random read에서 매우 강하다. Random write는 erase-before-write, garbage collection, write amplification 때문에 controller와 FTL 설계에 크게 좌우된다.

## NAND Flash 시장

자료는 NAND flash가 storage 시장에서 지배적 위치를 차지하게 된 흐름과 SSD 가격이 HDD 가격에 가까워지는 추세를 소개한다. Flash density 증가와 가격 하락은 SSD 보급의 핵심 배경이다.

## War of the Disks

SSD와 HDD는 성능, 가격, 용량, 신뢰성에서 서로 다른 장단점을 가진다. SSD는 latency와 random I/O가 강하고, HDD는 대용량당 비용에서 강점이 있었다. 시간이 지나며 SSD 가격이 낮아지면서 적용 범위가 넓어졌다.

## 시험ㆍ복습 체크포인트

- SSD가 HDD보다 random access에 강한 이유를 설명할 수 있어야 한다.
- SSD의 장점과 flash memory 기반 한계를 함께 말할 수 있어야 한다.
- SATA와 PCIe interface의 성능 관점 차이를 이해해야 한다.
- Random write 성능이 FTL과 garbage collection에 좌우되는 이유를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. SSD Overview 1 - Solid State Disk Basics**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **06. SSD Overview 1 - Solid State Disk Basics**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. Computer Organization Chapter 7 - Multiprocessors](05-computer-organization-chapter-7-multiprocessors.md) · 다음: [07. SSD Overview 2 - SSD Architecture](07-ssd-overview-2-architecture.md)
