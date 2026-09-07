---
layout: page
title: "04. Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy"
permalink: /studies/arch/microprocessor/04a-computer-organization-chapter-6-storage-and-io-topics-annotated-copy/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/04A%20Computer%20Organization%20Chapter%206%20-%20Storage%20and%20IO%20Topics%20Annotated%20Copy.md)

{% raw %}
tags: #micro-processor #computer-organization #io #storage #bus #interrupt #dma #annotated-copy

관련 노트: [Computer Organization Chapter 6 - Storage and I/O Topics](04-computer-organization-chapter-6-storage-and-io-topics.md), [Computer Organization Chapter 7 - Multiprocessors](05-computer-organization-chapter-7-multiprocessors.md)

## 핵심 요약

이 자료는 [Computer Organization Chapter 6 - Storage and I/O Topics](04-computer-organization-chapter-6-storage-and-io-topics.md)와 같은 84쪽 Storage and Other I/O Topics 강의의 별도 사본이다. 텍스트 추출 기준으로 본문 내용은 동일하며, I/O 성능 지표, disk access, bus protocol, arbitration, OS I/O, polling, interrupt, DMA를 같은 순서로 다룬다.

## 이 사본을 볼 때의 관점

이 파일은 같은 강의의 duplicate 또는 annotation용 copy로 보인다. 따라서 개념 정리는 원 강의 노트와 같게 보되, 복습할 때는 다음 항목을 중점적으로 확인하면 좋다.

- I/O가 processor 성능 향상 후 system bottleneck이 되는 이유
- Throughput과 latency의 차이
- Disk service time 계산
- Bus protocol과 arbitration
- Polling, interrupt, DMA의 역할 분담

## I/O 성능 핵심

I/O 성능은 하나의 숫자로만 볼 수 없다.

| 지표 | 의미 | 중요한 상황 |
|---|---|---|
| Latency | 한 요청의 응답 시간 | 사용자 상호작용, random access |
| Throughput | 단위 시간당 처리량 | 대용량 전송, batch I/O |
| Bandwidth | link가 전달할 수 있는 data rate | bus/network/storage |

Block size를 키우면 throughput은 좋아질 수 있지만, 작은 요청의 latency는 나빠질 수 있다.

## Disk Access 복습

Hard disk의 접근 시간은 다음 합으로 본다.

```text
service time = seek time + average rotational latency + transfer time + controller overhead
```

평균 rotational latency는 디스크가 원하는 sector까지 평균 반 바퀴 돌아야 한다는 가정에서 나온다.

## Bus 복습

Bus는 address, data, control line으로 구성된다. Transaction은 master가 bus를 얻고, address/control을 내고, slave가 data를 주거나 받는 과정이다.

중요한 구분:

- synchronous bus: 공통 clock 사용
- asynchronous bus: request/ack handshake 사용
- multiplexed bus: address와 data line을 시간적으로 공유
- split transaction: 요청과 응답 사이 bus를 다른 transaction에 넘김

## Arbitration 복습

Multiple master system에서는 누가 bus를 사용할지 결정해야 한다.

| 방식 | 장점 | 약점 |
|---|---|---|
| Centralized | 구현과 priority 관리가 명확 | arbiter 병목과 single point |
| Daisy chain | 단순 | 위치 기반 priority 불공정 |
| Distributed | 중앙 장치 불필요 | 구현 복잡 |
| Collision detection | shared medium에 적합 | 충돌 시 재시도 overhead |

## Polling, Interrupt, DMA

| 방식 | CPU 역할 | 특징 |
|---|---|---|
| Polling | 계속 status 확인 | 단순하지만 CPU 낭비 |
| Interrupt | event 발생 시 handler 실행 | 효율적이지만 overhead와 priority 관리 필요 |
| DMA | setup 후 controller가 직접 전송 | 대량 전송에 적합, cache coherence 주의 |

## 시험ㆍ복습 체크포인트

- 이 사본은 04번 노트와 같은 개념을 담는 자료로 이해한다.
- Disk service time 식을 암기하고 각 항목을 설명할 수 있어야 한다.
- Synchronous/asynchronous bus protocol 차이를 설명할 수 있어야 한다.
- DMA가 bus master로 동작할 수 있다는 점을 이해해야 한다.
- DMA와 cache coherence 문제가 왜 연결되는지 말할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **04. Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Computer Organization Chapter 6 - Storage and I/O Topics](04-computer-organization-chapter-6-storage-and-io-topics.md) · 다음: [05. Computer Organization Chapter 7 - Multiprocessors](05-computer-organization-chapter-7-multiprocessors.md)
