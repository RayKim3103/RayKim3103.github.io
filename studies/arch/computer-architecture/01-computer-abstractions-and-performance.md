---
layout: page
title: "01. Computer Abstractions & Performance"
permalink: /studies/arch/computer-architecture/01-computer-abstractions-and-performance/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `01` 정리·보강
- 교재: Patterson & Hennessy ch 1. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

컴퓨터를 application에서 transistor까지 **abstraction layer**로 나눠 보고, **성능·전력·에너지 효율**을 정량화한다.

## 1. Abstraction Layers

```
application → algorithm → programming language → compiler → operating system
   → ISA → microarchitecture → RTL → circuit → device → material
```
- **ISA (Instruction Set Architecture)**: software가 hardware를 쓰는 계약. assembly instruction이 이 interface의 구체적 표현. 예: RISC-V, x86, ARM.
- **microarchitecture**: 같은 ISA를 구현하는 방식(single-cycle vs pipelined vs OoO 등). ISA는 같아도 μarch는 다를 수 있다.
- computing system: desktop / server / mobile / embedded / supercomputer — workload와 제약(전력·비용·실시간성)이 다름.

## 2. 성능 정의

| | 관점 | 누가 중시 |
|---|---|---|
| **response time (latency)** | 작업 하나가 끝나는 시간 | 개인 사용자 |
| **throughput (bandwidth)** | 단위 시간당 작업 수 | 서버 운영자 |

두 향상은 **같은 말이 아니다**(코어를 늘리면 throughput↑이지만 단일 작업 latency는 그대로일 수 있다).

## 3. CPU Time 식 — 핵심

$$
\text{CPU time}
= \text{Instruction Count} \times \text{CPI} \times \text{Clock Cycle Time}
= \frac{\text{IC} \times \text{CPI}}{\text{Clock Rate}}
$$

| 항 | 영향을 주는 것 |
|---|---|
| **IC** | ISA, compiler, 알고리즘 |
| **CPI** (cycles per instruction) | μarch, ISA, memory/branch stall |
| **Clock rate** | μarch, 공정, 회로 |

$$
\text{CPI}_{\text{avg}} = \sum_i \big(\text{CPI}_i \times \text{fraction}_i\big)
$$
> clock rate만 올려도 CPI나 IC가 나빠지면 프로그램이 빨라지지 않는다. pipeline·cache 강의는 **CPI의 stall 항**을 줄이는 방법이다.

## 4. Power와 Energy

$$
\text{Power} = \frac{\text{Energy}}{\text{Time}}
$$
- **power**: 순간 소비율(W). 발열·냉각 한계.
- **energy**: 일정 시간 동안 소비한 총량(J). 배터리·전기요금.
- CMOS dynamic power $$\propto C \cdot V^2 \cdot f$$. → 전압을 못 낮추면서 주파수만 올리면 power가 급증(**power wall**) → multicore로 전환한 이유.
- 데이터센터·모바일: **performance per watt**가 핵심 지표.

## 5. Amdahl's Law

$$
\text{Speedup} = \frac{1}{(1-f) + \dfrac{f}{s}}
$$
$$f$$ = 개선되는 비율, $$s$$ = 그 부분의 속도 향상. **개선되지 않는 부분 $$(1-f)$$ 이 전체 speedup의 상한을 결정한다** ($$s\to\infty$$ 이면 speedup $$\to 1/(1-f)$$).

## 6. 자주 틀리는 지점

- clock rate↑ 인데 CPI·IC가 나빠지면 성능↓ 가능.
- throughput 향상 ≠ response time 향상.
- Amdahl: 자주 쓰이는 부분(common case)을 빠르게 — 드문 부분을 최적화해도 총 이득이 작다.
- power와 energy 혼동 시 모바일/서버 효율 평가가 틀어진다.

## 복습 질문

- CPU time 식의 세 항과, 각각에 영향을 주는 요소는?
- response time과 throughput의 차이를 예로 설명하라.
- Amdahl's law를 쓰고, $$f=0.9,\ s\to\infty$$ 일 때 최대 speedup은?
- power wall이 multicore 전환을 유발한 이유는?
{% endraw %}

---

이전: [00. 강의 개요](00-course-overview.md) · 다음: [02. Instructions & RISC-V ISA](02-instructions-and-risc-v-isa.md)
