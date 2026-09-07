---
layout: page
title: "10. Assignment 5 Cache"
permalink: /studies/arch/computer-architecture/10-assignment-5-cache/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Architecture/lecture_notes/10%20Assignment%205%20Cache.md)

{% raw %}
## 한눈에 보기
Kite의 data cache에 victim cache를 추가하는 과제이다. direct-mapped cache에서 자주 발생하는 conflict miss를 줄이기 위해 evicted block을 작은 buffer에 보관하고, 나중에 다시 접근하면 data cache로 되돌린다.

## 핵심 개념
- data cache
- direct-mapped cache
- victim cache
- conflict miss
- FIFO
- dirty bit
- valid bit
- tag
- block offset
- write-back

## 체계적 정리
- 기본 skeleton은 512-byte direct-mapped cache와 32-byte line size를 사용한다.
- direct-mapped cache는 총 16 sets를 가진다.
- read/write는 memory stage에서 ld 또는 sd instruction이 실행될 때 호출된다.
- cache access는 address에서 set index, tag, block offset을 계산하는 것에서 시작한다.
- valid bit가 1이고 tag가 일치하면 cache hit이다.
- read hit에서는 block 내부 doubleword 위치에서 값을 읽고, write hit에서는 값을 쓰고 dirty bit를 세운다.
- miss가 발생하면 memory에서 block base address를 기준으로 32-byte block을 가져온다.
- eviction된 cache block은 victim cache에 들어가며, victim cache가 가득 차면 FIFO 기준으로 가장 오래된 entry를 내보낸다.
- miss block이 victim cache에서 발견되면 victim entry와 data cache line을 교환해 conflict miss penalty를 줄인다.

## 중요한 수식과 관점
- num_sets = cache_size / block_size = 512 / 32 = 16.
- block base address = addr & ~block_mask.
- block 내부 doubleword index = (addr & block_mask) >> 3.
- direct-mapped set index는 address의 index bit로 결정되고 tag는 나머지 상위 bit다.

## 구현과 학습 포인트
구현에서는 victim cache search, data cache eviction, dirty block writeback, FIFO insertion/removal 순서가 중요하다. write miss와 read miss 모두 replacement path를 공유하므로 cache block의 valid, dirty, tag, data, base address를 일관되게 유지해야 한다.

## 자주 헷갈리는 지점
- victim hit은 단순 memory fetch가 아니라 victim entry와 direct-mapped cache entry의 swap으로 처리해야 한다.
- dirty eviction을 memory에 쓰지 않으면 이후 load 결과가 틀린다.
- block offset을 byte 단위와 doubleword 단위로 혼동하면 cache block 내부 위치가 어긋난다.
- last access time은 direct-mapped 본 cache에서는 LRU에 쓰이지 않지만, replacement policy가 바뀌면 의미가 생길 수 있다.

## 복습 질문
- 이 자료에서 다루는 abstraction layer는 software 쪽에 가까운가, hardware 쪽에 가까운가?
- 성능을 판단할 때 clock rate, CPI, instruction count 중 무엇이 병목인지 어떻게 구분할 수 있는가?
- RISC-V 구현에서 register, memory, PC, immediate, pipeline state 중 어떤 값이 다음 단계로 전달되는가?

## 연결 노트
- [Memory Hierarchy](05-memory-hierarchy.md)
- [Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
- [The Processor](04-the-processor.md)

{% endraw %}

---

이전: [09. Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
