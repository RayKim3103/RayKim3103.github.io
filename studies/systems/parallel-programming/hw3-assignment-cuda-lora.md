---
layout: page
title: "HW3. Assignment - CUDA LoRA"
permalink: /studies/systems/parallel-programming/hw3-assignment-cuda-lora/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW3%20Assignment%20-%20CUDA%20LoRA.md)

{% raw %}
이전: [Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md)  
다음: [Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)

## 핵심 요약

HW3는 LoRA 연산을 CUDA C++로 구현하는 과제다. LoRA 자체의 세부 이론보다, LoRA가 matrix multiplication에 크게 의존한다는 점에 초점을 둔다. 제출 대상은 `lora.h`이며, CUDA matmul과 scaling/addition을 정확하고 빠르게 구현해야 한다.

## LoRA 연산

NumPy 형태:

```python
def lora(x, W, A, B, alpha):
    out_linear = x @ W.T
    out_lora = x @ A.T @ B.T * scale
    return out_linear + out_lora
```

Shape:

| Tensor | Shape | 의미 |
|---|---|---|
| `x` | `[B, in_dim]` | input |
| `W` | `[out_dim, in_dim]` | original weight |
| `A` | `[r, in_dim]` | down projection |
| `B` | `[out_dim, r]` | up projection |
| output | `[B, out_dim]` | LoRA 적용 결과 |

## 구현 요구사항

- `lora.h`에 CUDA C++ 구현
- CUDA 기반 matrix multiplication
- LoRA scaling과 addition
- 정확한 output 형식 유지
- OpenMP 사용 금지

## 채점 기준

성능 순위 기반 점수:

| 점수 | 조건 |
|---:|---|
| 10 | top 10% |
| 9 | top 30% |
| 8 | top 50% |
| 7 | correctness 만족 |

## 제출 규칙

- HW3 directory를 지정 경로에 배치
- `make run`이 정상 작동해야 함
- `std::cout` 출력 변경 금지
- deadline 이후 timestamp 변경 주의
- file permission 유지

## 관련 강의 연결

- [Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md)
- [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
- [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)

## 정리

HW3는 CUDA matmul을 실제 deep learning layer 계산에 적용하는 과제다. 핵심은 LoRA 식을 여러 GEMM과 element-wise operation으로 분해하고, shared memory, coalescing, tiling, occupancy를 고려해 구현하는 것이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW3. Assignment - CUDA LoRA**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 병렬 프로그래밍 주제에서는 thread mapping, memory hierarchy, synchronization, profiling metric을 같은 표에 놓고 본다.
- GPU 최적화는 correctness baseline을 고정한 뒤 coalescing, tiling, occupancy, register pressure를 하나씩 바꾸며 측정한다.
- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
- 병렬화는 일을 나누는 것뿐 아니라 memory hierarchy, synchronization, occupancy, load balance를 맞추는 문제다.

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
- **HW3. Assignment - CUDA LoRA**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW2. Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md) · 다음: [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)
