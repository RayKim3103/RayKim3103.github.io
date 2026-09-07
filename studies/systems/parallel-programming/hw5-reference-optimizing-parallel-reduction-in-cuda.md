---
layout: page
title: "HW5. Reference - Optimizing Parallel Reduction in CUDA"
permalink: /studies/systems/parallel-programming/hw5-reference-optimizing-parallel-reduction-in-cuda/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW5%20Reference%20-%20Optimizing%20Parallel%20Reduction%20in%20CUDA.md)

{% raw %}
이전: [Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md)  
다음: [Assignment - Triton ResNet](hw6-assignment-triton-resnet.md)

## 핵심 요약

이 참고자료는 Mark Harris의 CUDA parallel reduction 최적화 자료다. Reduction kernel을 여러 version으로 발전시키며 memory bandwidth, divergent branching, bank conflict, loop unrolling, Brent's theorem 기반 cascading을 설명한다. HW5의 이론적 기준 자료다.

## Global Synchronization 문제

CUDA는 block 간 global synchronization을 kernel 내부에서 제공하지 않는다. 따라서 큰 배열 reduction은 block별 partial reduction을 수행한 뒤, 여러 kernel invocation으로 recursive하게 줄이는 방식이 필요하다.

## 성능 Metric

Reduction은 원소 하나를 읽고 덧셈 하나를 수행하는 낮은 arithmetic intensity를 가진다. 따라서 compute throughput보다 memory bandwidth가 적절한 성능 지표다.

## Version별 발전

| Version | 핵심 변화 | 효과 |
|---|---|---|
| reduce0 | interleaved addressing, divergent branch | baseline |
| reduce1 | modulo 제거, index 계산 | branch/instruction overhead 감소 |
| sequential addressing | stride 감소 방향 변경 | bank conflict와 divergence 완화 |
| first add during load | global load 시 두 원소 합산 | idle thread 감소 |
| last warp unroll | 마지막 warp에서 sync 제거 | loop/sync overhead 감소 |
| complete unroll | template block size | compile-time 최적화 |
| multiple adds per thread | thread당 여러 원소 처리 | Brent's theorem, cost efficiency |

## Warp Reduce

마지막 32개 thread만 남으면 warp 내부 reduction을 unroll한다.

```cpp
sdata[tid] += sdata[tid + 32];
sdata[tid] += sdata[tid + 16];
sdata[tid] += sdata[tid + 8];
sdata[tid] += sdata[tid + 4];
sdata[tid] += sdata[tid + 2];
sdata[tid] += sdata[tid + 1];
```

이 방식은 instruction overhead를 줄이지만, 최신 independent thread scheduling에서는 warp 동기화 안전성을 고려해야 한다.

## Template Unrolling

`blockSize`를 template parameter로 넘기면 compiler가 compile time에 branch를 평가하고 불필요한 코드를 제거한다.

```cpp
template <unsigned int blockSize>
__global__ void reduce(...)
```

Runtime에는 switch문으로 block size별 specialization을 호출한다.

## Parallel Complexity

Reduction은 work complexity가 `O(N)`이어야 sequential algorithm과 같은 총 작업량을 가진다. 단순히 `O(log N)` parallel step을 얻기 위해 너무 많은 processor/thread를 쓰면 cost가 `O(N log N)`이 되어 비효율적일 수 있다.

Brent's theorem은 각 thread가 `O(log N)` 정도의 sequential work를 하고, 전체 thread 수를 줄여 cost efficiency를 높이는 방향을 제시한다.

## Algorithmic vs Code Optimization

자료는 algorithmic optimization이 loop unrolling 같은 code optimization보다 더 큰 speedup을 만들 수 있음을 보여준다.

- Algorithmic: addressing 변경, cascading
- Code: loop unrolling, template specialization

## 정리

이 참고자료의 핵심은 CUDA 성능 최적화가 병목 유형에 따라 달라진다는 점이다. Reduction은 memory-bound이고 instruction overhead도 중요하므로, memory access pattern과 algorithmic work efficiency를 먼저 잡고, 이후 unrolling 같은 code optimization을 적용해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW5. Reference - Optimizing Parallel Reduction in CUDA**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 병렬 프로그래밍 주제에서는 thread mapping, memory hierarchy, synchronization, profiling metric을 같은 표에 놓고 본다.
- GPU 최적화는 correctness baseline을 고정한 뒤 coalescing, tiling, occupancy, register pressure를 하나씩 바꾸며 측정한다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 병렬화는 일을 나누는 것뿐 아니라 memory hierarchy, synchronization, occupancy, load balance를 맞추는 문제다.

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
- **HW5. Reference - Optimizing Parallel Reduction in CUDA**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW5. Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md) · 다음: [HW6. Assignment - Triton ResNet](hw6-assignment-triton-resnet.md)
