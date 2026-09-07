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

{% endraw %}

---

이전: [04. The Processor](04-the-processor.md) · 다음: [06. Assignment 1 RISC-V Assembly](06-assignment-1-risc-v-assembly.md)
