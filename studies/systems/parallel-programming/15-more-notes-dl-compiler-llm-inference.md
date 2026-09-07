---
layout: page
title: "15. More Notes - DL Compiler와 LLM Inference"
permalink: /studies/systems/parallel-programming/15-more-notes-dl-compiler-llm-inference/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/15%20More%20Notes%20-%20DL%20Compiler%EC%99%80%20LLM%20Inference.md)

{% raw %}
이전: [Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md)  
다음: [Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md)

## 핵심 요약

마지막 강의는 deep learning compiler와 LLM inference 최적화를 소개한다. TensorFlow XLA, TensorRT, TVM, Glow, Triton 같은 compiler/runtime은 computation graph를 IR로 보고 graph rewrite, operator fusion, code generation을 수행한다. LLM inference에서는 prefill은 GEMM, decode는 GEMV가 중요하며, FlashAttention과 vLLM 같은 기법이 memory와 scheduling 병목을 줄인다.

## Deep Learning Compilers

대표 시스템:

- TensorFlow XLA
- NVIDIA TensorRT
- TVM
- Glow
- Triton

DL framework는 보통 network를 operator 단위로 실행한다. 각 operator는 input을 읽고 output을 만들며, operator 사이에 intermediate tensor가 생긴다. 이 방식은 사용하기 쉽지만 interpreted execution, kernel launch overhead, 불필요한 memory traffic이 생길 수 있다.

## Compiler 관점

Compiler는 framework의 computation을 intermediate representation(IR)로 보고 hardware별 code를 생성한다.

흐름:

```text
Framework graph
-> IR
-> graph optimization
-> operator fusion / layout transform
-> target-specific code generation
-> optimized binary/runtime
```

## XLA

XLA는 Accelerated Linear Algebra로, TensorFlow computation을 HLO(High Level Operations) IR로 표현한다. Map, broadcast, reduce, convolution 같은 tensor primitive를 최적화하고 target에 맞는 실행 binary를 생성한다.

## TensorRT

NVIDIA TensorRT는 DNN inference 최적화 플랫폼이다.

주요 기능:

- Weight quantization
- Kernel fusion
- Vertical/horizontal fusion
- NVIDIA GPU에 최적화된 inference execution

## TVM

TVM은 다양한 hardware target을 위한 machine learning compiler와 runtime이다. High-level graph rewriting부터 schedule/code generation까지 포함하며 GPU, CPU, mobile, FPGA 같은 target을 지원한다.

## LLM Inference

Decode-only LLM inference는 크게 두 단계로 나뉜다.

| 단계 | 설명 | 주요 연산 |
|---|---|---|
| Prefill | prompt 전체를 처리해 첫 token 상태 생성 | GEMM 중심 |
| Decode | token을 하나씩 autoregressive하게 생성 | GEMV 중심 |

Context length가 길어질수록 prefill latency에서 GEMM 비중이 커지고, decode에서는 매 token마다 batch/sequence 구조 때문에 GEMV가 중요해진다.

## FlashAttention

Standard attention은 memory traffic이 크다. FlashAttention은 tiling과 fusion으로 attention 중간 matrix를 global memory에 크게 저장하지 않고 block 단위로 처리하여 memory traffic을 줄인다.

발전 흐름:

- FlashAttention v1: tiling/fusion으로 memory efficient attention
- v2/v3: work partitioning과 hardware utilization 개선

## vLLM과 PagedAttention

vLLM은 PagedAttention으로 KV cache를 memory page처럼 관리한다. LLM serving에서 요청별 sequence 길이가 다르고 KV cache가 커지는 문제를 줄여 throughput과 memory utilization을 개선한다.

## 정리

마지막 강의의 메시지는 병렬 프로그래밍의 원리가 CPU/GPU kernel을 넘어 DL compiler와 LLM serving에도 그대로 적용된다는 것이다. 연산을 fusion하고, memory traffic을 줄이고, GEMM/GEMV/attention 같은 핵심 primitive를 target hardware에 맞게 최적화하는 것이 현대 AI system 성능의 중심이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **15. More Notes - DL Compiler와 LLM Inference**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
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
- **15. More Notes - DL Compiler와 LLM Inference**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [14. Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md) · 다음: [HW2. Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md)
