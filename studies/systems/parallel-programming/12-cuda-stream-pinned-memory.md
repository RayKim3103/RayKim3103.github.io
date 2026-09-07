---
layout: page
title: "12. CUDA Stream - Pinned Memory와 비동기 파이프라인"
permalink: /studies/systems/parallel-programming/12-cuda-stream-pinned-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/12%20CUDA%20Stream%20-%20Pinned%20Memory%EC%99%80%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8.md)

{% raw %}
이전: [Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)  
다음: [CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)

## 핵심 요약

이 강의는 host-device memory copy 병목을 줄이기 위한 pinned memory와 CUDA stream을 다룬다. GPU kernel이 빠르더라도 PCIe/NVLink를 통한 H2D/D2H copy가 오래 걸리면 전체 시간이 copy에 지배된다. Pinned memory와 `cudaMemcpyAsync`, multiple stream을 사용하면 copy와 kernel 실행을 overlap하여 pipeline을 만들 수 있다.

## Memcpy 병목

8192x8192 transpose 예시에서 kernel 자체는 약 2.3ms인데, host-device copy는 각각 44ms 수준으로 더 크다. 이 경우 kernel만 최적화해도 전체 실행시간은 크게 줄지 않는다.

## Pinned Memory

Pinned memory는 OS가 page out하지 않도록 고정한 host memory다. GPU DMA가 안정적으로 접근할 수 있어 pageable memory보다 transfer가 빠르다.

사용 API:

```cpp
cudaMallocHost(&h_ptr, bytes);
```

또는 기존 malloc memory를 등록:

```cpp
h_ptr = malloc(bytes);
cudaHostRegister(h_ptr, bytes, 0);
```

예시에서는 H2D/D2H copy가 44ms에서 20ms 정도로 줄어드는 효과가 제시된다.

## Stream

CUDA stream은 비동기 작업 queue다. 서로 다른 stream의 copy와 kernel은 resource가 겹치지 않으면 overlap될 수 있다.

```cpp
cudaStream_t stream1, stream2;
cudaStreamCreate(&stream1);
cudaStreamCreate(&stream2);
```

Kernel launch도 stream을 지정할 수 있다.

```cpp
MatMulKernel<<<dimGrid, dimBlock, 0, stream1>>>(...);
```

## Pipeline 구조

여러 matrix를 처리할 때 각 matrix에 대해 다음 흐름을 하나의 stream에 배치한다.

```text
H2D copy -> kernel -> D2H copy
```

그리고 여러 stream을 동시에 사용하면:

```text
stream1: M1 H2D -> M1 kernel -> M1 D2H
stream2: M2 H2D -> M2 kernel -> M2 D2H
stream3: M3 H2D -> M3 kernel -> M3 D2H
stream4: M4 H2D -> M4 kernel -> M4 D2H
```

Copy engine과 compute engine이 동시에 일할 수 있으면 전체 throughput이 좋아진다.

## NVVP 확인

NVIDIA Visual Profiler에서는 timeline을 통해 stream별 H2D, kernel, D2H가 실제로 overlap되는지 확인할 수 있다. Zoom in해서 copy와 kernel 사이의 빈 공간이 줄었는지 보는 것이 중요하다.

## 정리

CUDA stream 최적화는 kernel 내부 최적화가 아니라 application-level scheduling 최적화다. Copy가 kernel보다 훨씬 긴 workload에서는 pinned memory와 async stream pipeline이 전체 성능을 크게 좌우한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. CUDA Stream - Pinned Memory와 비동기 파이프라인**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **12. CUDA Stream - Pinned Memory와 비동기 파이프라인**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11. Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md) · 다음: [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)
