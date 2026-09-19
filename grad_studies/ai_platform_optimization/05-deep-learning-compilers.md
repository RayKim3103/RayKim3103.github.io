---
layout: page
title: "05. 딥러닝 컴파일러"
permalink: /grad_studies/ai_platform_optimization/05-deep-learning-compilers/
sitemap: false
---

- **강의**: 5. DL Compilers
- **실습**: HW1 — TVM Programming & Optimization

{% raw %}
## 개요

프레임워크(Ch.4)가 "연구자가 모델을 쉽게 표현하게 해주는 계층"이라면, 딥러닝 컴파일러는 **그 표현을 각 하드웨어(CPU/GPU/NPU/FPGA)에 맞는 고성능 실행 코드로 변환하는 계층**이다. 이 장은 초기 프레임워크(Theano)의 컴파일 파이프라인부터 시작해, 오늘날의 주요 DL 컴파일러(XLA, TensorRT, Halide, TVM, Glow)가 각각 어떤 IR(중간 표현)과 최적화 철학을 택했는지 비교한다.

## 딥러닝 프레임워크의 계보: Theano → PyTorch/TensorFlow

### Theano (2008, Univ. of Montreal / Yoshua Bengio Group)

최초의 딥러닝 프레임워크 중 하나로, Python DSL 기반 **symbolic expression graph**를 구성해 다차원 배열 연산을 정의·최적화·평가했다.

**Theano의 5단계 컴파일 파이프라인**:

```text
Canonicalization → Stabilization → Specialization → GPU Transfer → Code Generation
```

| 단계 | 목적 | 예시 |
|---|---|---|
| Canonicalization | 수식 정규화/단순화, 중복 코드 제거 | `sqrt(x*x) = x`, gradient rewrite로 생기는 `x-x = 0` 같은 중복 제거 |
| Stabilization | 수치 안정성 개선 | `log(1+exp(x))`가 `x>709`에서 오버플로 → `x`로 재작성 |
| Specialization | 서브그래프를 더 빠른 형태로 재작성 | `pow(x,2)→sqr(x)`, 최적 BLAS GEMM 루틴 매핑, element-wise 연산 fusion |
| GPU Transfer | 가능한 연산을 GPU 표현으로 전환 | 데이터 이동을 위한 copy routine 삽입 |
| Code Generation | 동적 컴파일된 Python 모듈 생성 | dtype별 특화 C++/CUDA 구현 링크 |

Theano는 2016년까지는 TensorFlow와 경쟁력이 있었지만, (1) 거대 IT 기업의 공식 지원 부재, (2) 멀티 GPU/분산 처리 미지원, (3) 부족한 내장 연산자, (4) TensorBoard 같은 시각화 도구 부재로 결국 유지보수가 중단되었다.

### PyTorch / TensorFlow 요약 (Ch.4 연결)

- **PyTorch**: NumPy처럼 동작하는 Imperative 라이브러리. Tensor/Variable/Module 3단 추상화(Ch.4 참고)
- **TensorFlow**: dataflow 프로그래밍 추상화(구글의 분산 처리 시스템에서 영감), 2015년 11월 출시, 디바이스별 연산 배치(fine-grained placement) 지원. Keras가 고수준 API로 결합

> 참고 논문: *TensorFlow: A System for Large-Scale Machine Learning* (OSDI'16), *Theano: A CPU and GPU Math Compiler in Python* (SciPy'10), *Automatic Differentiation in ML: Where We Are and Where We Should Be Going* (NeurIPS'18)

## DL 실행 모델의 근본적 문제

프레임워크는 기본적으로 **연산자를 하나씩 실행(interpreted execution)**한다. 문제는:

- 느리다 (매 연산마다 Python↔C++ 오버헤드)
- 데이터를 여러 번 훑는다(multiple scans) → 큰 메모리 전송 필요
- 새 연산자·새 하드웨어를 타깃하기 어렵다

이를 해결하려면 하드웨어 특성(이기종성, 복잡한 메모리 계층, 스레드/SIMD/Tensor Core 같은 병렬 프리미티브)을 고려한 **operator 구현 자체의 최적화**(loop order, tiling, memory layout)가 필요하다 — 이것이 DL 컴파일러의 존재 이유다.

## IR(중간 표현) 접근 방식 비교

| 접근 | 대표 시스템 | 초점 |
|---|---|---|
| **Computation Graph 기반** | XLA, TensorRT | DAG 최적화(연산 fusion, no-op 제거), 기존 텐서 연산 라이브러리를 다수 포함 |
| **Tensor Loop Algebra** | Halide, Tensor Comprehensions | DAG을 낮은 레벨(loop order/tiling/memory layout)까지 최적화, 신규 연산자 커스터마이징 지원 |
| **두 접근을 모두 채택** | **TVM** | 그래프 레벨 + 텐서/루프 레벨 최적화를 모두 수행 |

## TensorFlow XLA (Accelerated Linear Algebra)

- **HLO(High Level Operations)**: 텐서 연산을 위한 IR — Map, Broadcast, Reduce, Convolution 같은 저수준 텐서 프리미티브
- 최적화: **커널 fusion**(중복 메모리 접근 제거), 데이터 레이아웃 최적화, op dispatch 오버헤드 최소화, 루프 unrolling/vectorization
- **JIT**(런타임에 낮은 오버헤드로 컴파일) + **모바일 AOT**(모델을 실행 파일로 사전 변환, ARM/x86 지원) 모두 지원
- 백엔드: CPU/OpenCL은 LLVM, CUDA는 LLVM:NVPTX — StreamExecutor로 여러 타깃을 추상화

## NVIDIA TensorRT

- Weight quantization 지원
- **수직/수평 커널 fusion**을 모두 지원 (수직: 순차적인 Conv-BN-ReLU 결합, 수평: 같은 입력을 쓰는 병렬 연산 결합)

## Halide: 이미지 처리를 위한 언어·컴파일러

핵심 아이디어는 **"알고리즘(무엇을 계산할지)"과 "스케줄(언제·어디서 계산할지)"의 분리(decoupling)**다.

```text
Algorithm: 사용자가 직접 정의 (계산 로직)
Schedule:  프레임워크가 탐색 (split/vectorize/fuse/tile/parallelize 최적화 지점) — 오토튜너 활용 가능
```

- **장점**: 멀티스레드/CUDA/OpenCL을 직접 짜는 것보다 훨씬 단순
- **단점**: 여전히 고성능을 위해서는 도메인 지식이 필요하고, 원래 이미지 처리용으로 설계되어 딥러닝 전용은 아니다 — Tensor Comprehensions와 TVM이 Halide의 아이디어를 차용하되 일부 Halide 특유의 한계를 넘어선다.

## TVM: An Automated End-to-End Optimizing Compiler for Deep Learning

TVM은 Halide의 "알고리즘/스케줄 분리" 철학을 계승하면서, **Tensor Expression Language**로 DL 특화 IR을 설계했다.

### TVM Stack 계층 구조

```text
High-level Graph Rewriting          (Graph Level Optimization)
     ↓  Computation Graph 구성·최적화
Tensor-level Optimization/Scheduling/Tuning   (Tensor Level Optimization)
     ↓  Tensor Expression, schedule primitives
Backend Code Generation             (Backend Compiler)
     ↓ NVCC / LLVM / OpenCL / VTA ISA / 커스텀 ISA
GPU / CPU / Mobile / VTA(FPGA) / NPU / New HW
```

### 스케줄링 예시: Vector Add

```python
C = tvm.compute((n,), lambda i: A[i] + B[i])
s = tvm.create_schedule(C.op)

xo, xi = s[C].split(s[C].axis[0], factor=32)
s[C].reorder(xi, xo)
s[C].bind(xo, tvm.thread_axis("blockIdx.x"))
s[C].bind(xi, tvm.thread_axis("threadIdx.x"))
```

동일한 `C[i]=A[i]+B[i]` 알고리즘이라도, `split`(32개씩 나누기) → `reorder`(루프 순서 변경) → `bind`(GPU의 block/thread에 매핑)이라는 스케줄 프리미티브를 통해 완전히 다른 실행 코드(CUDA 커널)로 컴파일된다 — 이것이 "알고리즘과 스케줄의 분리"가 실제로 의미하는 바다.

### 저수준 최적화

- **Thread Cooperation**, **Tensorization**(텐서 연산 전용 하드웨어 명령 매핑), **Latency Hiding**
- **Pipelined execution**: 순차 실행 대신 `Input/Weight 로드`와 `MatMul 연산`을 파이프라인으로 겹쳐 실행해 지연시간을 숨김(latency hiding). 단, 의존성 추적(dependency tracking)이 필요하다.

### AutoTVM (NeurIPS 2018)

> "We learn domain-specific statistical cost models to guide the search of tensor operator implementations over billions of possible program variants. We further accelerate the search using effective model transfer across workloads."

수동으로 스케줄을 튜닝하는 대신, **학습 기반 비용 모델**로 수십억 개에 달하는 가능한 프로그램 변형 중 최적의 것을 탐색한다.

## Glow (Facebook): Graph Lowering Compiler

```text
Inputs(ONNX/PyTorch/C++API) → Glow Core(Optimizer/Quantizer/PGO) → CodeGen → Backend(A/B/C, CPU 등)
```

- **High-level Optimizer**: 정적 dataflow 그래프에 대한 선형대수 최적화(레이아웃 변경, 연산 fusion 등)
- **Low-level IR**: 버퍼를 가리키는 typed pointer 기반의 선형 명령어 표현 — instruction scheduling, buffer sharing 등 메모리 최적화에 집중
- **Graph Lowering**: 고수준 노드를 primitive 노드로 낮춤
- **Profile-guided Quantization**: 프로파일 데이터로 각 엣지의 값 범위를 추정해, 노드 간 불필요한 스케일 변환을 제거

## 실무: TVM 실습 (HW1)과 메모리/프루닝으로의 연결 (강의 노트 기반)

### HW1 — TVM Programming & Optimization

강의 HW1은 실제로 TVM(v0.17.0, CUDA+LLVM 필요)을 설치하고 다음을 수행한다: (1) 네트워크를 TVM으로 로드해 연산 결과 확인, (2) 여러 연산 최적화 기법 적용, (3) Convolution 연산 시간 측정, (4) Relay Pass를 이용한 그래프 최적화, (5) VTA(FPGA 가속기 시뮬레이터) 상에서의 최적화. 이 실습은 위에서 다룬 **Relay(그래프 레벨 IR) → Schedule(텐서 레벨 최적화) → Code Generation**이라는 TVM 파이프라인을 직접 손으로 경험하는 과정이다.

### 실무 관점: TVM vs PyTorch Inductor, 그리고 다음 주제로의 연결

- **PyTorch Inductor**는 PyTorch 사용자 편의성이 최강이며 `torch.compile()` 한 줄로 통합된다(Ch.4).
- **TVM**은 새로운 하드웨어(NPU/CPU/GPU)에 자유롭게 백엔드를 붙이고, Schedule로 훨씬 세밀한 최적화가 가능하다. NPU 회사들이 PyTorch에 직접 backend를 붙이는 작업(C10/LibTorch 연동)이 복잡하기 때문에, TVM 스타일의 컴파일러 스택이 신규 하드웨어 온보딩의 표준 경로로 쓰이는 경우가 많다.
- 컴파일러가 메모리를 어떻게 다루는지(Memoization vs **Rematerialization/Checkpointing**)와, 모델을 어떻게 더 작게 만들 것인지(**Pruning/Sparsity**)는 다음 장(Ch.6)에서 본격적으로 다룬다 — 특히 Rematerialization은 "GPU 메모리가 부족할 때 forward의 모든 activation을 저장하는 대신 일부만 저장하고 backward에서 재계산"하는 기법으로, 현재 LLM 학습/추론의 표준 기법이다.

## 시험·복습 체크포인트

- Theano의 5단계 컴파일 파이프라인 각각이 하는 일은?
- Computation-graph 기반 IR(XLA/TensorRT)과 Tensor-loop-algebra 기반 IR(Halide)의 차이는? TVM은 왜 "둘 다"로 분류되는가?
- Halide/TVM에서 "알고리즘과 스케줄의 분리"가 왜 중요한가?
- TVM 스택에서 Graph-level, Tensor-level, Backend-level 최적화가 각각 담당하는 범위는?
- AutoTVM이 스케줄 탐색 문제를 어떻게 다루는가?
- Glow의 High-level Optimizer와 Low-level IR이 각각 최적화하는 대상은?
{% endraw %}

---

이전: [04. PyTorch 내부 구조와 PyTorch 2.x 컴파일 스택](04-pytorch-internals-and-pytorch2-compilation.md) · 다음: [06. 메모리 최적화와 네트워크 프루닝](06-memory-optimization-and-network-pruning.md)
