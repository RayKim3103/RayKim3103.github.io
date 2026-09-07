---
layout: page
title: "HW2. Assignment - Matrix Verification Challenge"
permalink: /studies/systems/parallel-programming/hw2-assignment-matrix-verification-challenge/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW2%20Assignment%20-%20Matrix%20Verification%20Challenge.md)

{% raw %}
이전: [More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md)  
다음: [Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md)

## 핵심 요약

HW2는 행렬곱 검증 문제를 두 방식으로 구현하는 과제다. 하나는 병렬 GEMM으로 `A * B`를 직접 계산해 `C`와 비교하는 방식이고, 다른 하나는 Freivalds algorithm으로 확률적 검증을 수행하는 방식이다. 구현 파일은 `hw2/parallel.h`이며, 성능과 correctness가 모두 중요하다.

## 문제 배경

세 개의 `N x N` 행렬 `A`, `B`, `C`가 있을 때 `A * B == C`인지 검증해야 한다.

| 접근 | 아이디어 |
|---|---|
| Parallel GEMM | `A * B` 전체를 병렬 계산한 뒤 `C`와 비교 |
| Freivalds Algorithm | random vector `v`를 이용해 `A(Bv) == Cv`인지 검사 |

## 구현 대상

- `GEMV`: General Matrix-Vector Multiplication
- `GEMM`: General Matrix-Matrix Multiplication
- `init_vec`: Freivalds 검증에 사용할 vector 초기화
- 병렬 programming technique을 사용해 multi-thread 성능 개선

## Freivalds Algorithm

기본 절차:

1. 0/1 random vector `v` 생성
2. `Bv` 계산
3. `A(Bv)` 계산
4. `Cv` 계산
5. 두 vector가 같으면 `AB=C`일 가능성이 높다고 판단

`AB != C`인데 통과할 확률은 한 번 검사에서 최대 `1/2`이고, `k`번 반복하면 최대 `(1/2)^k`로 감소한다.

## 보고서 요구사항

보고서에는 다음을 포함해야 한다.

- Parallel algorithm 구현 방식
- 두 접근법 중 어떤 방식이 더 나은지
- Freivalds algorithm이 probabilistic인 이유와 error bound
- 프로그램이 최상의 성능을 내는 이유와 추가 여지가 있는지
- 표 또는 그래프를 포함한 evaluation
- 추가 분석

## 제출 및 채점 조건

- grading 시 `hw2/parallel.h`만 복사해 사용
- grading server에서 5회 실행 중 maximum 기준으로 speed 측정
- 출력 형식을 바꾸면 안 됨
- deadline 이후 timestamp 변경 시 late 처리 가능
- local에서만 동작했다는 이유로 재채점되지 않음

## 관련 강의 연결

- [Thread Programming - C++ Thread와 동기화](02-thread-programming-c.md)
- [Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md)

## 정리

HW2의 핵심은 같은 검증 문제를 완전 계산 방식과 확률적 방식으로 비교하는 것이다. GEMM은 정확하지만 `O(N^3)` 비용이 크고, Freivalds는 확률적 error를 허용하는 대신 GEMV 중심으로 계산량을 크게 줄인다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW2. Assignment - Matrix Verification Challenge**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
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
- **HW2. Assignment - Matrix Verification Challenge**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [15. More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md) · 다음: [HW2. Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md)
