---
layout: page
title: "11. LLM 서빙·추론 가속 최신 연구 동향"
permalink: /grad_studies/ai_platform_optimization/11-llm-serving-research-trends/
sitemap: false
---

- **강의**: Project Proposal(11주) / Paper Presentation & Project Checkpoint(12~15주) — 팀 프로젝트 발표 종합

{% raw %}
## 개요

이 장은 특정 강의 슬라이드가 아니라, 학기 후반(11~15주)에 진행된 **학생 팀 프로젝트 제안·중간점검·논문 발표**를 종합한 것이다. 시험이 없는 대신 팀 프로젝트가 성적의 절반을 차지하는 이 강의의 특성상, 이 발표들은 **2025~2026년 시점 AI 플랫폼 최적화 연구의 최전선**을 상당히 폭넓게 보여준다. 지금까지의 장들(Ch.2~10)에서 배운 기초 개념 — 양자화, 프루닝, 컴파일러, NPU 데이터플로우, Prefill/Decode, FlashAttention — 이 실제 연구에서 어떻게 조합·응용되는지 확인하는 것이 이 장의 목적이다.

전체 주제를 네 개의 축으로 묶어 정리한다.

```text
Part 1. 저정밀 수치 표현과 하드웨어 PE 설계
Part 2. LLM 하드웨어 가속 아키텍처 (PIM/CXL/NPU)
Part 3. 컴파일러와 오토튜닝
Part 4. LLM 서빙 시스템과 스케줄링
Part 5. Diffusion LLM 추론 최적화
```

> 이 장의 수치·실험 결과 중 일부는 발표 녹취를 재구성한 것이라 정밀도가 낮을 수 있다. 정확한 수치보다 **"어떤 문제의식으로, 어떤 방향의 해법을 제시했는가"**라는 구조에 집중해서 읽는 것을 권장한다.

## Part 1. 저정밀 수치 표현과 하드웨어 PE 설계

Ch.3에서 다룬 양자화 기초(PTQ/QAT, mixed precision, HAWQ-V3)가 LLM 시대에 어떻게 더 정교해지는지를 보여주는 주제들이다.

### AWQ (Activation-aware Weight Quantization)

- **배경**: 엣지 디바이스에서 LLM을 구동하려면 양자화가 필수지만, 기존 PTQ는 성능 저하가 크다.
- **핵심 아이디어**: 가중치 자체가 아니라 **활성화 값(activation)의 크기(magnitude)를 기준**으로 중요한 채널(salient channel)을 선별한다.
- **원리**: 중요한 가중치 채널에는 스케일링 팩터 `s`를 곱하고, 대응하는 활성화 값에는 `1/s`를 나눈다. 결과적으로 라운딩 오차가 `1/s`만큼 줄어들어 양자화 오류가 크게 감소한다.
- **탐색**: 라운딩 연산은 미분 불가능하므로, 역전파 대신 **그리드 서치(grid search)**로 최적 스케일링 팩터 `α`를 찾는다.

### 선형성 정리(Linearity Theorem) 기반 예산 할당 양자화

- 모델 전체의 손실(perplexity)과 각 레이어의 오차(MSE) 사이의 관계를 이론적으로 연결한다.
- 민감도(sensitivity)가 높은 레이어에는 더 많은 비트 예산을, 낮은 레이어에는 적은 예산을 할당하는 **mixed-precision 동적 프로그래밍**을 적용한다 — Ch.3의 HAWQ-V3(ILP 기반)와 유사한 문제의식을, 동적 프로그래밍으로 접근한 변형이다.
- **Data-free 양자화**: calibration 데이터가 없을 때는 가우시안 노이즈를 주입해 레이어별 민감도를 근사 측정한다.

### 부동소수점 엔트로피 코딩

지수(exponent)와 가수(mantissa)의 등장 빈도가 균일하지 않다는 점(특히 지수는 특정 값이 자주 등장 — 낮은 엔트로피)에 착안해, 빈번한 값에는 짧은 비트를, 드문 값에는 긴 비트를 배정하는 **허프만 코딩 계열 압축**을 적용한다. 이를 통해 성능 저하 없이 모델 용량과 KV Cache 크기를 크게 줄인다.

### 비트 레벨 희소성 (Bit-level Sparsity)

16비트 데이터를 4비트 슬라이스 4개로 분할한 뒤, 값이 0인 **제로 슬라이스(zero-slice)** 연산을 하드웨어 단(멀티플렉서 활용)에서 스킵해 연산 효율과 처리량을 높인다.

### 저정밀 Floating-Point/Block-FP 연산기: outlier와 metadata의 트레이드오프

MXFP4, UVFP4, BF16 등 FP4 계열 저정밀 포맷 연구가 공통으로 마주하는 구조적 문제:

```text
저정밀 표현 채택 → 저장/연산량 감소
       ↓
dynamic range 축소 → outlier 표현이 어려워짐
       ↓
outlier 처리용 별도 metadata(scale factor, outlier list) 필요
       ↓
metadata/encoding-decoding 비용 증가 → 하드웨어 면적·전력 이득 감소
```

**해법 방향**: 기존 FP multiplier 대신 연산을 **shift-add 중심**으로 단순화한다. Block Floating-Point에서는 같은 블록 안의 값들이 공통 scale을 공유하므로, exponent 차이를 shift로 처리하면 multiplier 비용을 줄일 수 있다. 실제 weight vector 기반 스위칭 활동으로 평가했을 때 baseline 대비 **면적/전력 약 2.3배 절감**, 다만 end-to-end 성능은 baseline 대비 약 40% 느려지는 트레이드오프가 관찰되었다.

> **연구 방법론 팁**: 단순 random test vector는 dynamic power를 잘 반영하지 못한다. 실제 weight나 실제 연산 시퀀스 기반 test vector를 구성해야 power 평가의 설득력이 올라간다.

### Hybrid Delta Exponent / Floating-Coin

여러 커스텀 저정밀 포맷(FPC6, FPC8, FPC24, BF16 등)을 하나의 유연한 하드웨어로 지원하려는 시도.

- **Hybrid Delta Exponent**: 각 값의 exponent를 그대로 저장하는 대신, 기준 exponent와의 **차이(delta)만 저장**해 metadata 비트 수를 줄인다.
- **Floating-Coin**: 여러 정밀도 구성요소(FPC6/8/24)를 같은 하드웨어 구조 안에서 조합하거나 zero-padding으로 맞추는 방식.
- **평가**: 정확도 손실은 매우 작음(accuracy 감소 약 0.0005, perplexity 증가 약 0.0003 수준), 하드웨어 합성 결과 baseline 대비 **면적 효율 약 3.8배, 전력 효율 약 4.8배** 개선.
- **확장성이 핵심 평가 기준**: "특정 포맷 전용"이 아니라 "조합형/패딩형/비율 조정형 구조라 새로운 포맷에도 확장 가능하다"는 주장이 논문화에 있어 중요한 포인트로 지적되었다.

### LLM 프루닝의 최신 흐름 (Ch.6의 심화)

- **Weight + Activation 결합 분석**: 단순히 가중치만 보지 않고, 실제 실행 시 발생하는 활성화 값까지 함께 분석해 원본 모델과의 출력 차이(error)를 최소화하는 방향으로 발전.
- **N:M Sparsity**: NVIDIA GPU 등에서 하드웨어 가속이 가능한 구조적 희소성 패턴(N개의 연속된 가중치 중 M개만 남김) — Ch.6의 Structured Sparsity가 실무에서 구체화된 형태.
- **Calibration 데이터의 중요성**: 프루닝 품질은 calibration 데이터가 모델의 사전학습(pre-training) 데이터 분포와 얼마나 유사한지에 크게 좌우된다.

### 부동소수점→정수(FP-to-INT) 변환 기반 PE 설계

FP16/BF16을 INT4 등 정수형으로 변환해 1:4 수준의 높은 하드웨어 병렬성을 확보하는 접근. 그룹 단위로 최대 지수(exponent)를 추출하고 나머지를 shift해 정수 곱셈기만으로 연산하되, 비트폭 증가로 인한 하드웨어 오버헤드를 억제하는 PE 디자인(Scale-Sim으로 검증).

## Part 2. LLM 하드웨어 가속 아키텍처 (PIM/CXL/NPU)

Ch.8(NPU 아키텍처)에서 다룬 데이터플로우·메모리 설계 원칙이, LLM의 Prefill/Decode(Ch.9) 특성과 결합해 어떻게 확장되는지를 보여준다.

### CENT/CXL-PIM 구조에 GEMM 유닛 추가

**배경**: CENT는 CXL 기반 PIM(Processing-In-Memory) LLM 추론 구조로, 행렬곱을 PIM 내부에서 **GEMV**(행렬-벡터 곱) 형태로 풀어 처리한다. 하지만 Ch.9에서 본 것처럼 **Prefill 단계는 본질적으로 GEMM(행렬-행렬 곱) 연산**이므로, 기존 CENT 구조에서는 GEMM을 GEMV로 언롤링(unrolling)하는 오버헤드가 발생한다.

**제안**: CXL 컨트롤러 쪽에 **전용 GEMM 유닛**을 추가하고, Q/K/V/Output projection과 FFN 계열(GEMM 대상) 연산을 GEMM 경로로 보낸다. 반면 attention score 계산(`QKᵀ`, `SV`)과 KV-cache 접근처럼 memory-bound 성격이 강한 연산은 기존 PIM(GEMV) 경로를 유지한다.

| 연산 | 기존 CENT | 제안 구조 |
|---|---|---|
| Prefill Q/K/V/FFN projection | GEMV unrolling | **GEMM unit** |
| Decode batched linear | GEMV 또는 작은 GEMM | GEMM unit 가능 |
| Decode attention (`QKᵀ`, `SV`), KV-cache 접근 | PIM GEMV path | 기존 PIM 유지 |

실험에서는 prefill 단계의 성능 향상이 뚜렷했고, decode 단계는 설정에 따라 약 1.05~1.2배 개선이 관찰되었다(시뮬레이션 단계). **평가 시 주의점**: 기존 구조에 유닛을 "추가"한 것이므로 동일 area/power 조건 비교가 아니라는 점, prefill/decode 실험 경로가 완전히 일관되지 않았다는 점이 한계로 지적되었다 — 하드웨어 연구에서 **"공정한 비교(fair comparison)"**가 왜 중요한지 보여주는 사례다.

### PIM/PNM + CXL 기반 다중 디바이스 아키텍처

- **PIM/PNM 도입**: 연산기를 메모리 내부(PIM) 또는 메모리 바로 옆(PNM, Processing-Near-Memory)에 배치해 호스트로의 데이터 이동 병목을 제거하고 대역폭을 극대화한다.
- **Non-MAC 연산의 유연성**: LLM 추론에는 행렬곱(MAC)뿐 아니라 activation·정규화 같은 **Non-MAC 연산**도 많다. 단순 MAC 유닛만으로는 부족해, 제어와 복잡한 연산이 가능한 **범용 RISC-V 코어**를 메모리 근처에 통합해 유연하게 워크로드를 처리하는 설계가 제안된다.
- **CXL 3.0과 다중 디바이스 분산(Disaggregation)**: 모델과 KV Cache 크기가 커지며 대역폭뿐 아니라 **용량(capacity)** 문제도 심각해진다. CXL 3.0으로 메모리를 탄력적으로 확장하고, 여러 CXL/PIM 디바이스를 **마스터-슬레이브** 구조로 스케줄링·제어하는 것이 중요한 연구 과제로 제시된다.
- **기존 PIM 연구의 사각지대**: 기존 PIM 가속 연구 다수가 **Decode 단계에만 집중**해 긴 Prefill 단계를 간과했다는 지적이 있다. 또한 CXL로 연결된 다중 PIM 환경에서 Attention 연산 시 노드 간 **부하 불균형(load imbalance)**이 발생하는데, GEMM 전용 유닛을 구성하고 여러 PIM 디바이스에 걸쳐 Attention을 병렬화해 유휴 리소스를 최소화하는 구조가 제안된다.

### GPU-DLA 이기종 스케줄링

NVIDIA Jetson 같은 임베디드 플랫폼에서, DLA(Deep Learning Accelerator)가 지원하지 않는 특정 연산(예: Transformer 구조)이 등장하면 연산이 GPU로 넘어가는 **콜백(callback) 지연**이 발생한다. 모델 레이어의 호환성 맵을 사전에 생성하고 TensorRT 설정을 조정해, 멀티 DNN 실행 환경에서 콜백을 줄이고 전체 처리량을 높이는 동적 스케줄링이 제안된다.

### PIM과 NPU의 동시 활용 (Co-utilization)

Memory-bound 연산인 **Attention은 PIM에**, Compute-bound 연산인 **Feed-Forward는 NPU에** 할당하는 이종 자원 분업 아키텍처. **Double Row Buffer**를 통해 두 하드웨어가 쉬지 않고 병렬 처리하도록 파이프라인과 스케줄링을 세분화한다 — Ch.9에서 본 "Prefill은 compute-bound, Decode의 attention은 memory-bound"라는 구분이 하드웨어 자원 배분의 기준으로 직접 활용된 사례다.

## Part 3. 컴파일러와 오토튜닝

Ch.5(TVM 등 DL 컴파일러)의 "그래프+텐서 레벨 최적화"와 "학습 기반 비용 모델 탐색"이 새로운 백엔드·하드웨어로 어떻게 확장되는지를 보여준다.

### Triton/Tracker 계열 프레임워크의 CPU/PIM 백엔드 확장

Ch.10에서 다룬 **Triton**은 주로 GPU를 타깃으로 설계되어 있다. 이를 CPU(SIMD/AVX 백엔드)와 PIM 백엔드까지 확장하려는 연구:

```text
High-level operation
   ↓
Common IR (TT-IR / TT-SHARED IR)   ← backend-independent 표현
   ↓
Backend-specific lowering
   ↓
CPU / Triton(GPU) / PIM backend 실행
```

핵심 기여는 (1) CPU/PIM 등 신규 backend 추가, (2) block/tile 단위 튜닝이 가능한 kernel 생산성 컴포넌트 추가, (3) **PIM Common IR** 설계 — GEMM 패턴을 backend-독립적으로 표현해, 새로운 backend가 등장해도 frontend를 다시 만들 필요 없이 **lowering 규칙만 추가**하면 되는 컴파일러 스택 구조다.

### Tile/Block/Split Configuration 튜닝

PIM/가속기 백엔드에서는 tile shape·memory layout·compute unit 길이·data reuse·frequency·split overhead·synchronization overhead가 모두 성능에 영향을 준다. `M32/N32/K16`과 `M24/N28/K4` 같은 서로 다른 타일 설정 사이에서 latency 차이가 크게 관찰되었으며(예시 수치상 1.74~2.08배 차이), **단일 고정 타일 설정이 항상 최적은 아니라는 것**, 즉 **backend-aware autotuning**이 필요하다는 결론으로 이어진다 — Ch.5의 AutoTVM, Ch.8의 매핑 문제(mapping problem)와 본질적으로 같은 탐색 문제다.

### Trinity: E-graph 기반 텐서 프로그램 최적화

**한계 지적**: 기존 컴파일러는 커널 융합(fusion)과 타일링(tiling)을 순차적으로 분리해 최적화하기 때문에 전역 최적해(global optimum)를 놓치는 경우가 많다.

**해법**: **E-graph(Equality Saturation)** 자료구조를 사용해, 형태는 다르지만 논리적으로 동등한 수천 개의 프로그램 변환 경로를 버리지 않고 하나의 그래프 안에 동시에 유지한다. 이후 비용이 가장 낮은 경로(메모리 레이아웃·fusion·loop 분할의 최적 조합)를 추출해 **FlashAttention 수준의 고성능 커널을 자동 생성**한다.

### 신규 NPU를 위한 하드웨어 인지형 오토튜닝 컴파일러

**문제**: XLA·TVM 같은 기존 DL 컴파일러는 주로 CPU/GPU 백엔드에 맞춰져 있어, 완전히 새로운 커스텀 가속기가 등장할 때마다 scratchpad 메모리 관리·DMA·데이터 이동을 사람이 수동으로 커널에 매핑해야 하는 막대한 개발 비용(전체 개발 비용의 50% 이상 추정)이 발생한다.

**해법**: 하드웨어 구조(ISA, 메모리 계층, vector unit 등)를 컴파일러에 명시(description)하면, 시스템이 **하드웨어 비용 모델(hardware cost model)** 기반으로 최적의 데이터 스케줄(tiling, loop fusion 등)을 자동 탐색한다. 평가 결과 기본 GEMM 라이브러리 대비 약 **5.9배**, 수동 최적화 커널 대비 약 **1.3~2.4배** 스피드업을 기록했으며, NVIDIA GPU 환경에서는 PyTorch 네이티브 대비 2.5배, TVM Meta Schedule 대비 3.8배의 성능 향상을 보였다.

## Part 4. LLM 서빙 시스템과 스케줄링

Ch.9의 Prefill/Decode 구분이, 실제 서빙 시스템에서 스케줄링·캐시 관리·아키텍처 설계에 어떻게 반영되는지를 다루는, 이 장에서 가장 비중이 큰 영역이다.

### DistServe: Prefill과 Decode의 물리적 분리 (Disaggregation)

**문제**: Prefill(compute-bound)과 Decode(memory-bound)가 한 GPU에서 실행되면 서로 간섭(interference)해 TTFT(Time-To-First-Token)와 TPOT(Time-Per-Output-Token)가 모두 악화된다.

**해법**: Prefill 전용 노드와 Decode 전용 노드를 **물리적으로 분리**한다. 노드 간 KV Cache 전송 오버헤드가 발생하지만, 자원 할당이 분리되어 전체 처리량이 최대 10배 이상 향상될 수 있다.

> **하지만 이것이 항상 좋은 것은 아니다** — 아래 "Small-scale P/D Disaggregation" 항목에서 이어지는 반증 사례를 참고.

### CPU-Free LLM 서빙

**문제**: 요청 처리, 가상 메모리 변환(TLB miss), 페이지 폴트 등으로 CPU가 critical path에 개입하면 병목과 간섭이 발생한다.

**해법**: **Smart NIC + RDMA**를 활용해 CPU를 거치지 않고 GPU 스케줄러와 GPU 메모리(VRAM)로 직접 통신해 오버헤드를 제거한다.

### Serverless LLM과 라이브 마이그레이션

**문제**: 콜드 스타트 시 거대한 체크포인트를 GPU로 로딩하는 데 시간이 오래 걸린다.

**해법**: (1) 텐서 인덱스 매핑으로 필요한 텐서 청크만 병렬 로딩, (2) 서버 상태가 꽉 찼을 때 진행 중인 추론을 다른 서버로 옮기는 **라이브 마이그레이션**(KV Cache 전체를 복사하는 대신 재연산 활용).

### 추측 해독 (Speculative Decoding)

토큰을 하나씩 생성하는 auto-regressive 방식의 근본적인 느린 속도를 극복하는 기법.

```text
가볍고 빠른 Draft Model이 여러 토큰을 병렬로 미리 예측
        ↓
무겁고 정확한 Target Model이 이를 한 번에 검증(verify)해 Accept/Reject 결정
```

핵심 장점은 **출력의 확률 분포를 원본 Target Model과 동일하게 유지**하면서 추론 속도만 높일 수 있다는 것이다 — 정확도 손실이 없는(lossless) 가속 기법이라는 점에서 앞서 다룬 양자화·프루닝(손실 있는 압축)과 근본적으로 다른 카테고리다.

### Hi-RAG: 파이프라인 RAG 시스템

**문제**: RAG(검색 증강 생성) 환경에서는 검색 시스템이 문서를 찾아올 때까지 LLM 추론이 멈춰(idle) 대기해야 한다.

**해법**: 약간 과거의 문맥(stale query)으로 LLM 생성과 검색을 동시에 겹쳐 실행(pipeline parallelism)해 검색 지연시간을 생성 지연시간 뒤에 숨긴다.

### KV Cache 관리 전략들

Ch.9에서 본 KV Cache(PagedAttention)를 넘어, 다양한 서빙 시나리오별 세부 관리 전략이 연구되고 있다.

| 시나리오 | 전략 |
|---|---|
| **에이전트(Agentic AI) 워크플로우** (KV Flow) | 도구 호출·사고(CoT)가 포함된 멀티 에이전트 환경에서, 작업 흐름(DAG)을 미리 파악해 곧 사용될 prefix 캐시는 eviction에서 제외하고 GPU로 프리페치 |
| **멀티턴(Multi-Turn) 대화** | 대화가 길어질수록 KV Cache가 선형 폭증 → GPU VRAM이 꽉 차면 CPU RAM/SSD로 오프로딩, 대기 큐 요청 크기 분석으로 기아(starvation) 방지 리오더링 |
| **의미론적 유사도(Semantic Similarity) 기반 Prefix Cache** | 토큰이 완전히 일치해야만 캐시를 재사용하는 기존 방식과 달리, 임베딩 검색 모델(Contriever 등)로 유사한 과거 쿼리를 찾아 그 reasoning 결과와 KV Cache를 재사용 — 정확도와 지연시간의 트레이드오프 |
| **큐 인지형(Queue-Aware) Cache Eviction** | SGLang의 RadixAttention이 쓰는 LRU 방식은 온라인 FIFO 큐 특성상 곧 재사용될 캐시도 삭제할 수 있음 → 대기 큐(waiting queue)의 요청 정보를 미리 스캔해 향후 사용될 캐시 블록에 우선순위 가중치 부여 |
| **QFL/BTFT/AFC vs LRO 계열 교체 정책** | 기존 LRU/LRO는 반복 등장하는 공통 블록에 치우쳐 다양한 블록이 밀려남 → waiting queue와 window size(약 700~1000)를 함께 고려하면 workload에 따라 더 나은 hit rate 달성 가능 |

### Cache Simulator 기반 정책 분석

간단한 cache simulator로 애플리케이션 request trace를 수집해 policy별 hit rate를 비교한 결과, **"항상 우월한 universal cache policy는 존재하지 않는다"**는 결론이 반복적으로 관찰된다. Cache policy는 workload와 agent의 작업 흐름(workflow)에 따라 달라져야 하며, agent 자체의 로직이 아니라 **시스템 레벨의 control policy**로 다뤄야 한다는 방향이 제시된다.

### Small-scale P/D Disaggregation 분석 — "분리가 항상 좋은가?"

작은 규모(7B~11B급 모델)의 서빙 환경에서 Prefill-Decode Disaggregation을 분석한 결과, DistServe에서 본 것과 달리 **오히려 overhead가 커질 수 있다**는 것이 확인되었다.

```text
P/D Disaggregation → KV 전송 필요 → 큐/백프레셔(backpressure) 발생
      → 요청 스케줄링 지연 → end-to-end latency 악화 가능
```

- 관찰된 핵심 병목은 **KV transfer queue**에 요청이 쌓이는 현상이다. 큐 backpressure를 줄이기 위해 요청을 서버 측에서 잠시 hold하면 **server-side latency는 줄지만 client-side waiting이 늘어나**, 결국 병목이 사라진 것이 아니라 **대기 위치가 서버에서 클라이언트로 이동**했을 뿐인 경우가 있었다.
- prefill-heavy 시나리오에서는 decode가 prefill 간섭 없이 진행되어 이득이 있었지만, decode-heavy 시나리오에서는 그 효과가 사라졌다.

**결론**: PD 분리가 항상 좋은 것은 아니며, 특히 소규모 서빙에서는 KV 전송·큐잉·백프레셔·클라이언트 대기까지 포함한 **end-to-end 분석**이 반드시 필요하다.

### 예상 출력 길이 기반 스케줄링

vLLM의 기본 FCFS(First-Come-First-Serve) 큐에서는 출력이 짧은 요청이 긴 요청 뒤에 갇혀 지연되는 **Head-of-Line Blocking**이 발생한다. 과거 요청 로그(trace)로 프롬프트 피처를 분석해 예상 출력 길이를 예측하고, 이를 기반으로 우선순위를 부여해 짧은 요청의 꼬리 지연시간(tail latency)을 개선하는 접근이다.

### VLM/vLLM 서빙에서의 Preemption·Recomputation-aware Scheduling

GPU 메모리가 부족할 때 발생하는 문제:

```text
요청 증가 → KV cache/block 메모리 부족 → preemption 발생
   → block swap-out/swap-in 또는 recomputation → overhead 증가 → latency 악화
```

**접근**: OS 스케줄링(FCFS/CFS/MLFQ)을 LLM 서빙 스케줄러에 적용하되, prompt length·output length 예측·prefill/decode length·tool-call 복잡도·향후 필요 KV 메모리량 같은 **LLM 특화 정보**를 결합한다.

**vLLM EngineCore 구조**:

```text
EngineCore → Scheduler(배치 계획 수립) → Block Manager(블록/오프로딩/페이징 결정)
           → GPU Worker(실제 실행) → Model Runner(런타임 메타데이터 관리) → 출력 갱신 후 반복
```

**Priority Queue 기반 스케줄러** (total length / prefill length / decode length / tool-call 복잡도 등으로 3단계 클래스 분류)를 FCFS, CFS, MLFQ와 비교 평가한 결과:

- ShareGPT 등 여러 데이터셋·RTX 5080 환경에서, **모든 데이터셋에서 효과적인 것은 아니었지만** tool-call이 섞여 요청 비용 편차가 큰 workload에서는 priority queue 기반 스케줄러가 약 **9.4%(다른 설정에서는 약 8%)** 성능 향상을 보였다. CFS·MLFQ는 기대만큼 효과적이지 않았다.
- **throughput 증가 ≠ 사용자 체감 latency 개선**이라는 점이 중요하게 지적되었다 — 평균 throughput이 좋아져도 특정 요청이 오래 기다리는 **starvation**이 발생할 수 있으므로, 반드시 **P95 latency와 fairness**를 함께 확인해야 한다.

## Part 5. Diffusion LLM 추론 최적화

Auto-regressive LLM(지금까지 다룬 대부분의 모델)과 달리, **Diffusion LLM**은 여러 위치의 토큰을 반복적으로 정제(refine)하는 방식으로 생성한다. 이 구조적 차이가 최적화 전략에도 근본적인 차이를 만든다.

```text
AR LLM:       t번째 토큰 생성 후 고정 → KV Cache 재사용이 쉬움
Diffusion LLM: 여러 토큰이 step마다 계속 갱신 → 각 step의 confidence/uncertainty가 다름
               → 기존 KV Cache가 항상 유효하지 않음 (cache invalidation/재계산 문제)
```

### Step별 Adaptive Precision/Scheduling

Diffusion 모델의 각 step은 확신(confidence)이 다르므로, **모든 step에 같은 precision을 쓸 필요가 없다**는 아이디어:

```text
확신이 낮은/중요한 step → 높은 precision 또는 더 많은 compute
확신이 높은/영향이 작은 step → 낮은 precision 또는 적은 compute
```

실험에서는 평균 비트폭을 약 3.7~3.75bit 수준(4bit보다 약간 낮은 수준)으로 유지하면서 품질을 보존하는 방향이 관찰되었다.

### KV Cache 재사용을 위한 Dual-PC와 Confidence Threshold Decoding

- **Dual-PC**: 테이블/블록 인덱스를 사용해 어떤 cache block을 재사용할 수 있는지 상태 기반으로 추적한다 — 단순 위치 기반 재사용이 아니라 블록의 상태를 함께 관리한다.
- **Confidence Threshold Decoding**: 모델의 confidence를 기준으로 토큰/블록이 충분히 안정적인지 판단한다. confidence가 높으면 cache 재사용 또는 forward 생략이 가능하고, 낮으면 재계산이 필요하다.

**핵심 트레이드오프**: 더 공격적인 cache reuse/forward skipping은 빠르지만 품질 저하 위험이 있고, 보수적인 confidence threshold는 느리지만 품질이 안정적이다.

### 연구로서 보완이 필요한 지점

Diffusion LLM cache reuse 실험에 대한 피드백은, 아직 **example benchmark 수준**이며 정식 논문화를 위해서는 다음이 추가로 필요하다는 것이었다: 어떤 task에서 reuse가 잘 되는가, 어떤 task에서 품질이 무너지는가, confidence threshold가 실제 정답 여부(correctness)와 잘 맞는가, code generation/reasoning/simple QA 간 차이는 없는가 — 즉 **speedup 수치 하나만으로는 부족하고, task별 error case 분석이 뒷받침되어야 한다.**

## 엣지·모바일 응용 사례

이 강의의 팀 프로젝트에는 서버급 LLM 서빙뿐 아니라, Ch.7(엣지 최적화)의 문제의식을 잇는 모바일/AR-VR 응용도 포함되어 있었다.

- **모바일 AR/VR 렌더링·객체 인식 최적화**: 고해상도(FP16/BF16 수준) 데이터를 실시간 처리할 때 지연시간이 실시간 데드라인을 놓치는 문제에 대해, **고정된 배경(static background)과 움직이는 객체(moving object)를 분리**해 움직임이 큰 영역만 GPU/NPU로 오프로딩하고 배경은 연산을 생략·저전력 처리하는 접근을 취했다. 정확도 저하 없이 데드라인 충족률을 향상시켰다.
- **온디바이스(On-Device) LLM의 시스템 변동성 대응**: 모바일 기기 내부의 메모리 압박, CPU 리소스 경쟁, 백그라운드 앱 구동 등 시스템 변동성이 LLM 추론에 미치는 영향을 측정하고, 특정 연산의 가속기 오프로딩·콜드스타트 지연 완화로 강건한(robust) 추론 성능을 확보했다.
- **소규모 환경의 Prefill-Decode Disaggregation**: 대규모 AI 클러스터에서 쓰이는 PD 분리 기법을, AI PC나 GPU 2대 수준의 소규모 로컬 에이전트 환경에 적용해 실효성을 실험 — 네트워크 대역폭 한계 속에서 KV Cache 압축·전송 엔진 최적화가 핵심이었다 (위 "Small-scale P/D Disaggregation" 항목과 같은 문제의식의 다른 사례).

## 연구 발표들을 관통하는 공통 메시지

이 학기 전체 프로젝트 발표를 관통하는 문제의식은 다음 세 가지로 요약된다.

**1) 단순히 연산을 줄이는 것만으로는 부족하다** — 저정밀 포맷, GEMM 유닛, cache reuse, 스케줄러 모두 "속도를 높이자"는 같은 목표를 갖지만, 실제로는 다음 제약들이 항상 함께 따라온다.

```text
속도 향상 ↔ 정확도 손실 ↔ area/power 증가 ↔ metadata overhead
         ↔ memory transfer overhead ↔ queueing delay ↔ tail latency
```

**2) Workload-aware 설계가 핵심이다** — "항상 좋은 universal policy는 없다"는 결론이 여러 발표에서 반복된다. 타일 크기도 backend마다, 스케줄러도 데이터셋/workload마다, cache policy도 요청 패턴에 따라 달라져야 한다.

**3) 평균 성능만 보면 안 된다** — 특히 서빙 관련 연구에서는 평균 throughput보다 **P95 latency, client-side waiting, GPU 활용률, KV transfer queue 길이, fairness, starvation**이 실제 사용자 경험을 좌우한다.

이를 연구(논문/프로젝트)로 발전시키기 위해 공통으로 요구되는 보완 사항은: 정확한 baseline 정의, 동일 area/power 조건 비교, 실제 workload 기반 test vector, ablation study, tail latency 분석, 품질-속도 트레이드오프 분석, 확장성(extensibility) 주장, 벤치마크 커버리지 확대, error case 분석이다.

## 이 강의를 마치며

Ch.1에서 "AI 혁명은 알고리즘보다 데이터와 연산력에서 왔다"는 역사적 관찰로 시작해, Ch.2~3에서 DNN 연산과 양자화의 수학적 기초를 다지고, Ch.4~5에서 프레임워크·컴파일러가 이를 어떻게 자동으로 최적화하는지, Ch.6~7에서 메모리·프루닝·엣지 배포 전략을, Ch.8~10에서 NPU 하드웨어와 LLM 추론·Triton 커널까지 살펴봤다. 이 마지막 장은 그 모든 조각(양자화·컴파일러·NPU·메모리·스케줄링)이 **LLM 서빙이라는 하나의 실전 문제** 안에서 어떻게 동시에 맞물려 작동하는지를 보여준다 — 그리고 이것이 바로 이 강의의 팀 프로젝트가 학생들에게 요구했던 것, "내가 무언가를 바꿨더니 latency/throughput이 실제로 변했다"를 증명하는 작업의 실제 모습이다.

## 시험·복습 체크포인트

- AWQ가 "가중치가 아니라 활성화 크기"를 기준으로 채널을 선별하는 이유는?
- CENT 구조에 GEMM 유닛을 추가하는 제안에서, Prefill과 Decode 연산이 각각 어느 경로(GEMM unit vs 기존 PIM)로 가는가?
- DistServe의 PD Disaggregation이 항상 좋지 않을 수 있는 이유(Small-scale 분석)를 설명할 수 있는가?
- Speculative Decoding이 양자화·프루닝과 근본적으로 다른 이유는? (lossless vs lossy)
- E-graph(Trinity)가 기존 순차적 fusion+tiling 최적화의 어떤 한계를 해결하는가?
- Diffusion LLM에서 KV Cache 재사용이 AR LLM보다 어려운 구조적 이유는?
- "평균 throughput만 보면 안 되는" 이유와, P95 latency·fairness를 함께 봐야 하는 이유는?
{% endraw %}

---

이전: [10. Triton으로 GPU 커널 작성하기](10-triton-gpu-kernel-programming.md)
