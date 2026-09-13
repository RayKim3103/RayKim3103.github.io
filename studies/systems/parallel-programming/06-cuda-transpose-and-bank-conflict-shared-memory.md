---
layout: page
title: "06. CUDA Transpose and Bank Conflict - Shared Memory 심화"
permalink: /studies/systems/parallel-programming/06-cuda-transpose-and-bank-conflict-shared-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/06%20CUDA%20Transpose%20and%20Bank%20Conflict%20-%20Shared%20Memory%20%EC%8B%AC%ED%99%94.md)

{% raw %}
이전: [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)  
다음: [CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)

## 핵심 요약

이 강의는 dynamic shared memory, matrix transpose, shared memory bank conflict를 다룬다. Matrix transpose는 read와 write 중 하나가 비연속 접근이 되기 쉬워 coalescing 문제가 발생한다. Shared memory를 이용한 corner turning으로 global memory read/write를 모두 coalesced하게 만들 수 있지만, shared memory 내부 bank conflict를 padding으로 해결해야 한다.

## Dynamic Shared Memory

정적 shared memory:

```cpp
__shared__ float tile[32][32];
```

동적 shared memory:

```cpp
extern __shared__ float buffer[];
SomeKernel<<<grid, block, shared_bytes>>>(...);
```

특징:

- kernel launch 시 shared memory 크기를 지정한다.
- 1D array로 선언되므로 index 계산이 필요하다.
- 하나의 buffer를 여러 영역으로 나누어 사용할 수 있다.
- block size를 여러 값으로 바꿔 실험할 때 유용하다.

## Shared Memory Bank

Shared memory는 32개 bank로 구성되어 warp의 32 threads가 동시에 접근할 수 있게 한다. 각 bank는 보통 4B 단위로 mapping된다.

| 접근 패턴 | 결과 |
|---|---|
| 32 threads가 서로 다른 bank 접근 | 병렬 처리 |
| 여러 threads가 같은 bank의 다른 address 접근 | bank conflict, serialization |
| 여러 threads가 같은 address broadcast | architecture에 따라 broadcast 가능 |

## Matrix Transpose 문제

Transpose는 `B[col][row] = A[row][col]` 형태다. Row-major 배열에서는 read와 write 중 하나가 stride 접근이 된다.

| 시도 | read | write | 문제 |
|---|---|---|---|
| Try 0 | coalesced | not coalesced | write가 비연속 |
| Try 1 | not coalesced | coalesced | read가 비연속 |
| Try 2 | 2D block | 한쪽 비연속 | index는 명확하지만 한계 존재 |
| Try 3 | shared memory corner turning | global read/write 개선 | shared memory bank conflict 가능 |
| Try 4 | skew/padding 추가 | coalescing + conflict 완화 | shared memory index 계산 필요 |

## Corner Turning

Corner turning의 목적은 global memory 접근을 모두 coalesced하게 만드는 것이다.

1. 원본 matrix를 row-wise로 읽어 shared memory tile에 저장한다.
2. Shared memory tile 내부에서 transpose된 위치로 접근한다.
3. 결과 matrix에 row-wise로 write한다.

Global memory 관점에서는 read와 write가 모두 연속 접근이 된다.

## Bank Conflict와 Padding

Shared memory tile을 `[TILE][TILE]`로 두고 transpose access를 하면 warp threads가 같은 bank를 동시에 건드릴 수 있다. 해결책은 한 column의 폭을 1만큼 늘리는 padding이다.

```cpp
const int SKEW = 1;
extern __shared__ float buffer[];
// logical tile width = TILE + SKEW
```

이렇게 stride가 32의 배수에서 벗어나 bank mapping이 분산된다.

## 숫자로 확인하기 — 32-way bank conflict와 padding 효과

Shared memory tile을 `float tile[32][32]`로 선언하면 한 row의 폭은 $$32 \times 4\text{byte} = 128\text{byte} = 32\text{bank} \times 4\text{byte}$$, 즉 정확히 32개 bank 전체를 채운다. 이제 warp의 32개 thread가 같은 column을 동시에 접근한다고 하자(`tile[i][col]`, `i=0..31` 고정 `col`).

`col=0`으로 고정하면 thread `i`가 접근하는 word 단위 주소는 `tile[i][0]` = $$i \times 32$$이고, bank 번호는 $$(\text{word 주소}) \bmod 32$$로 정해진다.

$$
\text{bank}(i) = (i \times 32) \bmod 32 = 0 \quad (\text{모든 } i = 0,\dots,31\text{에 대해})
$$

`col`이 0이 아닌 다른 고정값이어도 모든 thread의 주소에 똑같이 `+col`이 더해질 뿐이므로 bank 번호는 여전히 `col mod 32`로 **32개 thread 모두 동일**하다.

즉 32개 thread **전부가 bank 0 하나로 몰리는 32-way bank conflict**가 발생해, 원래 1cycle에 끝날 접근이 32cycle로 직렬화된다.

**Padding 적용**(`SKEW=1`로 `tile[32][33]` 선언): row 폭이 $$33 \times 4\text{byte} = 132\text{byte}$$가 되어, 같은 column 접근의 주소 간격이 $$33\times4=132\text{byte}$$로 바뀐다.

$$
\text{bank}(i) = \frac{i \times 33 \times 4}{4} \bmod 32 = (i \times 33) \bmod 32 = i \bmod 32
$$

이제 $$i=0,1,\dots,31$$에 대해 bank 번호가 $$0,1,\dots,31$$로 **서로 다른 32개 bank에 정확히 하나씩** 흩어진다 — conflict가 완전히 사라져 32cycle이 다시 1cycle로 돌아온다. `SKEW=1`이라는 단 하나의 padding column이 32-way conflict를 무충돌로 바꾸는 이유가 이 modular 연산에서 나온다.

## 정리

Transpose는 memory coalescing과 bank conflict를 동시에 보여주는 대표 예제다. 좋은 CUDA kernel은 global memory access만 보는 것이 아니라, shared memory 내부 bank mapping까지 고려해야 한다. “coalescing을 만들고, bank conflict를 피하라”가 이 강의의 결론이다.

## 복습 질문

- `tile[32][32]`에서 같은 column을 32개 thread가 접근할 때 왜 모두 bank 0(또는 `col mod 32`)으로 몰리는지 계산할 수 있는가?
- `SKEW=1` padding이 row 폭을 33 word로 바꾸는 것만으로 왜 32개의 서로 다른 bank로 정확히 흩어지는지, modular 연산으로 설명할 수 있는가?
- Bank conflict가 32-way일 때와 conflict-free일 때 접근 시간이 왜 32배 차이가 나는지 설명할 수 있는가?

{% endraw %}

---

이전: [05. CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md) · 다음: [07. CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)
