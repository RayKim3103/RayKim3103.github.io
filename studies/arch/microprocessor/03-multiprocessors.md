---
layout: page
title: "03. Multiprocessors — 병렬성 · 캐시 일관성 · 동기화"
permalink: /studies/arch/microprocessor/03-multiprocessors/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · `05 Computer Organization Ch7 — Multiprocessors` 보강
- 교재: *Computer Organization and Design* (Patterson & Hennessy) Ch7 계열.

{% raw %}
## 개요

여러 작은 processor를 연결해 성능을 확장하는 것이 parallel machine의 기본 아이디어다. 실제 성능은 **communication, synchronization, serial portion** 때문에 제한된다. 이 장은 parallelism 종류, Flynn 분류, speedup과 Amdahl's law, shared-memory와 **cache coherence**(snooping, MESI), synchronization, interconnection network를 다룬다.

---

## 1. Parallelism의 종류

| 종류 | 의미 | 활용 |
|---|---|---|
| **DLP** (Data-Level) | 같은 연산을 많은 data에 동시 적용 | SIMD, vector, GPU |
| **TLP** (Task-Level) | 서로 다른 task를 병렬 수행 | multicore |
| **ILP** (Instruction-Level) | 한 thread 내부 instruction 병렬성 | superscalar, pipeline, OoO |
| **Thread-Level** | 여러 thread 동시 실행 | SMT, multicore |
| **Request-Level** | 독립적 request들을 병렬 처리 | web server, datacenter |

---

## 2. Flynn 분류

| 분류 | 의미 | 예 |
|---|---|---|
| **SISD** | single instruction, single data | 전통적 uniprocessor |
| **SIMD** | single instruction, multiple data | vector processor, GPU warp |
| **MISD** | multiple instruction, single data | (거의 사용 안 함, 일부 systolic·fault-tolerant) |
| **MIMD** | multiple instruction, multiple data | multicore, cluster |

**MIMD 세부**:
- **Tightly-coupled** (shared memory): processor들이 공통 physical memory·주소공간 공유, 통신은 load/store. → SMP.
- **Loosely-coupled** (distributed memory): 각 node가 자기 memory, 통신은 message passing(network). → cluster.

---

## 3. Speedup과 Amdahl's Law

```text
S(n) = T(1) / T(n)          이상적 linear speedup: S(n) = n
```

Linear speedup이 어려운 이유: 병렬화 불가능한 부분, communication overhead, load imbalance, synchronization 대기.

### Amdahl's Law

```text
Speedup = 1 / ( s + (1 - s) / n )
```

| 기호 | 의미 |
|---|---|
| `s` | 병렬화 **불가능한** serial fraction |
| `1 - s` | 병렬화 가능한 fraction |
| `n` | processor 수 |

**핵심 결론**: `n → ∞` 라도
```text
Speedup_max = 1 / s
```
예: serial fraction `s = 0.05` → processor를 무한히 늘려도 speedup은 **최대 20배**. `s = 0.2` → 최대 5배.

→ 병렬 컴퓨팅의 승부처는 serial fraction을 줄이는 것 (알고리즘·자료구조·동기화 설계).

---

## 4. Shared Memory와 Cache Coherence

Shared-memory multiprocessor: 여러 processor가 같은 memory address space 공유. 각 processor가 **private cache**를 가지면 같은 memory block의 복사본이 여러 cache에 존재.

**Cache coherence problem**:
```text
P0가 X에 새 값을 write → P0 cache만 갱신
P1 cache에는 X의 옛 값이 그대로 → P1이 stale data를 읽음
```

**Coherence의 비형식적 정의**: (1) P가 write 후 read하면 자기 write 값을 봄, (2) 다른 processor의 write도 충분한 시간 뒤엔 보임, (3) 같은 위치에 대한 write는 모든 processor에 같은 순서로 보임(write serialization).

### Write Invalidate vs Write Update

| | Write Invalidate | Write Update (broadcast) |
|---|---|---|
| write 시 동작 | 다른 cache의 copy를 **Invalid**로 | 새 값을 다른 cache에 **broadcast** |
| 같은 word 연속 write | traffic 적음 (한 번만 invalidate) | 매번 broadcast → traffic 많음 |
| reader 최신값 획득 | 다음 read에서 miss → 새로 fetch | 즉시 최신값 보유 |
| 주류 | ✅ 대부분의 실제 시스템 | 특수 상황 |

---

## 5. Snooping Protocol

Bus 기반 shared-memory에서 각 cache controller가 **bus transaction을 감시(snoop)**. 다른 processor의 read miss / write miss를 보고 자기 cache line state를 전이.

**기본 3-state (MSI)**:
- **Invalid** — 유효한 data 없음
- **Shared** — clean, 다른 cache와 공유 가능, read-only
- **Modified** — 이 cache만 최신 dirty copy 보유, memory는 stale

전이 예: Shared 상태에서 write → bus에 invalidate 방송 → 다른 copy Invalid, 자기 line Modified. 다른 processor가 그 block을 read miss → Modified 보유 cache가 data 공급(write-back) + Shared로.

---

## 6. MESI Protocol

MSI에 **Exclusive**를 추가한 4-state.

| State | 의미 |
|---|---|
| **Modified** | 이 cache만 dirty copy, memory stale |
| **Exclusive** | 이 cache만 clean copy, memory와 일치 |
| **Shared** | 여러 cache가 clean copy 공유 |
| **Invalid** | 유효하지 않음 |

**Exclusive의 이점**: 다른 cache에 없는 clean block을 read하면 Exclusive로 들어옴. 이후 그 block에 write할 때 **bus transaction(invalidate broadcast) 없이** 바로 Modified로 전이 → 단일 스레드 워크로드에서 불필요한 bus traffic 제거. (MSI에서는 Shared에서 write 시 항상 invalidate가 필요했음.)

---

## 7. Synchronization

공유 data를 여러 thread가 동시에 접근하면 **race condition**. Critical section은 **mutual exclusion** 보장 필요.

**Correctness 조건 (Dijkstra)**:
- **Mutual exclusion** — 동시에 최대 한 thread만 critical section
- **Progress** — 아무도 안 들어가 있으면 원하는 thread가 들어갈 수 있어야
- **Bounded waiting** — 무한정 기다리지 않음 (starvation 방지)

### Test-and-Set spin lock

```c
test_and_set(lock) {      // atomic
    old = lock;
    lock = true;
    return old;
}

acquire: while (test_and_set(lock) == true) ;   // spin
release: lock = false;
```

**문제**: naive spin lock은 매 반복마다 `test_and_set`이 write를 하므로 bus에 invalidate가 쏟아져 traffic 폭증.
**개선 — test-and-test-and-set**: 먼저 일반 read로 lock이 풀리길 local cache에서 spin(Shared 상태, bus traffic 없음), 풀린 것 같을 때만 atomic `test_and_set` 시도.
→ 이 외에도 exponential backoff, ticket lock, MCS lock, LL/SC(load-linked / store-conditional) 등.

---

## 8. Interconnection Networks

Multiprocessor 성능은 processor 간 연결망에 크게 의존. 평가 축: **bisection bandwidth, diameter(최대 hop), degree(node당 link 수), cost**.

| Network | 특징 | diameter |
|---|---|---|
| **Bus** | 단순·저비용, 한 번에 한 transaction | 1 (but 확장성 최악) |
| **Crossbar** | 모든 입출력 쌍 동시 연결 가능 | 1, 비용 O(N²) |
| **Omega / multistage** | log 단계 스위치, 비용·성능 절충 | O(log N), blocking 가능 |
| **Ring** | 저비용, 인접 연결 | O(N) |
| **Mesh (2D)** | 2D 칩 layout에 자연스러움 | O(√N) |
| **2D Torus** | mesh의 가장자리를 wrap-around 연결 → 균형 개선 | O(√N)/2 |
| **Hypercube** | n차원, 2ⁿ node, node당 n link | n = log₂N |

---

## 복습 질문

- Amdahl's law에서 serial fraction `s = 0.1` 일 때 이론상 최대 speedup은? 그 함의는?
- Write invalidate와 write update의 차이, 그리고 왜 invalidate가 주류인가?
- MESI에서 Exclusive state가 MSI 대비 절약해 주는 것은 구체적으로 무엇인가?
- naive test-and-set spin lock이 bus traffic을 폭증시키는 이유와, test-and-test-and-set의 개선점은?
- Hypercube에서 2ⁿ개 node의 diameter와 node당 link 수는?
{% endraw %}

---

이전: [02. Storage & I/O](02-storage-and-io.md) · 다음: [04. SSD 기본과 아키텍처](04-ssd-basics-and-architecture.md)
