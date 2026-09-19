---
layout: page
title: "10. Triton으로 GPU 커널 작성하기"
permalink: /grad_studies/ai_platform_optimization/10-triton-gpu-kernel-programming/
sitemap: false
---

- **강의**: 7-1. Triton Introduction

{% raw %}
## 개요

Ch.4에서 TorchInductor가 GPU 코드를 생성할 때 **OpenAI Triton**을 사용한다고 언급했다. 이 장은 Triton이 정확히 무엇이고, CUDA를 직접 짜는 것과 무엇이 다르며, 왜 순수 PyTorch보다 훨씬 빠른 커널을 생성할 수 있는지를 GPU 하드웨어 구조부터 실제 성능 비교까지 이어서 설명한다.

## Triton은 왜 빠른가 — 실측 비교

같은 Softmax 연산을 세 가지 방식으로 구현해 비교하면 (python 3.13, torch 2.5.1, triton 3.2.0, RTX 3090 기준):

- **Naive PyTorch 구현** 대비 **Triton 커널은 약 4배 빠르다**
- 심지어 고도로 최적화된 **cuBLAS 기반 `torch.softmax`보다도 근소하게 더 빠르다**
- `torch.compile()`(Ch.4)을 적용하면 naive 구현보다는 빨라지지만("triton is here"), 여전히 이상적인 성능에는 못 미친다 (python 3.11, T4 GPU 기준 — 참고로 Dynamo는 이 실험 시점 기준 Python 3.13+에서 동작하지 않았다)

즉 Triton으로 **직접** 커널을 작성하면, 컴파일러(`torch.compile`)가 자동으로 생성한 코드보다도 더 나은 성능을 낼 수 있는 여지가 있다는 뜻이다 — 이는 Ch.9에서 말한 "필요시 커널을 수동으로 작성한다"는 최적화 우선순위의 마지막 단계에 해당한다.

## CUDA 소프트웨어 스택 복습

Application에서 실제 GPU까지 내려가는 경로:

```text
1. Application
2. CUDA Libraries (예: cuBLAS)
3. CUDA Runtime API (libcudart.so)  — #include <cuda_runtime.h>, cudaMalloc, cudaMemcpy
4. CUDA Driver API (libcuda.so)     — 사용자 모드 API, #include <cuda.h>, cuMemcpyDtoA, cuMemAlloc
5. NVIDIA GPU Driver (nvidia.ko)    — 오픈소스, OS 커널 모드. PTX → SASS 변환(nvrtc 사용 시)
```

Runtime API가 Driver API보다 한 단계 더 높은 추상화를 제공하며, 대부분의 애플리케이션(및 Triton이 생성하는 코드)은 Runtime API 레벨에서 동작한다.

## GPU 내부 구조: Streaming Multiprocessor(SM)

데이터센터 GPU와 게이밍 GPU는 세부 사항은 다르지만 **많은 구조를 공유**한다 (예: H100 기준).

```text
GPU
 ├─ SM × 다수 (Streaming Multiprocessor)
 │    ├─ L1 Instruction Cache
 │    ├─ Registers
 │    ├─ CUDA Cores + Tensor Core
 │    ├─ Load/Store Units
 │    └─ L1 Data Cache / Shared Memory
 ├─ L2 Cache (모든 SM이 공유)
 └─ Global Memory (GDDR 또는 HBM)

Host(CPU) ↔ DRAM  ↔ (PCIe 등) ↔  GPU
```

**Tensor Core**는 Ch.1에서 언급한 것처럼 GEMM/Convolution을 가속하는 전용 유닛이며, 한 명령으로 행렬곱-누산을 대량 병렬 수행한다.

## Naive PyTorch의 근본 문제: 메모리 접근 횟수

`N×N` 크기의 입력에 대해 여러 연산(예: 정규화가 포함된 softmax류 연산)을 순서대로 적용하는 naive PyTorch 코드를 생각해보자. PyTorch는 각 연산을 **개별 커널**로 실행하기 때문에, 중간 결과를 매번 GPU 메모리에 썼다가 다시 읽어야 한다.

```text
연산1: Load(N²) / Compute / Store(N)
연산2: Load(N²+N) / Compute / Store(N²)
연산3: Load(N²) / Compute / Store(N²)
연산4: Load(N²) / Compute / Store(N)
연산5: Load(N²+N) / Compute / Store(N²)

합계 — Load: 5N²+2N,  Store: 3N²+2N
```

이 다섯 번의 개별 커널 호출은 총 `8N²+4N`에 달하는 메모리 접근을 발생시킨다.

### Triton: 하나의 커널로 Fusion

Triton으로 이 연산들을 **하나의 커널로 융합(fusion)**하면, 입력을 한 번 로드하고(`N²`) 커널 내부에서 모든 연산을 레지스터/SRAM 상에서 처리한 뒤 결과를 한 번만 저장한다(`N²`).

```text
Naive PyTorch: 8N² + 4N     Triton (fused): 2N²

→ 이론상 약 4배 차이
```

이것이 앞서 본 "Triton이 naive PyTorch보다 4배 빠르다"는 실측 결과의 정확한 이유다. 단, 이 계산은 **메모리 바운드(memory-bound) 커널**에 해당하는 이야기이며, 연산량(compute) 자체가 지배적인 커널에서는 이 오버헤드를 무시할 수 있다.

> Triton이 하는 일의 본질은 Ch.2에서 언급한 "im2col의 메모리 복사 오버헤드"나 Ch.4의 "Operator Fusion"과 동일한 문제의식이다 — **다만 그 fusion을 컴파일러에 맡기는 대신, Python과 크게 다르지 않은 문법으로 직접 제어할 수 있게** 해준다는 점이 Triton의 차별점이다.

## Triton 언어 기초

Triton 커널은 다음과 같은 구조로 작성된다(개념적 순서):

1. **Input/Output Buffer**와 그 stride, 입력 shape를 받는다.
2. **Kernel Config**(튜닝 가능한 파라미터, 예: block size)를 정의한다.
3. **Block Info** — 현재 몇 번째 블록인지(`# of the block`), 전체 블록 수는 몇 개인지 계산한다.
4. **Index/Mask 생성(Load용)** — 이 블록이 담당할 데이터의 인덱스와, 경계를 넘는 부분을 걸러낼 마스크를 계산한다.
5. **Main Logic** — 데이터를 로드하고(`Load N²` 수준) 실제 연산을 수행한다.
6. **Index 생성(Store용)** — 결과를 어디에 저장할지 계산하고 저장한다(`Store N²`).

### 주요 데코레이터/키워드

| 요소 | 역할 |
|---|---|
| `@triton.jit` | 함수를 Triton 커널로 JIT 컴파일 |
| `@triton.autotune` | 여러 `configs`(가능한 커널 설정 후보)를 자동으로 시도해 최적값 탐색. 특정 값(`x_size` 등)이 바뀔 때마다 재평가됨 |
| `tl.constexpr` | 반드시 컴파일 타임 상수여야 하는 값 (예: block size) |
| `@triton.benchmark` | 커널 성능을 표준화된 방식으로 측정 |

Triton은 약 90개 이상의 연산(operation)을 언어 차원에서 제공하며([triton-lang.org](https://triton-lang.org/main/python-api/triton.language.html) 참고), 지원 하드웨어·백엔드 현황은 공식 GitHub 저장소에서 확인할 수 있다.

## PyTorch와의 통합

Triton은 독립적으로도 쓰이지만, PyTorch 생태계와 깊게 통합되어 있다:

- **TorchInductor**(Ch.4)는 GPU 백엔드로 Triton을 사용해 자동으로 커널을 생성한다.
- 사용자가 직접 작성한 Triton 커널을 `torch.compile`과 함께 사용할 수도 있다 (PyTorch 공식 튜토리얼: *torch_compile_user_defined_triton_kernel*).
- 실무에서 널리 쓰이는 커스텀 Triton 커널 모음의 예로 **Liger-Kernel**(LinkedIn 오픈소스)이 있다 — LLM 학습에 자주 쓰이는 연산(RMSNorm, RoPE, CrossEntropy 등)을 fused Triton 커널로 제공한다.

## 이 장이 완성하는 그림

Ch.4~5에서 "컴파일러가 자동으로 최적화해준다"는 이야기를 했다면, 이 장은 **그 컴파일러가 실제로 생성하는 코드의 언어(Triton)를 사람이 직접 다룰 수 있다**는 것을 보여준다. CUDA를 밑바닥부터 작성하는 것보다 훨씬 생산적이면서도, 순수 PyTorch보다는 훨씬 하드웨어에 밀착된 성능을 낼 수 있다는 점에서 Triton은 "프레임워크의 편의성"과 "하드웨어 성능" 사이의 실질적인 중간 지점을 제공한다. 이는 Ch.11에서 다룰 여러 최신 연구(신규 NPU/PIM 백엔드로의 Triton 확장 등)의 기반이기도 하다.

## 시험·복습 체크포인트

- Naive PyTorch가 Triton 대비 느린 근본 원인은 무엇인가? (메모리 접근 횟수로 설명)
- `8N²+4N` vs `2N²` 계산이 어떤 가정(memory-bound kernel) 위에서 성립하는가?
- SM(Streaming Multiprocessor) 내부에서 CUDA Core와 Tensor Core의 역할 차이는?
- `@triton.autotune`과 `tl.constexpr`이 각각 어떤 역할을 하는가?
- Triton이 TorchInductor·`torch.compile`과 어떻게 연결되는가?
{% endraw %}

---

이전: [09. LLM 추론 기초: Prefill/Decode, FlashAttention, vLLM](09-llm-inference-prefill-decode-flashattention-vllm.md) · 다음: [11. LLM 서빙·추론 가속 최신 연구 동향](11-llm-serving-research-trends.md)
