---
layout: page
title: "HW4. Assignment - CUDA Conv2d"
permalink: /studies/systems/parallel-programming/hw4-assignment-cuda-conv2d/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW4%20Assignment%20-%20CUDA%20Conv2d.md)

{% raw %}
이전: [Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)  
다음: [Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)

## 핵심 요약

HW4는 CUDA로 convolution을 두 방식으로 구현하는 과제다. 첫째는 direct Conv2d kernel이고, 둘째는 im2col로 convolution을 matrix multiplication으로 변환한 뒤 matmul을 수행하는 방식이다. 제출 대상은 `conv.h`이며, filter size가 구현에 미치는 영향을 신중히 고려해야 한다.

## 구현 방식

| 방식 | 설명 |
|---|---|
| Parallel Conv2d | GPU에서 convolution을 직접 계산 |
| im2col + Matmul | input patch를 column matrix로 펼친 뒤 GEMM 수행 |

## 문제 조건

- Grading 중 input size는 고정
- input value는 달라질 수 있음
- batch size는 항상 1
- filter size가 memory access와 output size에 미치는 영향을 고려해야 함

## 구현 대상

`conv.h` 안의 세 함수를 구현한다.

예상 구성:

- direct convolution
- im2col 변환
- matmul 기반 convolution

## 채점 포인트

- Correctness
- CUDA kernel 성능
- memory access pattern 최적화
- strict performance benchmark 만족

## 규칙

- 지정된 HW4 directory 구조 유지
- `make run` 정상 실행
- 출력 문구 변경 금지
- deadline 이후 timestamp 변경 주의
- CUDA library 사용 금지
- file permission 유지

## 관련 강의 연결

- [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
- [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)
- [CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)

## 정리

HW4는 convolution 자체와 GEMM으로 변환한 convolution을 비교하는 과제다. Direct 방식은 중간 memory가 적지만 최적화가 어렵고, im2col+GEMM은 matrix multiplication 최적화를 재사용할 수 있지만 중간 데이터 변환 비용이 생긴다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW4. Assignment - CUDA Conv2d**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **HW4. Assignment - CUDA Conv2d**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md) · 다음: [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)
