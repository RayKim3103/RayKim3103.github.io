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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Assignment 5 Cache**를 다루며, ISA, datapath, control, memory hierarchy를 연결해 프로그램이 실제 하드웨어 위에서 실행되는 비용을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 컴퓨터구조 문제는 datapath에서 값이 흐르는 경로와 control signal이 켜지는 이유를 함께 그린다.
- 성능 계산은 cycle 수를 직접 세는 방식과 CPU time 식으로 검산하면 실수를 줄일 수 있다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- ISA는 software와 hardware 사이의 계약이고, microarchitecture는 그 계약을 빠르게 실행하기 위한 구현 선택이다.

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
- **10. Assignment 5 Cache**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. Assignment 4 Branch Prediction](09-assignment-4-branch-prediction.md)
