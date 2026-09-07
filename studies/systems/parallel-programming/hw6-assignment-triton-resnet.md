---
layout: page
title: "HW6. Assignment - Triton ResNet"
permalink: /studies/systems/parallel-programming/hw6-assignment-triton-resnet/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW6%20Assignment%20-%20Triton%20ResNet.md)

{% raw %}
이전: [Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)  
다음: [Report - Triton ResNet18 구현과 Conv2d 분석](hw6-report-triton-resnet18-conv2d.md)

## 핵심 요약

HW6는 final assignment로 Triton을 이용해 ResNet18을 직접 구현하는 과제다. PyTorch와 CUDA library가 이미 고성능 kernel을 제공하지만, 특수 상황에서 custom GPU kernel을 작성하는 능력을 기르기 위해 Triton kernel을 구현한다.

## 과제 목표

- ResNet18 구조를 Triton kernel로 구현
- `TritonMGP/kernel` directory 안 kernel 구현
- Grading 시 kernel directory만 사용
- PyTorch/library 대비 custom kernel 성능과 한계 분석

## 왜 Triton인가

Custom kernel 작성은 비용이 있지만, operator fusion이나 특정 shape 최적화가 필요할 때 유리할 수 있다. Triton은 CUDA보다 높은 수준에서 GPU tile/program 단위 kernel을 작성할 수 있게 한다.

## 보고서 요구사항

보고서는 매우 중요하며 10점 배점이다. 특히 다음 질문에 대한 분석이 필요하다.

- 왜 Conv2d가 torch 대비 효율적인 성능을 보이지 않는가?
- 직접 구현한 Triton kernel의 병목은 무엇인가?
- PyTorch가 사용하는 cuBLAS, cuDNN, CUTLASS 같은 library kernel과 어떤 차이가 있는가?

## 채점 및 규칙

- Final assignment라 late submission 없음
- grading server 5회 실행 중 maximum 기준
- output 변경 금지
- 충분한 file permission 유지
- 보고서에 GPT 사용 시 큰 penalty 명시

## 관련 강의 연결

- [Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)
- [More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md)
- [CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)

## 정리

HW6는 강의의 CUDA 최적화 지식을 Triton과 DNN 전체 모델 구현으로 확장하는 과제다. 핵심은 ResNet18의 각 layer를 Triton kernel로 구현하면서, high-level DSL의 생산성과 low-level CUDA/library 대비 성능 한계를 함께 분석하는 것이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **HW6. Assignment - Triton ResNet**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **HW6. Assignment - Triton ResNet**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md) · 다음: [HW6. Report - Triton ResNet18 구현과 Conv2d 분석](hw6-report-triton-resnet18-conv2d.md)
