---
layout: page
title: "04. Computer Organization Chapter 6 - Storage and I/O Topics"
permalink: /studies/arch/microprocessor/04-computer-organization-chapter-6-storage-and-io-topics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/04%20Computer%20Organization%20Chapter%206%20-%20Storage%20and%20IO%20Topics.md)

{% raw %}
tags: #micro-processor #computer-organization #io #storage #bus #interrupt #dma #disk

관련 노트: [ARM Chapter 3 - ARM Assembly Language Programming](03-arm-chapter-3-assembly-language-programming.md), [Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy](04a-computer-organization-chapter-6-storage-and-io-topics-annotated-copy.md), [Computer Organization Chapter 7 - Multiprocessors](05-computer-organization-chapter-7-multiprocessors.md)

## 핵심 요약

이 자료는 computer system의 I/O와 storage를 다룬다. I/O device 다양성, throughput과 latency, hard disk access time, network, bus 구조, synchronous/asynchronous protocol, bus arbitration, OS와 I/O interface, polling, interrupt, DMA, I/O processor를 설명한다.

## I/O의 중요성

Computer의 다섯 구성 요소는 processor, memory, input, output, datapath/control로 볼 수 있다. Processor가 빨라져도 I/O를 무시하면 전체 system 성능은 I/O에서 병목이 된다.

I/O device는 behavior, partner, data rate가 다양하다.

- input device: mouse, keyboard, sensor
- output device: display, printer
- storage: disk, SSD
- communication: network interface

## Throughput과 Latency

| 개념 | 의미 |
|---|---|
| Throughput | 단위 시간당 완료되는 작업 수 |
| Latency | 하나의 요청이 완료되기까지 걸리는 시간 |

Throughput은 병렬 처리나 block transfer로 높일 수 있지만, 큰 block size는 개별 요청 latency를 늘릴 수 있다. 사용자 응답성에는 latency가 중요하고, 대량 데이터 이동에는 throughput이 중요하다.

## Disk Access Time

Hard disk access time은 여러 구성 요소의 합이다.

```text
Disk service time = seek time + rotational latency + transfer time + controller overhead
```

| 항목 | 의미 |
|---|---|
| Seek time | head가 원하는 track으로 이동하는 시간 |
| Rotational latency | 원하는 sector가 head 아래 올 때까지 기다리는 시간 |
| Transfer time | 실제 data bit를 읽거나 쓰는 시간 |
| Controller overhead | controller 처리 시간 |

평균 rotational latency는 보통 반 바퀴 회전 시간이다.

```text
Average rotational latency = 0.5 rotation / RPM
```

## Network

Network는 computer 사이 data communication을 담당한다. 성능은 bandwidth와 latency로 평가하며, hardware latency와 software latency가 모두 영향을 준다.

Point-to-point network와 shared network는 연결 방식과 contention 특성이 다르다.

## Bus

Bus는 여러 component가 공유하는 communication link이다.

Bus line:

- address lines
- data lines
- control lines

Bus transaction은 address 전송과 data 전송을 포함한다.

## Bus 종류

| Bus | 특징 |
|---|---|
| Processor-memory bus | CPU와 memory 중심, high speed |
| I/O bus | 여러 peripheral 연결, 표준화 중요 |
| Backplane bus | 확장성과 여러 module 연결 |

Single-bus system은 단순하지만 병목이 크다. Two-bus 또는 three-bus system은 processor-memory traffic과 I/O traffic을 분리해 성능과 확장성을 높인다.

## Synchronous와 Asynchronous Bus

### Synchronous Bus

공통 clock에 맞춰 transaction이 진행된다. 구현이 단순하고 빠르지만, bus 길이와 device 속도가 clock period를 제한한다.

### Asynchronous Bus

Clock 대신 request/acknowledge handshake로 동작한다.

Read protocol 예:

1. Master가 address와 ReadReq를 낸다.
2. Slave가 요청을 보고 Ack를 올린다.
3. Data가 준비되면 master가 읽는다.
4. Request와 Ack를 내리며 transaction을 끝낸다.

느린 device와 빠른 device를 함께 다루기 좋지만 control이 복잡하다.

## Bus Performance

Bandwidth를 높이는 방법:

- data bus width 증가
- block transfer 사용
- split transaction
- overlapped arbitration
- bus parking

하지만 complexity와 latency, arbitration overhead가 늘 수 있다.

## Bus Arbitration

Multiple master가 bus를 공유하려면 arbitration이 필요하다.

| 방식 | 특징 |
|---|---|
| Centralized parallel arbitration | 중앙 arbiter가 요청을 보고 grant |
| Daisy chain arbitration | grant가 장치들을 순서대로 통과, priority가 위치에 의존 |
| Distributed self-selection | 각 장치가 request를 보고 스스로 우선순위 판단 |
| Collision detection | 충돌을 감지하고 재시도 |

Arbitration은 priority, fairness, latency, cost 사이의 tradeoff를 가진다.

## OS와 I/O Interface

I/O는 OS를 통해 관리되어야 한다.

이유:

- protection
- resource sharing
- scheduling
- device abstraction
- error handling

OS는 device driver를 통해 device register 접근, interrupt 처리, buffer 관리, DMA setup을 수행한다.

## CPU-Device Interaction

### Polling

CPU가 status register를 주기적으로 읽는다.

장점:

- 단순함
- 예측 가능한 control flow

단점:

- CPU time 낭비
- polling interval 사이의 event를 놓칠 수 있음

### Interrupt

Device가 event 발생 시 CPU를 interrupt한다.

장점:

- CPU가 기다리며 낭비하지 않음
- event-driven 처리 가능

단점:

- interrupt overhead
- priority와 nesting 처리 필요
- 너무 잦으면 system overhead 증가

## DMA

DMA(Direct Memory Access)는 CPU가 data word를 직접 하나씩 옮기지 않고, DMA controller가 memory와 I/O device 사이 data를 전송하는 방식이다.

절차:

1. CPU가 source, destination, length, direction을 DMA controller에 설정한다.
2. DMA controller가 bus master로 data를 전송한다.
3. 완료 후 interrupt로 CPU에 알린다.

DMA 문제:

- cache coherence
- virtual address와 physical address 변환
- bus contention

## I/O Processor

DMA controller는 hard-wired special I/O processor로 볼 수 있다. 더 일반적인 I/O processor는 firmware나 software를 실행하며 복잡한 I/O task를 CPU 대신 처리한다.

## 시험ㆍ복습 체크포인트

- Throughput과 latency의 차이를 예시로 설명할 수 있어야 한다.
- Disk service time 구성 요소를 식으로 쓸 수 있어야 한다.
- Synchronous bus와 asynchronous bus의 장단점을 비교할 수 있어야 한다.
- Bus arbitration 방식별 특징을 구분해야 한다.
- Polling, interrupt, DMA의 CPU overhead 차이를 설명할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Computer Organization Chapter 6 - Storage and I/O Topics**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **04. Computer Organization Chapter 6 - Storage and I/O Topics**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. ARM Chapter 3 - ARM Assembly Language Programming](03-arm-chapter-3-assembly-language-programming.md) · 다음: [04. Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy](04a-computer-organization-chapter-6-storage-and-io-topics-annotated-copy.md)
