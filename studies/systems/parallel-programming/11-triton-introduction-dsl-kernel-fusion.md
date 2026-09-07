---
layout: page
title: "11. Triton Introduction - Triton DSL과 Kernel Fusion"
permalink: /studies/systems/parallel-programming/11-triton-introduction-dsl-kernel-fusion/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/11%20Triton%20Introduction%20-%20Triton%20DSL%EA%B3%BC%20Kernel%20Fusion.md)

{% raw %}
이전: [Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)  
다음: [CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md)

## 핵심 요약

이 강의는 Triton DSL을 소개한다. PyTorch의 operator 조합은 각 operator마다 memory load/store와 kernel launch가 발생해 memory-bound workload에서 비효율이 생길 수 있다. Triton은 Python 기반 DSL로 block 단위 GPU kernel을 직접 작성하여 operation fusion과 tunable kernel configuration을 가능하게 한다.

## 왜 Triton인가

Softmax 예시에서 naive PyTorch 구현은 여러 operator로 나뉘어 input을 반복해서 읽고 중간 결과를 저장한다. Triton은 필요한 연산을 하나의 custom kernel로 fusion하여 global memory traffic을 줄일 수 있다.

이론적 비교:

- Naive PyTorch: 여러 operator로 인해 `5N^2 + 2N` 수준의 load, 중간 store 발생
- Triton fusion: 필요한 데이터를 한 번 읽고 최종 결과를 저장하는 형태로 감소
- Memory-bound kernel에서는 memory traffic 감소가 곧 큰 speedup으로 연결된다.

## CUDA Software Stack 속 Triton

Application은 보통 CUDA library(cuBLAS, cuDNN), CUDA runtime API, device API 등을 통해 GPU를 사용한다. Triton은 CUDA보다 높은 수준에서 GPU kernel을 작성하지만, PyTorch보다 낮은 수준에서 block, index, mask, tile size를 직접 설계할 수 있는 위치에 있다.

## Triton Kernel 구성 요소

| 요소 | 의미 |
|---|---|
| `@triton.jit` | Python 함수를 GPU kernel로 JIT compile |
| `tl.program_id(axis)` | 현재 program instance의 block id |
| `tl.arange` | block 내부 vector index 생성 |
| mask | out-of-bounds load/store 방지 |
| strides | non-contiguous tensor layout 접근 |
| constexpr | compile-time tunable parameter |

Triton의 program instance는 CUDA thread block과 비슷한 추상화로 볼 수 있다. 각 program이 tile 단위 data를 처리한다.

## Index와 Mask

Triton에서는 직접 pointer offset을 만들어 load/store한다.

```python
offs = pid * BLOCK_SIZE + tl.arange(0, BLOCK_SIZE)
mask = offs < n
x = tl.load(x_ptr + offs, mask=mask)
tl.store(y_ptr + offs, out, mask=mask)
```

Mask는 block 크기가 입력 크기를 딱 나누지 않을 때 잘못된 memory 접근을 막는다.

## Autotune과 Benchmark

| 기능 | 역할 |
|---|---|
| `@triton.autotune` | 여러 block size, num warps 등 config를 자동 실험 |
| key | 어떤 입력 shape 변화에서 다시 튜닝할지 결정 |
| `@triton.benchmark` | kernel 성능 측정 |

Triton은 kernel parameter를 실험하며 shape별 최적 configuration을 찾기 좋다.

## PyTorch Integration

Triton은 PyTorch와 함께 custom operator처럼 사용될 수 있다. Liger Kernel, PyTorch tutorial 등에서 transformer/LLM 연산을 Triton으로 최적화하는 예시를 제공한다.

## 정리

Triton의 강점은 CUDA보다 생산성이 높고 PyTorch보다 kernel-level 제어가 가능하다는 점이다. 특히 memory-bound operator에서 fusion으로 global memory traffic을 줄일 때 효과적이다. 다만 CUDA처럼 thread, shared memory를 세밀하게 직접 제어하는 모델은 아니므로 최적화 관점의 추상화 차이를 이해해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. Triton Introduction - Triton DSL과 Kernel Fusion**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **11. Triton Introduction - Triton DSL과 Kernel Fusion**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md) · 다음: [12. CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md)
