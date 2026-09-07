---
layout: page
title: "HW5. Assignment - CUDA Sum Reduction"
permalink: /studies/systems/parallel-programming/hw5-assignment-cuda-sum-reduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW5%20Assignment%20-%20CUDA%20Sum%20Reduction.md)

{% raw %}
이전: [Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)  
다음: [Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)

## 핵심 요약

HW5는 CUDA sum reduction을 구현하는 과제다. 강의자료에서 제공한 7가지 reduction kernel version을 실행할 수 있도록 kernel invocation을 작성하고, 최종 제출에서는 `2^24 = 16777216`개 item reduction에 대해 가장 빠른 version을 사용해야 한다.

## Reduction 정의

Parallel reduction은 배열 원소를 결합해 하나의 값을 만드는 알고리즘이다.

예:

```text
[a0, a1, a2, ..., an] -> sum
```

## 제공 변수

| 변수 | 의미 |
|---|---|
| `g_idata` | host input sequence |
| `g_odata` | host output buffer |
| `d_idata` | device input sequence |
| `d_odata` | device output buffer, 최종 결과는 `d_odata[0]` |

입력 sequence의 총합은 32bit signed integer 범위를 넘지 않도록 보장된다.

## 구현 범위

`main()`은 수정할 수 없고, 필요한 추가 allocation은 `reduction_optimized()` 내부에서 수행해야 한다. Host-device copy와 기본 device memory allocation/deallocation은 main에서 처리된다.

## 실행 및 실험

제공된 Makefile로 여러 version을 실행한다.

```bash
make 1
make 2
make 3
make 4
make run
```

목표는 7가지 version을 모두 이해하고, 최종적으로 가장 빠른 version을 선택하는 것이다.

## 채점 조건

- 출력 형식 변경 금지
- CUDA library 사용 금지
- deadline 이후 timestamp 변경 주의
- grading server에서 5회 실행 중 maximum 기준
- local congestion 또는 server congestion을 고려해 여유 있는 성능 필요

## 관련 강의 연결

- [CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)

## 정리

HW5는 CUDA reduction 최적화의 실습 과제다. 단순 합계 계산이지만, divergent branch, bank conflict, idle thread, loop unrolling, multiple adds per thread, kernel decomposition을 모두 확인해야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW5. Assignment - CUDA Sum Reduction**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **HW5. Assignment - CUDA Sum Reduction**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md) · 다음: [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)
