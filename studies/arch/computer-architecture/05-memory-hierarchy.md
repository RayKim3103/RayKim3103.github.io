---
layout: page
title: "05. Memory Hierarchy"
permalink: /studies/arch/computer-architecture/05-memory-hierarchy/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/05%20Memory%20Hierarchy.md)

{% raw %}
## 한눈에 보기
현대 processor가 빠른 cache와 큰 memory 사이의 latency gap을 locality 원리로 줄이는 방법을 설명하는 자료이다. cache hit/miss, mapping, tag/index/offset, write policy, miss penalty가 중심이다.

## 핵심 개념
- memory hierarchy
- locality
- cache
- hit rate
- miss rate
- direct-mapped cache
- set-associative cache
- write-through
- write-back
- AMAT
- victim cache

## 체계적 정리
- ideal memory는 빠르고 크고 저렴해야 하지만 현실의 memory device는 이 조건을 동시에 만족하지 못한다.
- temporal locality는 최근 접근한 data를 다시 접근할 가능성이고, spatial locality는 인접 data를 접근할 가능성이다.
- cache는 자주 쓰는 memory block을 processor 가까이에 둔다. 요청 block이 있으면 hit, 없으면 miss다.
- direct-mapped cache는 memory block이 하나의 cache entry에만 갈 수 있어 단순하지만 conflict miss에 취약하다.
- set-associative cache는 한 set 안의 여러 way 중 하나에 block을 둘 수 있어 conflict miss를 줄인다.
- tag는 같은 index로 들어오는 여러 memory block을 구분하고 valid bit는 entry가 유효한지 나타낸다.
- write-through는 store 때 cache와 memory를 동시에 갱신하고, write-back은 dirty block eviction 때 memory에 반영한다.
- cache miss가 생기면 pipeline이 stall되고 miss penalty가 CPI를 증가시킨다.
- compulsory, capacity, conflict miss는 원인이 다르며 해결책도 prefetching, cache size 증가, associativity 증가처럼 다르다.

## 중요한 수식과 관점
- cache_index = block_address mod number_of_cache_entries.
- AMAT = hit time + miss rate x miss penalty.
- Memory stall cycles = instruction count x memory access frequency x miss rate x miss penalty.
- Cache block size가 $2^m$ doubleword이면 byte offset에는 $m+3$ bit가 필요하다.

## 구현과 학습 포인트
cache 과제에서는 512-byte direct-mapped data cache에 32-byte line을 두고 victim cache를 붙인다. address에서 set index와 tag를 계산하고, miss 시 eviction block을 victim cache로 보내며, victim hit이면 data cache와 victim entry를 교환한다.

## 자주 헷갈리는 지점
- cache size를 말할 때 tag/valid/dirty overhead는 보통 data capacity와 구분한다.
- write-back cache에서는 dirty bit 처리가 틀리면 memory와 cache 값이 달라진다.
- associativity가 늘면 miss는 줄 수 있지만 tag compare 비용과 hit time이 늘 수 있다.
- block size를 키우면 spatial locality에는 좋지만 miss penalty와 pollution이 커질 수 있다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Assignment 5 Cache](10-assignment-5-cache.md)
- [The Processor](04-the-processor.md)
- [Computer Abstractions and Technology](01-computer-abstractions-and-technology.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Memory Hierarchy**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.
- 성능은 instruction count, CPI, clock period의 곱으로 나누어 보아야 병목이 명확해진다.
- pipeline, cache, branch prediction은 평균 성능을 높이지만 hazard, miss, misprediction이라는 예외 비용을 만든다.

### 문제 풀이 또는 구현 루틴

- 명령어를 보면 operand 위치, control signal, datapath 경로, writeback 대상을 순서대로 추적한다.
- 성능 문제는 baseline cycle을 세고 stall/miss penalty를 더해 CPI로 환산한다.
- assembly 과제에서는 calling convention, stack frame, register 보존 규칙을 표로 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- clock frequency만 보고 성능을 판단하면 CPI와 instruction count 효과를 놓친다.
- pipeline은 개별 명령어 latency를 크게 줄이기보다 throughput을 높이는 기법이다.
- cache hit rate가 높아도 miss penalty가 크면 전체 실행 시간이 크게 늘 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 주제는 ISA 수준 설명인가, microarchitecture 구현인가?
- 병목이 계산, 메모리, branch, synchronization 중 어디에 있는가?
- 같은 프로그램을 더 빠르게 만들 때 compiler, ISA, hardware 중 어느 층을 바꿀 수 있는가?
- **05. Memory Hierarchy**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. The Processor](04-the-processor.md) · 다음: [06. Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md)
