---
layout: page
title: "HW2. Report - Parallel GEMM과 Freivalds 최적화"
permalink: /studies/systems/parallel-programming/hw2-report-parallel-gemm-freivalds/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW2%20Report%20-%20Parallel%20GEMM%EA%B3%BC%20Freivalds%20%EC%B5%9C%EC%A0%81%ED%99%94.md)

{% raw %}
이전: [Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md)  
다음: [Assignment - CUDA LoRA](hw3-assignment-cuda-lora.md)

## 핵심 요약

이 보고서는 GEMM과 Freivalds algorithm을 CPU multi-threading으로 구현하고 최적화한 내용을 정리한다. 주요 최적화는 thread 수 조정, row-wise 병렬화, B transpose, blocked matrix multiplication, cache locality 개선, false sharing 회피, loop unrolling이다.

## 목표

- GEMM과 GEMV 기반 Freivalds algorithm 병렬 구현
- Deep learning과 computer vision에서 중요한 행렬 연산의 성능 개선
- Cache와 thread scheduling을 고려한 CPU 최적화
- GEMM 접근과 Freivalds 접근의 성능 및 정확도 tradeoff 비교

## GEMM 구현 전략

### Row-wise 병렬화

`C[i][j]` 계산에서 row index `i`를 thread별로 나누었다. 이유는 다음과 같다.

- thread 간 dependency가 없다.
- 같은 C element를 여러 thread가 쓰지 않아 race condition이 없다.
- atomic 연산이 필요 없다.
- row-major access로 spatial locality가 좋다.

### Transpose와 Blocking

B matrix를 transpose하여 원래 column-wise 접근을 row-wise 접근으로 바꾸었다. 이후 block 단위로 계산해 A, B, C의 tile이 L1 cache에 들어가도록 했다.

Block size 결정 관점:

- L1d cache를 core당 약 32KiB로 보고 계산
- local A, local B, local C가 cache에 들어가야 함
- 실험적으로 `16 x 16` block size가 좋은 성능을 보임

## Freivalds / GEMV 구현 전략

Freivalds는 `A * (B * v) == C * v`를 검사한다. 핵심 계산은 GEMV다.

적용한 최적화:

- multi-threading
- B vector 또는 partial vector를 thread-local하게 복사해 L1 cache 활용
- blocked GEMV
- loop unrolling

`init_vec()` 실행 시간은 grading 성능 평가에 포함되지 않으므로 단순 single-thread loop로 0/1 vector를 생성했다.

## Freivalds가 확률적인 이유

만약 `AB=C`이면 Freivalds는 항상 참을 반환한다. 그러나 `AB != C`인 경우에도 특정 random vector가 차이를 숨길 수 있다. 한 번의 검사에서 false positive 확률은 최대 `1/2`이고, `k`번 독립 반복하면 error bound는 `(1/2)^k`가 된다.

## 두 접근 비교

| 기준 | Parallel GEMM | Freivalds |
|---|---|---|
| 정확성 | 결정적 | 확률적 |
| 계산량 | `O(N^3)` | 여러 GEMV, 대략 `O(kN^2)` |
| 검증 목적 성능 | 느릴 수 있음 | 훨씬 빠름 |
| 결과 행렬 필요 여부 | 계산 결과가 필요하면 적합 | 검증만 필요할 때 적합 |

검증만 목적이라면 Freivalds가 더 적은 연산으로 높은 확률의 correctness를 제공하므로 더 적합하다고 판단했다.

## 추가 성능 고찰

보고서는 GEMV advanced performance 기준을 달성하지 못한 이유도 분석한다.

고려한 요소:

- thread 수가 너무 많으면 scheduling/context switching overhead 증가
- cache locality가 부족하면 memory access가 병목
- false sharing 회피를 위해 local matrix/vector 사용
- loop unrolling으로 compiler SIMD 유도
- copy overhead와 cache hit gain 사이 균형 필요

## 정리

HW2 보고서의 핵심은 CPU 병렬화에서 thread 수만 늘리는 것이 답이 아니라는 점이다. 행렬곱은 memory layout, cache line, false sharing, block size, transpose 여부가 성능을 지배한다. 검증 문제에서는 Freivalds처럼 문제 목적에 맞는 algorithmic optimization이 가장 큰 차이를 만든다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW2. Report - Parallel GEMM과 Freivalds 최적화**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **HW2. Report - Parallel GEMM과 Freivalds 최적화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW2. Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md) · 다음: [HW3. Assignment - CUDA LoRA](hw3-assignment-cuda-lora.md)
