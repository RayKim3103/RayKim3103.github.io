---
layout: page
title: "09. CUDA Others - TensorCore와 CUDA Libraries"
permalink: /studies/systems/parallel-programming/09-cuda-others-tensorcore-libraries/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/09%20CUDA%20Others%20-%20TensorCore%EC%99%80%20CUDA%20Libraries.md)

{% raw %}
이전: [CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)  
다음: [Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)

## 핵심 요약

이 강의는 TensorCore, CUDA library, 그리고 최신 GPU에서 기존 설명이 단순화였던 부분들을 보완한다. 핵심은 모든 kernel을 직접 작성할 필요는 없으며, GEMM/DNN/parallel primitive는 cuBLAS, cuDNN, Thrust 같은 library가 매우 강력하다는 점이다.

## TensorCore

Matrix multiplication은 작은 matrix multiply-accumulate의 반복으로 볼 수 있다. TensorCore는 이런 작은 matrix 연산을 hardware에서 매우 빠르게 처리하는 특수 unit이다.

특징:

- 작은 tile 단위 matrix multiply-accumulate 수행
- 16bit multiplication과 32bit accumulation 지원
- GPU 세대별 지원 precision이 다름
- deep learning GEMM/conv 성능의 핵심 hardware

## CUDA Libraries

| Library | 역할 |
|---|---|
| cuBLAS | BLAS, GEMM/GEMV 등 dense linear algebra |
| cuDNN | convolution, pooling, normalization 등 DNN primitive |
| Thrust | C++ STL 스타일 parallel algorithms |

직접 kernel을 쓰는 것은 학습과 특수 최적화에는 중요하지만, 실제 production에서는 library kernel이 더 빠르고 안정적인 경우가 많다.

## Independent Thread Scheduling

초기 CUDA 설명에서는 warp가 lockstep으로 움직인다고 단순화한다. 하지만 Volta 이후 GPU는 thread마다 program counter를 갖고 더 독립적으로 scheduling될 수 있다.

결과:

- 더 유연한 scheduling 가능
- 일부 warp-synchronous trick이 더 이상 안전하지 않을 수 있음
- warp 내부 통신이나 reduction에서는 `__syncwarp()` 필요 가능

## Large L2 Cache

최신 GPU는 더 큰 L2 cache를 가진다. 예를 들어 A100은 40MB L2 cache를 갖지만 SM 수로 나누면 SM당 절대적으로 무한히 큰 것은 아니다. 그래도 높은 bandwidth와 cache 효과는 성능 분석에서 중요하다.

## 정리

이 강의의 핵심은 “직접 kernel 작성”과 “library 사용”의 균형이다. CUDA를 잘하려면 low-level 최적화를 이해해야 하지만, 동시에 TensorCore와 cuBLAS/cuDNN/Thrust 같은 검증된 고성능 primitive를 언제 사용할지 판단할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. CUDA Others - TensorCore와 CUDA Libraries**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **09. CUDA Others - TensorCore와 CUDA Libraries**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md) · 다음: [10. Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)
