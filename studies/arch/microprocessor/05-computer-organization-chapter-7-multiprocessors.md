---
layout: page
title: "05. Computer Organization Chapter 7 - Multiprocessors"
permalink: /studies/arch/microprocessor/05-computer-organization-chapter-7-multiprocessors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/05%20Computer%20Organization%20Chapter%207%20-%20Multiprocessors.md)

{% raw %}
tags: #micro-processor #multiprocessor #parallelism #cache-coherence #mesi #synchronization #interconnection-network

관련 노트: [Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy](04a-computer-organization-chapter-6-storage-and-io-topics-annotated-copy.md), [SSD Overview 1 - Solid State Disk Basics](06-ssd-overview-1-solid-state-disk-basics.md)

## 핵심 요약

이 자료는 multiprocessor와 parallel computer를 다룬다. Parallelism의 종류, SISD/SIMD/MIMD 분류, speedup과 Amdahl's law, shared-memory multiprocessor, cache coherence, snooping protocol, MESI, synchronization, interconnection network를 설명한다.

## Parallel Machine의 아이디어

여러 작은 processor를 연결해 더 큰 성능을 얻는 것이 parallel machine의 기본 아이디어이다. 필요할 때 processor 수를 늘려 성능을 확장할 수 있지만, 실제 성능은 communication, synchronization, serial portion 때문에 제한된다.

## Parallelism의 종류

| 종류 | 의미 |
|---|---|
| DLP | Data-level parallelism, 같은 연산을 많은 data에 적용 |
| TLP | Task-level parallelism, 서로 다른 task를 병렬 수행 |
| ILP | Instruction-level parallelism, 한 thread 내부 instruction 병렬성 |
| Thread-level parallelism | 여러 thread 동시 실행 |
| Request-level parallelism | 독립적인 request들을 병렬 처리 |

## Flynn 분류

| 분류 | 의미 | 예 |
|---|---|---|
| SISD | Single instruction stream, single data stream | 전통적 single processor |
| SIMD | Single instruction stream, multiple data streams | vector processor, GPU 일부 |
| MIMD | Multiple instruction streams, multiple data streams | multicore, cluster |

MIMD는 tightly-coupled system과 loosely-coupled system으로 나눌 수 있다.

## Speedup

Speedup은 processor를 늘렸을 때 execution time이 얼마나 줄었는지 나타낸다.

```text
S(n) = T(1) / T(n)
```

Ideal linear speedup은 다음과 같다.

```text
S(n) = n
```

하지만 실제로는 병렬화할 수 없는 부분, communication overhead, load imbalance 때문에 linear speedup이 어렵다.

## Amdahl's Law

Program의 일부만 병렬화된다면 전체 speedup은 serial fraction에 의해 제한된다.

```text
Speedup = 1 / (s + (1-s)/n)
```

| 기호 | 의미 |
|---|---|
| `s` | 병렬화할 수 없는 serial fraction |
| `n` | processor 수 |

Processor 수를 무한히 늘려도 최대 speedup은 `1/s`를 넘지 못한다.

## Shared Memory와 Cache Coherence

Shared-memory multiprocessor에서는 여러 processor가 같은 memory address space를 공유한다. 각 processor가 cache를 가지면 같은 memory block의 복사본이 여러 cache에 존재할 수 있다.

Cache coherence problem:

```text
한 processor가 값을 write했는데 다른 processor cache에는 old value가 남아 있음
```

## Coherence Protocol

대표 방식:

- Write invalidate: write할 때 다른 cache copy를 invalid로 만듦
- Write update: write한 값을 다른 cache copy에 broadcast함

Write invalidate는 같은 word에 여러 번 write할 때 traffic이 적다. Write update는 reader가 최신 값을 빨리 보지만 broadcast traffic이 커질 수 있다.

## Snooping Protocol

Bus 기반 shared-memory system에서는 각 cache controller가 bus transaction을 감시한다. 다른 processor의 read/write miss를 보고 자신의 cache line state를 바꾼다.

기본 state 예:

- Invalid
- Shared
- Modified

## MESI Protocol

MESI는 네 가지 state를 사용한다.

| State | 의미 |
|---|---|
| Modified | 이 cache만 최신 dirty copy 보유 |
| Exclusive | 이 cache만 clean copy 보유 |
| Shared | 여러 cache가 clean copy 공유 |
| Invalid | 유효하지 않음 |

Exclusive state는 shared되지 않은 clean block을 write할 때 bus transaction 없이 Modified로 바꿀 수 있게 해 traffic을 줄인다.

## Synchronization

공유 data를 여러 process/thread가 동시에 접근하면 race condition이 발생할 수 있다. Critical section은 mutual exclusion을 보장해야 한다.

Correctness criteria:

- mutual exclusion
- progress
- bounded waiting

## Test and Set

`test_and_set`은 lock 구현에 쓰이는 atomic operation이다.

```c
test_and_set(lock) {
    old = lock;
    lock = true;
    return old;
}
```

Naive spin lock은 bus traffic을 많이 만들 수 있으므로, local cache에서 spinning하다가 필요한 순간에 atomic operation을 수행하는 optimized synchronization이 필요하다.

## Interconnection Networks

Multiprocessor 성능은 processor 간 연결망에도 크게 의존한다.

| Network | 특징 |
|---|---|
| Bus | 단순하지만 확장성 낮음 |
| Crossbar | 모든 node 간 연결 가능, 비용 큼 |
| Omega network | multistage network, 비용과 성능 절충 |
| Ring | 저비용, latency가 node 수에 의존 |
| Mesh | 2D layout에 자연스러움 |
| 2D Torus | mesh의 edge를 연결해 균형 개선 |
| Hypercube | n차원 구조, 2^n node |

## 시험ㆍ복습 체크포인트

- SISD, SIMD, MIMD를 구분할 수 있어야 한다.
- Amdahl's law로 serial fraction이 speedup을 제한하는 이유를 설명할 수 있어야 한다.
- Cache coherence problem을 예로 설명할 수 있어야 한다.
- Write invalidate와 write update의 차이를 말할 수 있어야 한다.
- MESI 네 state의 의미를 구분할 수 있어야 한다.
- Test-and-set이 atomic해야 하는 이유를 이해해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Computer Organization Chapter 7 - Multiprocessors**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- 저장장치 주제에서는 logical 주소와 physical 위치가 언제 어떻게 mapping되는지 추적한다.
- FTL/파일시스템 계층은 성능뿐 아니라 crash consistency와 metadata 복구 경로가 중요하다.
- processor 관점에서는 register, instruction encoding, addressing mode, exception 흐름을 함께 보아야 한다.

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
- **05. Computer Organization Chapter 7 - Multiprocessors**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Computer Organization Chapter 6 - Storage and I/O Topics Annotated Copy](04a-computer-organization-chapter-6-storage-and-io-topics-annotated-copy.md) · 다음: [06. SSD Overview 1 - Solid State Disk Basics](06-ssd-overview-1-solid-state-disk-basics.md)
