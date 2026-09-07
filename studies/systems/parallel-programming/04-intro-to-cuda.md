---
layout: page
title: "04. Intro to CUDA - CUDA 프로그래밍 모델"
permalink: /studies/systems/parallel-programming/04-intro-to-cuda/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/04%20Intro%20to%20CUDA%20-%20CUDA%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%20%EB%AA%A8%EB%8D%B8.md)

{% raw %}
이전: [Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md)  
다음: [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)

## 핵심 요약

이 강의는 NVIDIA GPU를 C/C++ 기반 CUDA로 프로그래밍하는 기본 흐름을 다룬다. host와 device memory를 구분하고, `cudaMalloc`, `cudaMemcpy`, kernel launch, grid/block/thread index 계산, warp scheduling, branch divergence, latency hiding, occupancy를 이해하는 것이 핵심이다.

## CPU와 GPU 역할

| 장치 | 잘하는 일 |
|---|---|
| CPU | branch 많고 control-heavy한 작업, random access, OS 제어 |
| GPU | 대량의 data-parallel 작업, 같은 연산을 많은 데이터에 반복 |

실제 프로그램은 CPU와 GPU를 함께 사용한다. CPU host가 device memory를 할당하고 데이터를 복사한 뒤 kernel을 launch하고, GPU가 대규모 병렬 계산을 수행한다.

## CUDA 기본 흐름

1. Host memory에 입력 준비
2. `cudaMalloc`으로 device memory 할당
3. `cudaMemcpy(..., cudaMemcpyHostToDevice)`로 입력 복사
4. Kernel launch
5. `cudaMemcpy(..., cudaMemcpyDeviceToHost)`로 결과 복사
6. Device memory 해제

```cpp
vecAddKernel<<<ceil(N / 256.0), 256>>>(A_d, B_d, C_d, N);
```

위 launch는 block당 256 threads를 사용하고, 전체 N개 element를 덮을 만큼 grid를 만든다.

## Kernel과 Index

```cpp
__global__
void vecAddKernel(float* A, float* B, float* C, int n) {
    int i = threadIdx.x + blockDim.x * blockIdx.x;
    if (i < n) C[i] = A[i] + B[i];
}
```

`__global__` 함수는 host에서 호출되고 device에서 실행된다. `threadIdx`, `blockIdx`, `blockDim`, `gridDim`으로 각 thread의 전역 index를 계산한다.

## Grid와 Block

| 개념 | 의미 |
|---|---|
| Thread | 실제 작업 단위 |
| Thread block | 함께 scheduling되며 shared memory와 `__syncthreads()`를 공유하는 단위 |
| Grid | kernel launch 전체 block 집합 |
| Warp | SM 내부 scheduling 단위, 32 threads |

Block size는 보통 warp size의 배수로 잡는다. 최대 thread block size는 일반적으로 1024이다.

## 2D 이미지 예시

Color to grayscale, image blur 같은 image kernel은 2D grid/block을 사용한다.

```cpp
int Col = threadIdx.x + blockIdx.x * blockDim.x;
int Row = threadIdx.y + blockIdx.y * blockDim.y;
```

Boundary check는 이미지 크기가 block tile로 나누어떨어지지 않을 때 out-of-bounds 접근을 막는다.

## CUDA Function Qualifier

| qualifier | 호출 위치 | 실행 위치 |
|---|---|---|
| `__host__` | host | host |
| `__global__` | host | device |
| `__device__` | device | device |

## SM과 Warp Scheduling

SM(Streaming Multiprocessor)은 block을 받아 warp 단위로 실행한다. Warp selector는 operand가 준비된 warp를 골라 실행한다. 준비된 warp가 없으면 SM이 idle 상태가 된다.

GPU는 context switching overhead 없이 많은 warp를 유지하여 memory latency를 숨긴다. 이것이 latency hiding이다.

## Branch Divergence

Warp 안 thread들이 서로 다른 branch를 선택하면 branch path가 시간적으로 나뉘어 실행되어 utilization이 낮아진다. `tid % 2`처럼 warp 내부에서 절반이 다른 branch를 타는 코드는 좋지 않다.

## Occupancy와 Resource Limit

Occupancy는 SM에 동시에 resident할 수 있는 active warp/thread 비율이다. 영향을 주는 요소:

- block당 thread 수
- register 사용량
- shared memory 사용량
- SM당 최대 threads/warps/blocks

`--ptxas-options=-v`를 사용하면 register 사용량 등 kernel resource 정보를 확인할 수 있다.

## 정리

CUDA의 핵심은 host-device 구조, grid/block/thread 계층, warp 단위 실행을 이해하는 것이다. 성능은 단순히 thread를 많이 만드는 것으로 결정되지 않고, branch divergence를 줄이고 occupancy를 확보하며 memory latency를 숨기는 방식으로 결정된다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Intro to CUDA - CUDA 프로그래밍 모델**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **04. Intro to CUDA - CUDA 프로그래밍 모델**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md) · 다음: [05. CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
