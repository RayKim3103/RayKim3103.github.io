---
layout: page
title: "01. Basic Parallel Architectures - 기본 병렬 아키텍처"
permalink: /studies/systems/parallel-programming/01-basic-parallel-architectures/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/01%20Basic%20Parallel%20Architectures%20-%20%EA%B8%B0%EB%B3%B8%20%EB%B3%91%EB%A0%AC%20%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.md)

{% raw %}
이전: [Course Overview - 병렬 프로그래밍 개요](00-course-overview.md)  
다음: [Thread Programming - C++ Thread와 동기화](02-thread-programming-c.md)

## 핵심 요약

이 강의는 병렬 컴퓨터 구조를 SISD, MIMD, SIMD 관점으로 소개한다. 핵심은 병렬성이 여러 층위에 존재한다는 점이다. Superscalar CPU는 명령어 수준 병렬성(ILP)을 자동으로 찾고, multi-core CPU는 thread 수준 병렬성을 요구하며, vector processor는 하나의 명령으로 여러 데이터를 처리한다.

## 병렬성의 층위

| 구조 | 분류 | 병렬성 | 프로그래머 관점 |
|---|---|---|---|
| Scalar / Superscalar CPU | SISD | ILP | 대부분 자동 |
| Multi-core CPU | MIMD | thread-level parallelism | thread 또는 OpenMP로 명시 |
| Vector processor | SIMD | data-level parallelism | compiler vectorization 또는 intrinsic |

## ILP와 Superscalar

Processor는 원래 program counter가 가리키는 instruction을 순서대로 실행하는 기계다. Superscalar processor는 여러 execution unit을 사용해 서로 독립적인 instruction을 같은 cycle에 실행한다.

예를 들어 `x*x`, `y*y`, `z*z`는 서로 독립적이면 동시에 실행될 수 있다. 그러나 dependency가 있는 instruction은 앞 instruction 결과를 기다려야 한다. 따라서 ILP에는 프로그램 내부 dependency가 만드는 한계가 있다.

## Multi-core Processor

Multi-core는 core를 여러 개 두어 서로 다른 instruction stream을 동시에 실행한다. 각 core 하나의 frequency가 single-core보다 낮을 수 있어도, 여러 core를 잘 활용하면 총 처리량이 증가한다.

하지만 기존 single-thread loop는 자동으로 여러 core에서 실행되지 않는다. multi-core를 쓰려면 thread로 일을 나누거나, OpenMP 같은 data parallel 표현을 사용해야 한다.

## Thread

Process는 실행 중인 프로그램 전체를 뜻하고 code, data, stack, register, PC 등을 가진다. Thread는 process 안의 실행 흐름 단위다. 같은 process의 thread들은 주소 공간을 공유하면서 각자 PC, register, stack을 가진다.

예시:

- 배열 `c[i] = k[i] * a[i] + k[i] * b[i]`는 각 `i`가 독립적이면 thread별로 index 범위를 나누기 좋다.
- loop iteration 사이 dependency가 없으면 data parallelism이 있다.

## Vector Processing

Vector processor는 하나의 instruction으로 여러 데이터를 동시에 처리한다. SSE, AVX2, AVX-512는 register 폭이 다르다.

| ISA | register 폭 | 32bit float 처리량 |
|---|---:|---:|
| SSE | 128bit | 4개 |
| AVX2 | 256bit | 8개 |
| AVX-512 | 512bit | 16개 |

Compiler가 vectorization을 자동 수행할 수도 있고, `<immintrin.h>` 같은 intrinsic을 직접 사용할 수도 있다.

## 조건문과 Predication

SIMD 구조에서 lane마다 branch 방향이 다르면 모든 lane을 같은 instruction stream으로 처리하기 어렵다. 이때 predication으로 조건별 결과를 mask 처리할 수 있지만, 실제 계산 자원 활용률이 떨어질 수 있다. 이 개념은 CUDA warp divergence와 직접 연결된다.

## 용어 정리

| 용어 | 의미 |
|---|---|
| SISD | Single Instruction, Single Data |
| SIMD | Single Instruction, Multiple Data |
| MIMD | Multiple Instruction, Multiple Data |
| ILP | Instruction Level Parallelism |
| TLP | Thread Level Parallelism |
| DLP | Data Level Parallelism |

## 정리

이 강의의 핵심은 병렬화를 하나의 기법으로 보지 않고, 하드웨어 계층별 병렬성으로 보는 것이다. Superscalar는 자동, multi-core는 thread 분할, SIMD는 데이터 벡터화가 중심이며, 이후 CUDA warp와 GPU thread block도 이 관점의 확장으로 이해할 수 있다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Basic Parallel Architectures - 기본 병렬 아키텍처**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 병렬 프로그래밍 주제에서는 thread mapping, memory hierarchy, synchronization, profiling metric을 같은 표에 놓고 본다.
- GPU 최적화는 correctness baseline을 고정한 뒤 coalescing, tiling, occupancy, register pressure를 하나씩 바꾸며 측정한다.
- 병렬화는 일을 나누는 것뿐 아니라 memory hierarchy, synchronization, occupancy, load balance를 맞추는 문제다.
- CUDA kernel 성능은 global memory coalescing, shared memory bank conflict, register pressure, occupancy가 함께 결정한다.
- Triton과 GPU library도 tile size, data layout, fusion, bandwidth 병목을 이해해야 잘 쓸 수 있다.

### 문제 풀이 또는 구현 루틴

- arithmetic intensity를 계산해 compute-bound인지 memory-bound인지 가른다.
- thread/block mapping, memory access pattern, shared memory reuse, synchronization 지점을 표로 적는다.
- 최적화 전후에는 correctness check, timing methodology, profiler metric을 같이 남긴다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- thread 수를 늘리는 것만으로 speedup이 선형 증가하지 않는다.
- shared memory를 쓰면 항상 빠른 것이 아니라 bank conflict와 load overhead를 함께 봐야 한다.
- 비동기 stream/memory copy는 dependency를 명확히 기록하지 않으면 race처럼 보이는 오류가 생긴다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 병목은 연산량, memory bandwidth, synchronization, launch overhead 중 무엇인가?
- 각 thread가 담당하는 데이터와 인접 thread의 memory access가 coalesced 되는가?
- 최적화가 정확도를 유지한다는 검증과 성능 개선 근거가 함께 있는가?
- **01. Basic Parallel Architectures - 기본 병렬 아키텍처**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. Course Overview - 병렬 프로그래밍 개요](00-course-overview.md) · 다음: [02. Thread Programming - C++ Thread와 동기화](02-thread-programming-c.md)
