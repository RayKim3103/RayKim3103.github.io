---
layout: page
title: "14. Multi GPU - 단일 노드와 MPI"
permalink: /studies/systems/parallel-programming/14-multi-gpu-mpi/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/14%20Multi%20GPU%20-%20%EB%8B%A8%EC%9D%BC%20%EB%85%B8%EB%93%9C%EC%99%80%20MPI.md)

{% raw %}
이전: [CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)  
다음: [More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md)

## 핵심 요약

이 강의는 하나의 GPU를 넘어 single-node multi-GPU와 multi-node multi-GPU를 다룬다. 단일 노드에서는 `cudaSetDevice`, peer copy, unified addressing, event를 사용하고, 여러 노드에서는 distributed memory model과 MPI message passing이 중심이 된다.

## 왜 Multi-GPU인가

- 단일 GPU memory 또는 compute가 부족할 수 있다.
- DNN training은 큰 data/model 때문에 여러 GPU가 필요하다.
- Vector addition처럼 쉽게 분할 가능한 작업은 GPU별로 나누기 좋다.
- Server/cluster 환경에서는 여러 GPU와 여러 node를 동시에 활용한다.

## Single-Node Multi-GPU

`cudaSetDevice()`로 현재 host thread가 사용할 GPU를 선택한다.

```cpp
cudaSetDevice(0);
// GPU 0 allocation/copy/kernel

cudaSetDevice(1);
// GPU 1 allocation/copy/kernel
```

비동기 copy와 stream을 함께 사용하면 한 GPU가 copy 중일 때 다른 GPU 작업을 진행할 수 있다.

## Unified Addressing

CUDA 4.0 이후 CPU와 GPU들이 private virtual memory address region을 가진 unified virtual addressing을 지원한다. Pointer 값만으로 어느 memory space인지 구분하기 쉬워지고, peer access와 runtime 관리가 단순해진다.

## GPU-GPU Communication과 Topology

GPU 간 통신 성능은 topology에 영향을 받는다.

- 같은 PCIe switch 아래 있는 GPU
- CPU socket을 가로질러 통신하는 GPU
- NVLink 연결 여부
- topology conflict로 인한 slow path

Multi-GPU 성능은 compute 분할뿐 아니라 data movement 경로가 중요하다.

## CUDA Events

Event는 timing 측정뿐 아니라 stream/device 작업 완료를 기다리는 데도 사용한다.

용도:

- kernel execution time 측정
- 특정 stream 작업 완료 확인
- GPU 간 dependency 표현

## Multi-Node Multi-GPU

여러 서버를 함께 쓰면 shared memory model만으로는 확장하기 어렵다. 각 node는 private memory를 가지며, network를 통해 data를 교환한다. 이것이 distributed memory model이다.

## Message Passing

Message passing에서는 data 교환이 명시적 send/receive로 이루어진다. 두 process가 matching되는 send/recv 호출을 해야 통신이 성립한다.

## MPI 기본

주요 함수:

| 함수 | 역할 |
|---|---|
| `MPI_Init` | MPI 환경 초기화 |
| `MPI_Comm_size` | rank 수 확인 |
| `MPI_Comm_rank` | 현재 process rank 확인 |
| `MPI_Send` | message 전송 |
| `MPI_Recv` | message 수신 |

`MPI_Send`는 buffer, count, datatype, destination, tag, communicator를 받는다. `MPI_Recv`는 source, tag, status를 통해 수신 정보를 얻는다.

## GPU와 MPI

현대 MPI/CUDA 환경에서는 host/device memory copy가 모두 가능하며, GPU buffer를 MPI 통신에 직접 사용할 수 있는 기능도 발전했다. 과거에는 host staging이 필요했지만, 최신 환경에서는 GPU-aware MPI가 성능을 개선할 수 있다.

## 정리

Multi-GPU는 병렬화를 한 단계 확장하지만 data movement가 더 중요해진다. 단일 노드는 device selection, stream, peer communication, event가 핵심이고, multi-node는 distributed memory와 MPI message passing을 이해해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14. Multi GPU - 단일 노드와 MPI**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **14. Multi GPU - 단일 노드와 MPI**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md) · 다음: [15. More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md)
