---
layout: page
title: "10. Prefix Sum - GPU Scan 알고리즘"
permalink: /studies/systems/parallel-programming/10-prefix-sum-gpu-scan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/10%20Prefix%20Sum%20-%20GPU%20Scan%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98.md)

{% raw %}
이전: [CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md)  
다음: [Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)

## 핵심 요약

Prefix sum 또는 scan은 배열의 각 위치에 그 앞 원소들의 누적값을 저장하는 병렬 primitive다. Quicksort partition, histogram, polynomial evaluation, radix sort, regular expression search 등 다양한 알고리즘의 building block이다.

## Reduce와 Prefix Sum

| 연산 | 결과 |
|---|---|
| Reduce | 전체 배열을 하나의 값으로 결합 |
| Prefix sum | 각 index별 partial accumulation을 모두 계산 |

예를 들어 `[1,2,3,4]`의 inclusive scan은 `[1,3,6,10]`이고, exclusive scan은 `[0,1,3,6]`이다.

## Naive Parallel Prefix Sum

각 단계마다 거리 `1, 2, 4, ...`의 neighbor를 더한다. Hillis-Steele 또는 Kogge-Stone 형태다.

장점:

- `O(log N)` 단계
- 병렬성이 매우 큼

단점:

- 전체 work가 `O(N log N)`으로 work-efficient하지 않다.
- 각 단계마다 barrier가 필요하다.

## Kogge-Stone Algorithm

Kogge-Stone은 가까운 neighbor부터 점점 먼 neighbor까지 더한다.

```text
step 1: i >= 1이면 a[i] += a[i-1]
step 2: i >= 2이면 a[i] += a[i-2]
step 4: i >= 4이면 a[i] += a[i-4]
...
```

특징:

- 빠른 depth: `O(log N)`
- 많은 연산량
- 많은 thread가 계속 active

## Brent-Kung Algorithm

Brent-Kung은 balanced binary tree pattern을 사용한다.

두 phase:

1. Up-sweep 또는 reduce phase: tree 위로 partial sum 생성
2. Down-sweep phase: prefix 값을 아래로 전파

특징:

- 시간: 대략 `2 log N`
- work: `O(N)`
- Kogge-Stone보다 단계는 많지만 계산량이 적다.

## Kogge-Stone vs Brent-Kung

| 항목 | Kogge-Stone | Brent-Kung |
|---|---|---|
| Time complexity | `log N` | `2 log N` |
| Work | `O(N log N)` | `O(N)` |
| 장점 | 짧은 latency | work-efficient |
| 단점 | 연산량 많음 | 단계 수 많음 |

## GPU 구현 포인트

- Shared memory에 block 내부 데이터를 올린다.
- 단계마다 `__syncthreads()`를 사용한다.
- Exclusive scan이면 입력을 한 칸 shift하고 첫 값을 identity로 둔다.
- 큰 배열은 block별 scan 후 block sum을 다시 scan하고 각 block에 더한다.
- Shared memory tree access에서 bank conflict가 생길 수 있어 padding이 필요하다.

## Lesson

강의의 마지막 메시지는 lock 기반 누적보다 병렬 알고리즘 구조를 찾는 것이 더 좋다는 것이다. Prefix sum은 단순해 보이지만 많은 병렬 알고리즘의 기반이며, scan을 잘 구현하면 여러 상위 문제를 효율적으로 풀 수 있다.

## 정리

Prefix sum은 reduce보다 더 많은 정보를 유지하는 누적 연산이다. GPU에서는 Kogge-Stone과 Brent-Kung의 depth/work tradeoff, shared memory bank conflict, 큰 배열 decomposition이 핵심이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Prefix Sum - GPU Scan 알고리즘**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **10. Prefix Sum - GPU Scan 알고리즘**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md) · 다음: [10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)
