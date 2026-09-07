---
layout: page
title: "12. CUDA Stream Updated - 업데이트본 요약"
permalink: /studies/systems/parallel-programming/12-cuda-stream-updated/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/12%20CUDA%20Stream%20Updated%20-%20%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8%EB%B3%B8%20%EC%9A%94%EC%95%BD.md)

{% raw %}
이전: [CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md)  
다음: [CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)

## 핵심 요약

이 업데이트본은 CUDA stream 강의와 같은 주제인 pinned memory, asynchronous copy, stream pipeline, NVVP timeline 확인을 다룬다. 추출된 내용 기준으로 기존 stream 자료와 구조가 거의 같으므로, 이 노트는 업데이트본에서 확인해야 할 실무 포인트를 중심으로 정리한다.

## 확인해야 할 핵심 개념

| 개념 | 내용 |
|---|---|
| Pageable memory | 일반 host memory, DMA 전송 전 staging 비용 가능 |
| Pinned memory | page-locked host memory, H2D/D2H 전송 가속 |
| `cudaMemcpyAsync` | stream에 비동기 copy enqueue |
| CUDA stream | 작업 순서를 보존하는 비동기 queue |
| Pipeline | copy와 kernel을 겹쳐 전체 처리량 향상 |

## Pinned Memory API

```cpp
cudaMallocHost(&h_ptr, bytes);
cudaHostRegister(h_ptr, bytes, 0);
```

Pinned memory는 많이 쓰면 OS memory management에 부담이 되므로 필요한 buffer에 제한적으로 사용해야 한다.

## Stream Pipeline 작성 패턴

각 stream 안에서는 순서가 보장된다.

```cpp
cudaMemcpyAsync(d_in, h_in, size, cudaMemcpyHostToDevice, stream);
kernel<<<grid, block, 0, stream>>>(d_in, d_out);
cudaMemcpyAsync(h_out, d_out, size, cudaMemcpyDeviceToHost, stream);
```

여러 stream 간에는 독립적으로 실행될 수 있으므로, data chunk 또는 여러 matrix를 stream별로 분배하면 overlap이 가능하다.

## 업데이트본 관점의 체크리스트

- Host buffer가 pinned memory인지 확인한다.
- `cudaMemcpyAsync`에 stream argument를 명시한다.
- Kernel launch에도 같은 stream을 지정해 한 data chunk의 순서를 유지한다.
- 불필요한 global device synchronization을 줄인다.
- NVVP 또는 profiler timeline에서 실제 overlap을 확인한다.

## 정리

업데이트본의 핵심은 stream 최적화를 코드에 넣었다고 끝나는 것이 아니라, profiler timeline에서 copy/compute overlap이 실제 발생하는지 검증해야 한다는 점이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. CUDA Stream Updated - 업데이트본 요약**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **12. CUDA Stream Updated - 업데이트본 요약**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md) · 다음: [13. CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)
