---
layout: page
title: "08. SSD Hardware 4 - NAND Flash Memory"
permalink: /studies/arch/microprocessor/08-ssd-hardware-4-nand-flash-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/08%20SSD%20Hardware%204%20-%20NAND%20Flash%20Memory.md)

{% raw %}
tags: #micro-processor #ssd #nand-flash #flash-memory #ecc #bad-block #3d-nand

관련 노트: [SSD Overview 2 - SSD Architecture](07-ssd-overview-2-architecture.md), [SSD Software 1 - FTL Overview, BAST, FAST](09-ssd-software-1-ftl-overview-bast-fast.md)

## 핵심 요약

이 자료는 SSD hardware의 핵심 저장 매체인 NAND flash memory를 다룬다. NOR와 NAND flash 비교, floating gate cell, program/read/erase operation, SLC/MLC, memory organization, command/timing, interleaved operation, bad block, reliability issue, ECC, 3D NAND까지 설명한다.

## NOR Flash와 NAND Flash

| 항목 | NOR Flash | NAND Flash |
|---|---|---|
| 접근 방식 | random direct access에 유리 | page/block 기반 access |
| 읽기 | 빠른 random read | sequential/page read에 적합 |
| 쓰기/삭제 | 상대적으로 느림 | write/erase 밀도와 성능 유리 |
| 용도 | code storage, execute-in-place | mass storage, SSD |
| 밀도/비용 | 낮은 밀도, 높은 비용 | 높은 밀도, 낮은 bit cost |

NAND는 write/erase 효율과 높은 집적도 때문에 SSD의 주류가 되었다.

## NAND Flash 표준

NAND flash interface와 command set은 ONFI 같은 표준화 흐름과 vendor-specific interface가 함께 발전했다. SSD controller는 여러 NAND chip의 timing과 command protocol을 맞춰 제어한다.

## Unit Cell Structure

NAND flash cell은 floating gate 또는 charge trap에 전하를 저장해 threshold voltage를 바꾼다.

```text
저장 전하 많음 -> Vth 증가
저장 전하 적음 -> Vth 감소
```

Read operation은 select cell에 특정 gate voltage를 인가하고 cell current를 sensing해 data를 판별한다.

## NAND Flash Cell Array

NAND flash cell은 word line과 bit line으로 배열된다. 여러 cell이 series string으로 연결되고, select transistor가 string의 양끝을 제어한다.

구조 단위:

- cell
- page
- block
- plane
- die
- package

## Program Operation

Program은 floating gate에 전하를 주입해 threshold voltage를 높이는 과정이다. NAND는 보통 page 단위로 program한다.

ISPP(Incremental Step Pulse Program):

1. 작은 program pulse를 인가한다.
2. verify read로 threshold가 목표 범위에 들어갔는지 확인한다.
3. 부족하면 더 높은 pulse를 인가한다.
4. 목표에 도달할 때까지 반복한다.

ISPP는 cell 분포를 제어해 reliability를 높인다.

## Read Operation

Read는 select cell의 threshold voltage와 read reference voltage를 비교해 data를 판별한다. On-cell current가 크면 sensing margin이 커져 안정성이 좋아진다.

MLC/TLC처럼 cell당 bit 수가 늘어나면 threshold level 간 간격이 좁아져 sensing margin이 줄고 ECC 요구가 커진다.

## Erase Operation

Erase는 block 단위로 수행된다. 이것이 NAND flash의 중요한 제약이다.

```text
read/program: page 단위
erase: block 단위
```

이미 program된 page를 직접 overwrite할 수 없으므로 SSD는 FTL과 garbage collection이 필요하다.

## SLC와 MLC

| 종류 | 저장 bit | Threshold level | 특징 |
|---|---:|---:|---|
| SLC | 1 bit/cell | 2 levels | 빠르고 endurance 높음 |
| MLC | 2 bit/cell | 4 levels | 밀도 높지만 margin 감소 |
| TLC/QLC 계열 | 3/4 bit 이상 | 8/16 levels | 비용 유리, ECC와 controller 부담 큼 |

## Pin Configuration과 Command Set

NAND flash는 command, address, data를 정해진 bus와 control signal로 주고받는다.

대표 operation:

- Read ID
- Read Status
- Page Read
- Page Program
- Change Read Column
- Change Write Column
- Block Erase
- Copyback
- Page Cache Program/Read

## Memory Organization과 Addressing

Address는 column address와 row address로 나뉜다.

- Column address: page 내부 byte/word 위치
- Row address: page, block, plane 위치

Random page programming이 제한되는 경우가 있어 program order를 controller가 관리해야 한다.

## Copyback

Copyback은 data를 외부 host로 모두 내보내지 않고 NAND 내부에서 page를 다른 위치로 복사하는 operation이다. Garbage collection이나 wear-leveling에서 data 이동 비용을 줄인다.

Data modification copyback은 일부 data를 수정하며 복사하는 방식이다.

## Interleaved Operation

여러 plane이나 die가 있을 때 interleaving으로 한 chip이 program/erase 중인 동안 다른 chip에서 read/write를 수행할 수 있다.

효과:

- 내부 parallelism 증가
- channel utilization 개선
- latency hiding

## Bad Block

NAND flash는 제조 시점부터 일부 bad block이 존재할 수 있고, 사용 중에도 wear로 bad block이 늘어난다. Controller는 bad block identification 정보를 읽고, bad block table을 관리하며, bad block을 사용하지 않도록 mapping한다.

## Reliability Issues

주요 문제:

- Program disturbance
- Read disturbance
- Floating-poly coupling noise
- Threshold voltage distribution widening
- Endurance degradation
- Retention loss

Program/read stress는 인접 cell과 같은 block의 threshold voltage를 변화시킬 수 있다.

## ECC

NAND flash는 bit error가 발생할 수 있으므로 ECC가 필수이다.

예:

- Hamming code
- BCH
- LDPC

Cell당 bit 수가 늘어날수록 threshold margin이 줄어 더 강한 ECC가 필요하다.

## 3D NAND

2D scaling의 한계를 극복하기 위해 cell을 수직 방향으로 쌓는 3D NAND가 등장했다. 3D NAND는 높은 density를 제공하고 tera-bit storage로 확장 가능하게 만든다.

## 시험ㆍ복습 체크포인트

- NOR와 NAND flash의 access 특성과 용도를 비교할 수 있어야 한다.
- Program, read, erase의 단위 차이를 설명할 수 있어야 한다.
- ISPP가 threshold voltage 분포 제어에 필요한 이유를 이해해야 한다.
- SLC와 MLC의 density/reliability tradeoff를 말할 수 있어야 한다.
- Bad block management와 ECC가 SSD controller에 필요한 이유를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. SSD Hardware 4 - NAND Flash Memory**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **08. SSD Hardware 4 - NAND Flash Memory**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. SSD Overview 2 - SSD Architecture](07-ssd-overview-2-architecture.md) · 다음: [09. SSD Software 1 - FTL Overview, BAST, FAST](09-ssd-software-1-ftl-overview-bast-fast.md)
