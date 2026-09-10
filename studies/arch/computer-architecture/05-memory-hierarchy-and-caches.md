---
layout: page
title: "05. Memory Hierarchy & Caches"
permalink: /studies/arch/computer-architecture/05-memory-hierarchy-and-caches/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `05` + 과제 `10(Victim Cache)` 통합·보강
- 교재: Patterson & Hennessy ch 5. **과제 세부(Kite)는 원문 그대로**입니다.

{% raw %}
## 개요

빠르고 작은 cache ↔ 크고 느린 memory의 **latency gap**을 **locality**로 메운다. hit/miss, mapping, tag/index/offset, write policy, miss penalty, 3C miss.

## 1. Locality

이상적 memory는 빠르고·크고·싸야 하지만 실제 device는 동시에 불가 → 계층 구조.
- **temporal locality**: 최근 접근한 data를 다시 접근할 가능성 → 그 data를 cache에 유지.
- **spatial locality**: 인접 data를 접근할 가능성 → block(line) 단위로 가져옴.

## 2. Cache Mapping — 주소 분해

$$
\text{address} = [\ \text{tag}\ |\ \text{index}\ |\ \text{block offset}\ ]
$$
- **block offset**: block 내부 위치. block size $$= 2^b$$ byte → offset $$b$$ bit. ($$2^m$$ doubleword block이면 byte offset은 $$m+3$$ bit.)
- **index**: 어느 set인가. set 수 $$= 2^s$$ → index $$s$$ bit.
- **tag**: 같은 index로 들어오는 여러 memory block 구분. **valid bit**로 entry 유효성 표시.

$$
\text{cache index} = \text{block address} \bmod \text{(number of sets)}
$$

| | 구조 | 특징 |
|---|---|---|
| **direct-mapped** | block이 갈 수 있는 entry가 **하나** | 단순·빠른 hit time, **conflict miss**에 취약 |
| **n-way set-associative** | 한 set의 $$n$$개 way 중 아무 곳 | conflict miss↓, tag compare $$n$$개·hit time↑ |
| **fully associative** | 아무 entry나 | conflict miss 최소, 비교 비용 최대 |

replacement policy: LRU, FIFO, random, pseudo-LRU.

## 3. Write Policy

| | store hit 시 | eviction 시 |
|---|---|---|
| **write-through** | cache + memory 동시 갱신 (write buffer로 완화) | 항상 memory와 일치 |
| **write-back** | cache만 갱신, **dirty bit** set | dirty block만 memory에 write |

write miss: write-allocate(block 가져옴) vs no-write-allocate.

## 4. 성능

$$
\text{AMAT} = \text{hit time} + \text{miss rate} \times \text{miss penalty}
$$
$$
\text{Memory stall cycles} = \text{IC} \times \text{(memory accesses/instr)} \times \text{miss rate} \times \text{miss penalty}
$$
cache miss → pipeline stall → CPI 증가.

## 5. 3C Miss

| miss | 원인 | 해결 |
|---|---|---|
| **compulsory** (cold) | 처음 접근 | prefetching, block size↑ |
| **capacity** | cache가 working set보다 작음 | cache size↑ |
| **conflict** | 같은 set에 몰림 (direct-mapped/저associativity) | associativity↑, victim cache |

trade-off: block size↑ → spatial locality엔 좋지만 **miss penalty·cache pollution↑**. associativity↑ → miss↓ 이지만 hit time·전력↑.

---

## 과제 A5 — Kite data cache에 Victim Cache 추가

- skeleton: **512-byte direct-mapped cache**, **32-byte line** → `num_sets = 512 / 32 = 16`.
- read/write는 **memory stage**에서 `ld`/`sd` 실행 시 호출.
- cache access: address에서 **set index, tag, block offset** 계산.
  - `block base address = addr & ~block_mask`
  - block 내부 doubleword index `= (addr & block_mask) >> 3`
  - direct-mapped set index = address의 index bit, tag = 나머지 상위 bit
- `valid == 1` 이고 tag 일치 → **hit**. read hit: block 내부 doubleword 읽기. write hit: 쓰고 **dirty bit** set.
- miss: memory에서 block base address 기준 32-byte block fetch.
- **evicted block → victim cache로**. victim cache가 가득 차면 **FIFO**로 가장 오래된 entry 방출. dirty eviction은 memory에 write-back.
- **miss block이 victim cache에서 발견되면 → victim entry와 data cache line을 swap** (단순 memory fetch가 아님) → conflict miss penalty↓.
- 구현 순서: victim cache search → data cache eviction → dirty block writeback → FIFO insertion/removal. valid·dirty·tag·data·base address 일관 유지.

## 자주 틀리는 지점

- cache size를 말할 때 tag/valid/dirty **overhead는 data capacity와 구분**.
- write-back에서 **dirty bit 처리 오류** → memory와 cache 값 불일치.
- **victim hit은 fetch가 아니라 swap**으로 처리.
- **dirty eviction을 memory에 안 쓰면** 이후 load 결과가 틀림.
- block offset을 byte 단위 ↔ doubleword 단위 혼동.
- associativity↑ → miss↓ 이지만 hit time↑ 가능.

## 복습 질문

- 주소의 tag/index/offset 분해와, 각 필드 bit 수 계산 방법은?
- direct-mapped / set-associative / fully-associative의 trade-off는?
- write-through vs write-back, AMAT 식, 3C miss와 각 해결책은?
- victim cache가 어떤 miss를 줄이며, "victim hit = swap" 인 이유는?
{% endraw %}

---

이전: [04. The Processor: Datapath & Pipeline](04-processor-datapath-and-pipeline.md)
