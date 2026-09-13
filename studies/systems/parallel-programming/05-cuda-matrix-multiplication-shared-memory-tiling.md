---
layout: page
title: "05. CUDA Matrix Multiplication - Shared Memory Tiling"
permalink: /studies/systems/parallel-programming/05-cuda-matrix-multiplication-shared-memory-tiling/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/05%20CUDA%20Matrix%20Multiplication%20-%20Shared%20Memory%20Tiling.md)

{% raw %}
이전: [Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md)  
다음: [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)

## 핵심 요약

이 강의는 CUDA에서 matrix multiplication을 구현하고 shared memory tiling으로 global memory traffic을 줄이는 방법을 다룬다. 핵심은 각 thread가 C의 한 element를 계산하되, A와 B의 tile을 shared memory에 올려 block 내부 thread들이 재사용하게 만드는 것이다.

## CUDA Memory Model

| Memory | 범위 | 특징 |
|---|---|---|
| Register | thread private | 가장 빠름, 자동 변수 대부분 |
| Shared memory | block shared | 빠른 SRAM, 명시적 관리 |
| Global memory | grid 전체 | 크지만 느림 |
| Constant/Texture | 특수 read path | 접근 패턴에 따라 유리 |

GPU cache는 spatial locality는 어느 정도 잡지만, 많은 thread가 동시에 실행되므로 per-thread cache capacity가 작다. Matrix multiplication처럼 같은 데이터를 여러 번 재사용하는 경우 shared memory를 직접 쓰는 것이 중요하다.

## Simple CUDA MatMul

각 thread가 `P[row][col]` 하나를 계산한다.

```cpp
int row = blockIdx.y * blockDim.y + threadIdx.y;
int col = blockIdx.x * blockDim.x + threadIdx.x;

float value = 0;
for (int k = 0; k < width; k++) {
    value += M[row * width + k] * N[k * width + col];
}
P[row * width + col] = value;
```

이 방식은 구현은 단순하지만 같은 M/N element를 여러 thread가 global memory에서 반복적으로 읽는다.

## 2D Block Strategy

`TILE_WIDTH x TILE_WIDTH` thread block 하나가 결과 matrix의 같은 크기 tile을 계산한다.

| 구성 | 의미 |
|---|---|
| `dimBlock(TILE_WIDTH, TILE_WIDTH)` | block 안 thread 배열 |
| `dimGrid(width/TILE_WIDTH, width/TILE_WIDTH)` | 결과 tile 개수 |
| `threadIdx.y` | 결과 row 내부 위치 |
| `threadIdx.x` | 결과 column 내부 위치 |

## Shared Memory Blocking

각 phase마다 A tile과 B tile을 shared memory에 load한다.

```cpp
__shared__ float subTileM[TILE_WIDTH][TILE_WIDTH];
__shared__ float subTileN[TILE_WIDTH][TILE_WIDTH];
```

흐름:

1. 각 thread가 global memory에서 A/B element 하나씩 load
2. `__syncthreads()`로 tile load 완료 대기
3. shared memory tile을 이용해 partial dot product 계산
4. `__syncthreads()`로 tile 사용 완료 대기
5. 다음 tile phase로 진행

## `__syncthreads()`

`__syncthreads()`는 같은 thread block 안의 barrier다. 모든 thread가 도착해야 다음으로 넘어간다. Tiled algorithm에서는 tile load와 tile consume 사이의 correctness를 보장한다.

주의:

- block 내부에서만 동작한다.
- 다른 block과는 synchronization하지 않는다.
- 조건문 안에서 일부 thread만 도달하면 deadlock이 될 수 있다.

## Memory Traffic 분석

Tile 크기가 `b`이면 각 tile element가 block 내부에서 `b`번 재사용된다. 전체 global memory load는 naive 대비 대략 `b`배 줄어들 수 있다.

단, tile을 크게 잡는다고 항상 좋은 것은 아니다.

제약:

- block당 최대 thread 수
- SM shared memory 용량
- register pressure
- occupancy

예를 들어 8x8, 16x16, 32x32 tile은 모두 occupancy 100%가 가능할 수 있지만, 32x32는 block당 1024 threads라 scheduling 유연성이 떨어질 수 있다.

## 숫자로 확인하기 — tiling이 줄이는 global memory traffic

`TILE_WIDTH=16`으로 $$N=1024$$ 정방행렬을 곱한다고 하자.

**Naive 방식**: 각 thread가 `P[row][col]` 하나를 계산하며 매 `k` 반복마다 `M`과 `N`에서 각각 1개 float를 global memory에서 읽는다. 전체 thread 수는 $$1024\times1024$$개이고 thread마다 $$1024$$번의 k-loop를 도므로, `M` 하나만 봐도 총 global memory read는

$$
1024\times1024\times1024 \approx 1.07\times10^9 \text{회}
$$

즉 같은 `M[row][k]` 값을 1024개의 서로 다른 column-thread가 각각 다시 읽는다.

**Tiling 방식**(tile 크기 $$b=16$$): 같은 tile 안의 16개 thread가 shared memory에 한 번 올라온 `M` 원소를 16번 재사용하므로, global memory read는 이론상 $$1/b = 1/16$$로 줄어

$$
1.07\times10^9 / 16 \approx 6.7\times10^7 \text{회}
$$

가 된다 — **16배의 global memory traffic 감소**다. 이것이 "tile 크기가 `b`이면 global memory load가 naive 대비 대략 `b`배 줄어들 수 있다"는 문장의 실제 배수다. 다만 tile을 32x32로 더 키우면 이론상 32배 감소를 기대할 수 있지만, block당 $$32\times32=1024$$ thread를 쓰게 되어 scheduling 유연성이 떨어지는 trade-off가 함께 온다.

## Coalescing

Global memory access는 warp의 thread들이 연속 주소를 읽을 때 효율적이다. Matrix multiplication에서 A와 B tile을 load할 때 `threadIdx.x`가 연속 주소를 담당하도록 배치해야 coalescing이 잘 일어난다.

## Corner Turning

데이터를 shared memory에 넣을 때는 coalesced read로 읽고, shared memory 내부에서 access pattern을 바꾸어 write 또는 compute에서도 효율을 얻는 방식이다. 이후 transpose 강의에서 bank conflict와 함께 더 자세히 다룬다.

## 복습 질문

- $$N=1024$$, `TILE_WIDTH=16`일 때 naive 방식의 global memory read 횟수(약 $$1.07\times10^9$$)를 직접 계산할 수 있는가?
- Tiling이 이 read 횟수를 16배 줄이는 이유를, "같은 tile 안 16개 thread가 shared memory 값을 재사용한다"는 사실로 설명할 수 있는가?
- Tile을 32x32로 키우면 이론적 traffic 감소는 커지지만 왜 scheduling 유연성이 떨어지는지 설명할 수 있는가?

## 정리

CUDA matmul 최적화의 핵심은 global memory에서 직접 dot product를 계산하지 않고, shared memory tile을 통해 데이터를 block 내부에서 재사용하는 것이다. 성능은 tile 크기, coalescing, bank conflict, occupancy, synchronization overhead의 균형으로 결정된다.

{% endraw %}

---

이전: [04. Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md) · 다음: [06. CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)
