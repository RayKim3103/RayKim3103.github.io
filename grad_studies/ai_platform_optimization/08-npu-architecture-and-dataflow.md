---
layout: page
title: "08. NPU 아키텍처와 데이터플로우"
permalink: /grad_studies/ai_platform_optimization/08-npu-architecture-and-dataflow/
sitemap: false
---

- **강의**: 7. Neural Accelerators & LLM Basics (Part 1: NPU 아키텍처)
- **실습**: HW2 — OpenCL GEMM Optimization

{% raw %}
## 개요

지금까지는 소프트웨어(프레임워크·컴파일러)가 어떻게 DNN 연산을 최적화하는지를 봤다면, 이 장은 **그 연산을 실제로 실행하는 하드웨어(NPU/가속기) 자체를 어떻게 설계하는가**를 다룬다. 핵심 질문은 "왜 GPU/CPU만으로는 부족한가?"와 "데이터 이동(data movement)을 최소화하려면 하드웨어를 어떻게 구성해야 하는가"이다.

## 신경망 가속을 위한 하드웨어 지형 (재정리)

| 하드웨어 | 특징 | 전력 효율 |
|---|---|---|
| GPU | 범용, CUDA 생태계 | ~0.05 TFLOPS/W |
| Azure Brainwave/Catapult (FPGA) | 재구성 가능한 클러스터 | ~0.3 TFLOPS/W |
| Nervana (LakeCrest) | Flexpoint 연산 | - |
| TPU | TensorFlow 전용 ASIC, 고정소수점 | ~2.3 TOPS/W |
| Cambricon (DianNao) | 화웨이 Kirin 탑재 | ~1 TFLOPS/W |
| EIE/ESE | FPGA, SW/HW Co-design | - |

## DNN 연산 효율화의 핵심 원칙

```text
Locality (지역성) + Parallelism (병렬성)
```

메모리 접근은 연산보다 훨씬 많은 에너지를 소모한다. 따라서 NPU 설계의 제1원칙은 **"최고 속도"가 아니라 전력 대비 성능(전성비)의 극대화**이며, 이를 위해 데이터 재사용(data reuse)을 극한으로 끌어올려야 한다.

- **시간적 재사용(Temporal reuse)**: 같은 데이터를 같은 소비자가 시간상 여러 번 사용 — 작은 고속 로컬 메모리(scratchpad)에 데이터를 가져온 뒤 최대한 반복 사용하고 버리는 "최적의 타이밍"을 찾는 것이 핵심.
- **공간적 재사용(Spatial reuse)**: 같은 데이터를 여러 소비자(하드웨어 상의 여러 연산 유닛)가 서로 다른 위치에서 동시에 사용 — 하나의 데이터를 여러 PE(Processing Element)에 동시에 뿌리는(broadcast) 방식.

## Dataflow: Output Stationary vs Weight Stationary

Dataflow는 DNN 연산의 실행 순서(연산 순서 + 데이터 이동 순서)를 정의하며, **루프 네스트(loop nest)**로 표현할 수 있다 (`for`=시간적 순서, `spatial_for`=병렬 실행).

```c
// Output Stationary (OS) — 보통 GPU
for (q=0; q<Q; q++) {
  for (s=0; s<S; s++) {
    OA[q] += IA[q+s] * W[s];
  }
}

// Weight Stationary (WS) — 보통 NPU
for (s=0; s<S; s++) {
  for (q=0; q<Q; q++) {
    OA[q] += IA[q+s] * W[s];
  }
}
```

같은 연산이라도 루프 순서만 바꾸면 **어떤 값이 하드웨어 레지스터에 고정(stationary)되어 재사용되는지**가 완전히 달라진다.

| 구분 | Output Stationary (OS) | Weight Stationary (WS) |
|---|---|---|
| 개념 | 출력값(Output)을 고정하고, 이를 만드는 데 필요한 입력·가중치를 순서대로 가져옴 | 가중치(Weight)를 연산기에 고정하고, 입력 데이터를 계속 흘려보내며 곱함 |
| 주 사용처 | GPU (스레드 블록 단위 매핑) | NPU, TPU (systolic array 기반) |
| 장점 | 최종 결과가 만들어질 때까지 캐시에 유지되어, 출력 데이터의 메모리 쓰기 연산을 최소화 | 가장 무거운 데이터(가중치)를 레지스터에 가둬 메모리 읽기 전력을 크게 절감 |
| 단점/특징 | 입력·가중치가 지속적으로 교체됨 | 완성되지 않은 부분합(partial sum)이 생겨, 순차적으로 더해주는 **Reduction 과정**이 필수 |

## DNN 가속기의 비효율 원인과 최적화 대상

CPU의 비효율성 원인(범용 명령어 디코딩, 제어 로직 등)을 제거하는 것이 NPU 설계의 목표다. 핵심 최적화 대상은 세 가지다: **명령어 디코딩 로직, 데이터패스(Datapath), 메모리 시스템**.

### 1) 명령어 디코딩 로직 최적화 — Coarse-Grained ISA

CPU는 명령어를 가져오고(fetch) 해독하는(decode) 데 막대한 전력을 소모한다. NPU는 **데이터 이동과 연산이라는 핵심 동작만을 다루는, 소수(5~10개 내외)의 크고 굵직한 CISC 스타일 명령어**로 구성한다.

- **TPU ISA**: 행렬곱·데이터 read/write 등 굵직한 명령어 소수로 구성
- **NVDLA ISA**: 유사한 철학
- **Gemmini ISA** (예시)
  - 데이터 이동: `mvin`(L2/DRAM→scratchpad), `mvout`(scratchpad→L2/DRAM)
  - 연산: `matmul.preload`(weight-stationary용 preload), `matmul.compute.preloaded`, `matmul.compute.accumulated`
  - 설정: `config_ex`/`config_mvin`/`config_mout`, `flush`(TLB flush)

**Operation Fusion**: Conv-ReLU-Pooling(CRP)처럼 여러 연산자를 하나로 합쳐, 중간 결과를 메모리에 썼다가 다시 읽어오는 비효율을 제거한다. Matrix-Multiply 직후 활성화 함수(예: ReLU)를 곧바로 적용하는 식의 **하드웨어 레벨 연산 융합(fusion)**은 NPU에서 표준적인 최적화다.

### 2) 데이터패스(Datapath) 최적화

**Spatial-K (누산/reduction 차원에 적용, 예: Convolution의 R/S/C)**

| 방식 | 예시 | 특징 |
|---|---|---|
| Adder Tree | NVDLA, DianNao | 한 사이클에 트리 형태로 동시 합산. 직관적이고 지연시간 짧지만, 연산기가 많아질수록 배선이 길어져 스케일업에 한계 |
| Systolic Accumulation | TPU, Gemmini | 파이프라인을 따라 사이클마다 누적 덧셈. 지연시간은 길어 보이지만 구조가 정교하고 확장이 쉬워 대규모 처리에 유리 |

**Spatial-N (비누산 차원에 적용)**

| 방식 | 예시 |
|---|---|
| Direct-wiring multicast | NVDLA, DianNao |
| Systolic multicast | TPU, Gemmini |

**TPU vs NVDLA 조합**: TPU는 Systolic accumulation + Systolic multicast, NVDLA는 Adder-tree accumulation + Direct-wiring multicast를 채택한다.

### 3) 메모리 최적화

- 짧게 존재하는 중간 결과(예: adder tree의 partial sum)를 직접 소비
- 애플리케이션 특화 데이터 저장 크기·대역폭(전용 weight/input/output buffer)
- 애플리케이션 특화 데이터 전달 네트워크(예: **double-buffering** — 다음 연산에 쓸 데이터를 미리 읽어와 대기시간을 숨김)

### TPU 종합: 세 최적화의 결합

```text
Inst. Decoding: 굵직한 matmul/data read-write 명령어
Datapath: Spatial-K(Systolic Accumulation) + Spatial-N(Systolic Multicast), 멀티사이클+레지스터, 우수한 확장성
Memory: 커스텀 systolic 레지스터, 전용 accumulation/weight buffer, double-buffered weight-stationary dataflow
```

## DNN을 하드웨어에 매핑하기 (Mapping Problem)

같은 DNN이라도 **하드웨어 제약**(systolic array 크기, weight buffer 크기, 공유 버퍼 존재 여부 등)에 따라 최적의 매핑이 달라진다.

```text
DNN dimensions + HW constraints  →  Mapping(Blocking/Tiling/Compiling)  →  Execution Order(타깃 HW 상에서)
```

**매핑의 세 차원**:

1. **Loop ordering**: 어떤 인덱스를 안쪽/바깥쪽 루프에 둘 것인가
2. **Loop bounds**: 각 루프의 타일 크기(N, K, M 등)를 얼마로 할 것인가
3. **Spatial choice**: 어떤 루프를 공간적(병렬)으로, 어떤 루프를 시간적으로 실행할 것인가 — Data/Model Parallelism 선택과 직결

### 성능 튜닝을 최적화 문제로 보기

주어진 DNN 차원(N,H,W,C,R,S,K,stride,padding)과 하드웨어 사양(dataflow, 메모리 계층)으로부터, 지연시간/에너지를 최소화하는 최적의 루프 네스트(시간적+공간적 실행 순서 모두)를 찾는 문제다. 접근법은 완전 탐색(exhaustive), 무작위 탐색(random), **학습 기반 탐색**으로 나뉜다.

- **FlexFlow** (SysML'18): MCMC 탐색 알고리즘으로 병렬화 전략 공간을 탐색하고, 실행 시뮬레이터로 후보를 평가
- **TVM/AutoTVM** (NeurIPS'18): 도메인 특화 통계적 비용 모델을 학습해 수십억 개의 텐서 연산 구현 변형 중 탐색을 가속 (Ch.5 참고)

### Neural Architecture Search (NAS)

레이어 수·연결·타입 등 방대한 하이퍼파라미터 공간에서 수동으로 최적 아키텍처를 찾는 것은 매우 번거롭다. NAS는 **탐색 공간에서 아키텍처를 선택 → 성능 추정치 반환**하는 과정을 자동화한다 (예: FBNet — 하드웨어 인지형 미분 가능 NAS).

## 대표 하드웨어 사례

### Google TPU 세대별 스펙

| 버전 | 핵심 스펙 |
|---|---|
| **TPU v1** | 65,536(256×256)개 8-bit 정수 MAC (Matrix Unit), 700MHz, 최대 92 TOPS/s(=65536×2×700M), GPU 대비 MAC 수 25배 이상, CPU 대비 100배 이상, 24MiB on-chip Unified Buffer, DDR3 8GiB off-chip weight memory |
| **TPU v2** | HBM 8GiB/core, MXU 1개/core, 45 TFLOPS/chip, 최대 512 core(4TiB), 학습+추론 모두 지원 |
| **TPU v3** | HBM 16GiB/core, MXU 2개/core, 90 TFLOPS/chip, 최대 2048 core(32TiB) |
| **TPU v2 Pod** | 64보드(2랙)/256칩/128 Xeon CPU, 4TB HBM, 11.5 petaflops |
| **TPU v3 Pod** | 256보드(8랙)/1024칩, v2 Pod 대비 8배, 100+ petaflops |

### NVDLA (NVIDIA Deep Learning Accelerator)

**하드웨어 구성 요소**와 TensorFlow 연산 매핑:

| 엔진 | 매핑되는 TF 연산 |
|---|---|
| Convolution Engine | `tf.nn.conv2d` |
| Single Data Point Processor | `batch_normalization`, `bias_add`, `elu`, `relu`, `sigmoid`, `tanh` 등 |
| Planar Data Processor | `avg_pool`, `max_pool`, `pool` |
| Cross-channel Data Processor | `local_response_normalization` |
| Data Reshape Engine | `conv2d_transpose`, `concat`, `slice`, `transpose` |
| Bridge DMA | 시스템 DRAM ↔ 전용 고성능 메모리 인터페이스 간 데이터 복사 |

**소프트웨어 스택**: 컴파일 도구(Caffe 모델 → IR → HW 레이어 네트워크로 변환, Winograd/basic convolution 알고리즘 선택, 정밀도 양자화, weight 메모리 영역 할당)와 런타임(User Mode Driver가 NVDLA Loadable 파일 생성·제출, Kernel Mode Driver가 레지스터 프로그래밍·레이어 스케줄링 수행)으로 구성된다.

### Tesla Full Self-Driving(FSD) 컴퓨터

- 3× quad-core Cortex-A72(총 12 CPU @2.2GHz, 범용 처리) + Mali G71 MP12 GPU @1GHz(후처리) + **커스텀 NPU 2개** @2GHz
- LPDDR4-4266 최대 128비트, TDP 36W (2019년 출시)
- **NPU 스펙**: SRAM(메인 메모리로의 데이터 이동을 줄이는 중간 결과 저장), MAC 총 9216개(8bit×8bit 정수 곱 + 32bit 정수 덧셈으로 전력 절감), 칩당 36.86 TOPS(총 73.7 TOPS)
- **컴파일러 지원**: Conv-Scale-Act-Pooling을 결합하는 레이어 퓨전, 뱅크 충돌을 줄이는 채널 패딩, 사용 전 프리페치를 위한 DMA 삽입
- **전용 명령어**: DMA 2종(read/write), dot-product 3종(convolution/deconvolution/inner-product), scale(1입력 1출력), eltwise(2입력 1출력)

## 실무: CUDA/OpenCL 프로그래밍 모델과 차세대 메모리 (강의 노트 기반)

### CUDA vs OpenCL

- **CUDA**: 프로그래밍이 편리하지만 NVIDIA 하드웨어에 종속적.
- **OpenCL**: CPU, AMD GPU, 모바일/엣지 NPU 등 다양한 하드웨어에서 구동 가능한 범용성을 갖췄지만, 코드 작성이 까다롭다.
- **최신 흐름 — Translator**: 최근에는 OpenCL로 직접 코드를 짜기보다, 기존 CUDA 코드를 다른 벤더 환경으로 자동 변환하는 도구(예: AMD의 HIP, ROCm)를 쓰는 추세다.

> **SW/HW 불일치 함정**: (1) 프로그래밍 모델이 BF16을 지원해도, 실제 구형 GPU에 BF16 네이티브 연산 유닛이 없으면 FP32로 강제 변환 후 재변환해야 하므로 — 메모리는 적게 쓰지만 오히려 **속도가 느려질 수 있다.** (2) 컴파일러 옵션에서 타깃 하드웨어의 shared memory를 64KB로 잡고 빌드했는데 실제 구형 칩이 16KB라면, 런타임에 실행 자체가 거부(crash)된다. **컴파일이 성공했다고 하드웨어에서 정상 동작한다는 보장은 없다.**

### HW2 실습 — OpenCL SGEMM 최적화

HW2는 이 CUDA/OpenCL 프로그래밍 모델을 직접 다루는 실습이다: (Step I) OpenCL host/kernel 구성, (Step II~III) Naïve SGEMM 구현, (Step IV) **loop unrolling**을 적용한 SGEMM, (Step V) **vectorization**을 적용한 SGEMM. 이 실습은 위에서 다룬 데이터패스·메모리 최적화 원칙을 GPU(OpenCL) 커널 레벨에서 직접 체험하는 과정이며, Ch.10(Triton)에서 배우게 될 "더 높은 생산성으로 같은 최적화를 수행하는 방법"과 자연스럽게 비교된다.

### Systolic Array가 표준이 된 이유

연산기(PE)를 매우 많이 한 번에 연결하면 **fan-out이 커져 물리적 지연(RC delay)**이 발생한다. 이를 해결하기 위해 데이터를 2D 배열을 따라 사이클 단위로 순차적으로 흘려보내는 **Systolic Array** 구조가 최신 NPU의 표준이 되었다 — 위에서 다룬 Spatial-K/N의 "Systolic" 계열 선택지가 바로 이 구조를 가리킨다.

### 메모리 병목과 차세대 인터커넥트: CXL, Processing-Near-Storage

연산 유닛의 속도는 충분히 빠르지만, 데이터를 공급하는 메모리 대역폭이 따라가지 못해 병목이 발생하는 "메모리 벽(Memory Wall)" 문제가 갈수록 심각해지고 있다.

- **CXL (Compute Express Link)**: PCIe 인터페이스 기반으로 메모리를 사실상 무한정 확장할 수 있는 기술. 속도는 PCIe를 경유하므로 다소 느리지만, 구형 서버에서 나오는 저렴한 DRAM을 모아 거대한 메모리 풀(pool)을 구성할 수 있어 **비용 효율이 매우 높다** — 최근 다시 주목받는 이유다.
- **Processing Near Storage (Smart SSD)**: 메인 메모리에 다 올릴 수 없는 거대한 데이터를 처리할 때, 호스트로 데이터를 전부 보내는 대신 SSD 내부 연산기가 1차 필터링을 수행해 대역폭 낭비를 줄인다.

이 CXL 기반 메모리 확장과 PIM(Processing-In-Memory) 아키텍처는 Ch.11(LLM 서빙 최신 연구 동향)에서 LLM 추론 가속의 핵심 트렌드로 다시 등장한다.

## 시험·복습 체크포인트

- Output Stationary와 Weight Stationary dataflow의 차이, 그리고 각각이 GPU/NPU에서 선호되는 이유는?
- Adder Tree와 Systolic Accumulation의 트레이드오프(지연시간 vs 확장성)는?
- NPU가 CISC 스타일의 굵은 명령어를 쓰는 이유는? Operation Fusion이 필요한 이유는?
- DNN 매핑 문제의 세 차원(loop ordering/bounds/spatial choice)은 무엇을 결정하는가?
- TPU v1의 92 TOPS 피크 성능이 어떻게 계산되는가?
- BF16을 지원한다는 프로그래밍 모델이 실제로는 느려질 수 있는 이유는?
- CXL이 메모리 확장에서 갖는 장단점은?
{% endraw %}

---

이전: [07. 엣지·CPU 추론 최적화](07-edge-and-cpu-inference-optimization.md) · 다음: [09. LLM 추론 기초: Prefill/Decode, FlashAttention, vLLM](09-llm-inference-prefill-decode-flashattention-vllm.md)
