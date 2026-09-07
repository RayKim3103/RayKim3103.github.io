---
layout: page
title: "07. CUDA DNN - Convolution과 im2col"
permalink: /studies/systems/parallel-programming/07-cuda-dnn-convolution-im2col/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/07%20CUDA%20DNN%20-%20Convolution%EA%B3%BC%20im2col.md)

{% raw %}
이전: [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)  
다음: [CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)

## 핵심 요약

이 강의는 DNN의 기본 연산, convolution layer, direct convolution, im2col 기반 convolution을 다룬다. DNN은 fully connected layer와 convolution layer 같은 building block으로 구성되며, GPU 구현에서는 convolution의 7중 loop를 어떻게 dataflow로 배치하고 병렬화할지가 중요하다.

## DNN 기본

Deep neural network는 여러 layer를 쌓아 입력을 점점 더 높은 수준의 feature로 변환한다.

주요 구성:

- Fully connected layer
- Convolution layer
- Activation function, 예: ReLU
- Pooling
- 반복적인 layer stack

Training은 weight를 조정해 network가 표현하는 함수를 바꾸는 과정이며, backpropagation을 통해 filter와 weight를 학습한다.

## 2D Convolution

Convolution은 filter mask를 input image 위에서 sliding하며 output response map을 만든다. 여러 filter를 사용하면 여러 output feature map이 생성된다.

Output 크기:

```text
H_out = H - K + 1
W_out = W - K + 1
```

Padding/stride를 고려하지 않은 기본 valid convolution 기준이다.

## Convolution Layer Forward

대표 loop 차원은 다음 7개다.

| 차원 | 의미 |
|---|---|
| N | minibatch |
| M | output feature maps |
| H | input height |
| W | input width |
| C | input feature maps |
| K1 | weight height |
| K2 | weight width |

7중 loop dataflow:

```text
N -> M -> H -> W -> C -> K1 -> K2
```

CNN 가속은 이 loop nest를 어떤 순서로 돌리고, 어떤 차원을 block/thread/grid로 mapping하며, 어떤 데이터를 재사용할지 결정하는 문제다.

## Direct Convolution 병렬화

강의 전략:

- 각 block이 output pixel tile을 계산한다.
- Grid는 minibatch, output feature map, output tile 차원으로 구성된다.
- Thread block은 `TILE_WIDTH x TILE_WIDTH` output tile을 담당한다.

각 thread는 특정 `(n, m, h, w)` output element를 계산하고, C와 KxK 범위를 순회하며 MAC을 수행한다.

## Shared Memory Convolution

Convolution에서는 인접 output pixel들이 겹치는 input window를 많이 읽는다. 중복 global memory read가 많으므로 shared memory에 input tile을 올려 재사용할 수 있다.

주의점:

- Filter size 때문에 tile 주변 halo 영역이 필요하다.
- Tile load 영역과 실제 compute 영역이 다를 수 있다.
- Boundary check가 필요하다.

## im2col

im2col은 convolution을 matrix multiplication으로 바꾸는 기법이다.

```text
Input image patches -> X_col
Filter weights -> W_row
Y = W_row * X_col
```

장점:

- GPU에서 고도로 최적화된 GEMM을 활용할 수 있다.
- 복잡한 convolution loop를 matrix multiplication 문제로 바꾼다.

단점:

- X_col 생성으로 memory overhead가 생긴다.
- patch가 겹치므로 같은 input 값이 여러 번 복사될 수 있다.

## Pooling과 큰 네트워크

Pooling은 convolution보다 단순하며, 영역 내 max 또는 average를 계산한다. VGGNet 같은 큰 네트워크는 convolution, ReLU, pooling, fully connected layer를 반복적으로 구성한다.

## 추가 convolution 방법

| 방법 | 아이디어 |
|---|---|
| Direct convolution | 원래 loop를 GPU에 mapping |
| im2col + GEMM | convolution을 matrix multiplication으로 변환 |
| FFT convolution | time/spatial domain convolution을 frequency domain multiply로 변환 |
| Winograd | 곱셈 수를 줄이도록 중간값 재배치 |

## 정리

DNN 가속의 핵심은 convolution dataflow를 이해하고, direct convolution과 im2col의 tradeoff를 비교하는 것이다. Direct 방식은 memory overhead가 낮지만 최적화가 어렵고, im2col은 GEMM 최적화를 활용할 수 있지만 추가 memory movement가 생긴다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. CUDA DNN - Convolution과 im2col**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **07. CUDA DNN - Convolution과 im2col**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md) · 다음: [08. CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)
