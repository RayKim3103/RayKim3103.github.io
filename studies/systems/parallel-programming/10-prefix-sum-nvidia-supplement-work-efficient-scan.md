---
layout: page
title: "10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan"
permalink: /studies/systems/parallel-programming/10-prefix-sum-nvidia-supplement-work-efficient-scan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/10%20Prefix%20Sum%20NVIDIA%20Supplement%20-%20Work-Efficient%20Scan.md)

{% raw %}
이전: [Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)  
다음: [Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)

## 핵심 요약

이 보충자료는 NVIDIA의 Parallel Prefix Sum 문서로, CUDA에서 scan을 효율적으로 구현하는 과정을 설명한다. Naive scan의 `O(N log N)` work를 피하기 위해 Blelloch scan의 up-sweep/down-sweep tree 구조를 사용하고, shared memory bank conflict를 padding으로 줄이며, 큰 배열은 block 단위 scan과 block sum scan으로 확장한다.

## Prefix Sum 정의

Associative operator `⊕`와 identity `I`가 있을 때:

- Inclusive scan: `[a0, a0⊕a1, ..., a0⊕...⊕an]`
- Exclusive scan: `[I, a0, a0⊕a1, ..., a0⊕...⊕a(n-1)]`

Scan은 sorting, stream compaction, data structure construction 등에서 핵심 primitive로 사용된다.

## Naive Scan의 한계

Hillis-Steele 형태의 naive scan은 `log N` 단계만 필요하지만, 각 단계마다 거의 N개 연산을 수행하므로 work가 `O(N log N)`이다. Sequential scan이 `O(N)`임을 생각하면 work-efficient하지 않다.

## Work-Efficient Scan

Blelloch scan은 balanced tree pattern을 사용한다.

### Up-Sweep

Tree의 leaf에서 root 방향으로 partial sum을 만든다. 마지막 root에는 전체 합이 저장된다.

### Down-Sweep

Root를 identity로 바꾼 뒤, tree를 내려오면서 prefix 값을 전파한다. 이 과정을 통해 exclusive scan 결과를 얻는다.

## CUDA Shared Memory 구현

Block 내부에서 각 thread가 보통 두 원소를 shared memory에 load한다. 이후 up-sweep과 down-sweep loop를 수행한다.

중요한 점:

- Shared memory access index가 tree pattern으로 변한다.
- 각 phase 사이에 `__syncthreads()`가 필요하다.
- Power-of-two 크기에서 설명이 단순하지만 임의 크기는 padding 또는 boundary 처리가 필요하다.

## Bank Conflict 제거

Tree access pattern은 같은 bank에 여러 thread가 몰리기 쉽다. NVIDIA 문서는 conflict-free offset macro를 사용해 shared memory index에 padding을 더한다.

아이디어:

```text
physical_index = logical_index + conflict_free_offset(logical_index)
```

이렇게 하면 stride가 bank 수와 정렬되어 생기는 conflict를 분산할 수 있다.

## Arbitrary Size Array

한 block이 처리할 수 있는 크기를 넘는 배열은 다음 순서로 처리한다.

1. 각 block이 자기 구간을 scan한다.
2. 각 block의 total sum을 별도 배열에 저장한다.
3. block sum 배열을 다시 scan한다.
4. 각 block 결과에 앞 block들의 prefix sum을 더한다.

이 decomposition은 block 간 global sync가 없는 CUDA에서 큰 scan을 구현하는 표준 방식이다.

## 성능 관점

보충자료는 GPU scan이 CPU scan보다 큰 speedup을 낼 수 있음을 보인다. 다만 단순히 병렬화만 해서는 충분하지 않고, work efficiency와 bank conflict 제거가 함께 필요하다.

## 정리

이 보충자료의 핵심은 efficient scan이 알고리즘과 하드웨어 최적화를 동시에 요구한다는 점이다. `O(N)` work의 Blelloch scan, shared memory padding, block decomposition을 합쳐야 큰 배열에서도 빠른 CUDA scan이 된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md) · 다음: [11. Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)
