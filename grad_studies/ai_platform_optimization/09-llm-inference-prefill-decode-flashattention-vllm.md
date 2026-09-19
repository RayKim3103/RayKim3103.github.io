---
layout: page
title: "09. LLM 추론 기초: Prefill/Decode, FlashAttention, vLLM"
permalink: /grad_studies/ai_platform_optimization/09-llm-inference-prefill-decode-flashattention-vllm/
sitemap: false
---

- **강의**: 7. Neural Accelerators & LLM Basics (Part 2: LLM 추론 기초)

{% raw %}
## 개요

Ch.8이 하드웨어(NPU) 자체의 구조를 다뤘다면, 이 장은 그 하드웨어 위에서 **LLM(대형 언어 모델) 추론이 구조적으로 왜 특이한지**를 다룬다. Decode-only LLM의 두 단계(Prefill/Decode)가 서로 완전히 다른 연산 특성을 가진다는 사실이, 이후 이 강의(Ch.11)에서 다루는 대부분의 LLM 서빙 최적화 연구의 출발점이 된다.

## Decode-only LLM = Prefill + Decode

Decode-only LLM(GPT/Llama 계열)은 **"다음 단어를 반복적으로 예측"**하도록 학습된 모델이다. 학습된 하나의 가중치 집합을 그대로 사용하지만, 추론 시에는 구조적으로 **다른 두 단계**로 나뉜다.

```text
Model: causal attention으로 다음 단어를 예측하도록 학습됨
       ↓ 같은 weight를 두 개의 뚜렷이 다른 stage로 사용

Prefill: 사용자 프롬프트 전체를 한 번에 처리 (병렬)
Decode : 새 토큰을 한 번에 하나씩 반복 생성 (순차)
```

> Prefill과 Decode는 가중치를 공유하지만, **구조적으로는 서로 다른 두 개의 모델처럼 동작한다**고 볼 수 있다 — 이 관점이 Ch.11의 Prefill/Decode Disaggregation(PD 분리) 연구의 출발점이다.

### Prefill vs Decode 상세 비교

| 구분 | Prefill | Decode |
|---|---|---|
| 하는 일 | 프롬프트의 모든 단어에 대한 KV 값 생성 + 첫 번째 단어 생성 | 새로운 단어를 반복적으로 생성 |
| 연산 패턴 | **병렬(Parallel)** — 프롬프트 전체 토큰을 한 번에 처리 | **순차(Sequential)** — 토큰을 하나씩 생성, 이전 토큰에 의존 |
| 연산 특성 | 행렬-행렬 곱(GEMM) 중심, **Compute-bound**인 경우가 많음 | 행렬-벡터 곱(GEMV) 중심, **Memory-bound**(특히 KV cache 접근)인 경우가 많음 |

예를 들어 "I like my cat a lot ."이라는 문장을 생성할 때, Prefill은 이미 주어진 프롬프트 토큰들(`I like my cat`)에 대한 KV 값을 한 번에 병렬로 계산하고, 이후 Decode는 `a`, `lot`, `.` 처럼 한 토큰씩 순차적으로 생성한다.

> 이 Compute-bound(Prefill) vs Memory-bound(Decode)라는 성격 차이는, "왜 하나의 서버/하나의 스케줄링 정책으로 Prefill과 Decode를 동시에 잘 처리하기 어려운가"라는 질문의 근본 원인이며 Ch.11에서 다시 자세히 다룬다.

## FlashAttention: Attention을 하드웨어 친화적으로

표준 Attention 구현은 `Q·Kᵀ`로 만들어지는 (시퀀스 길이)² 크기의 attention score 행렬을 **HBM(고대역폭 메모리)에 전부 썼다가 다시 읽는** 방식이라, 시퀀스가 길어질수록 메모리 대역폭이 심각한 병목이 된다.

| 버전 | 핵심 기법 | GPU 활용률(대략) |
|---|---|---|
| Standard Attention | - | Low |
| **FlashAttention v1** | **Tiling + Fusion** — attention 계산을 SRAM에 맞는 작은 타일 단위로 쪼개 HBM 접근을 최소화 | ~25% |
| **FlashAttention v2** | non-matmul 연산 제거, 더 나은 warp partitioning | ~50–70% |
| **FlashAttention v3** | NVIDIA Hopper 아키텍처에 특화된 최적화 | ~75% |

FlashAttention의 원리는 결국 Ch.2에서 다룬 **연산 fusion + 타일링(tiling)**을, Attention이라는 구체적인 연산에 정밀하게 적용한 사례다 — HBM 읽기/쓰기를 최소화하고 SRAM 재사용을 극대화한다. 실무에서는 `attn_implementation="flash_attention_2"`처럼 한 줄로 적용할 수 있는 경우가 많다.

## vLLM과 PagedAttention

LLM 서빙에서는 각 요청(request)마다 KV Cache를 위한 메모리를 미리 할당해야 하는데, 요청마다 생성될 토큰 수를 미리 알 수 없어 **메모리 단편화(fragmentation)**가 심각한 문제가 된다.

**PagedAttention**은 운영체제의 **가상 메모리 페이징(paging)** 아이디어를 KV Cache에 적용한다 — KV Cache를 고정 크기의 블록(page) 단위로 관리해, 연속된 메모리를 미리 예약하지 않고도 필요한 만큼만 동적으로 할당한다. 이를 통해 메모리 단편화를 크게 줄이고, 여러 요청이 GPU 메모리를 훨씬 효율적으로 공유하게 한다.

vLLM은 이 PagedAttention을 핵심 기법으로 삼아 한때 오픈소스 LLM 추론 엔진 중 사실상 표준 위치를 차지했다.

## 실무: 추론 엔진 생태계와 선택 기준 (강의 노트 기반)

- **주요 추론 엔진**: **vLLM**(PagedAttention 기반, 한때 압도적) ↔ **SGLang**(RadixAttention 기반, 최근 vLLM을 따라잡거나 역전하는 사례가 늘고 있음). 그 외 상황별로 TensorRT-LLM, LMDeploy, MLC-LLM, llama.cpp 등이 강점을 가진다.
- 이 엔진들은 내부적으로 PyTorch 기반이지만, 실제 성능은 **별도의 최적화된 실행 엔진**(Ch.4의 TorchInductor, Ch.5의 컴파일러 기법들, Ch.10의 Triton 커널)이 좌우한다.
- **시스템 최적화자의 역할**: 모델 구조를 미세하게 변형해 하드웨어에 더 잘 맞추는 것(예: GPU의 thread/warp 정렬에 맞춰 연산 크기를 조정) — 이는 정확도에 영향을 주지 않으면서 시스템 효율만 높이는 전형적인 최적화 유형이다.
- Ch.0에서 제시한 "실무 추론 최적화 우선순위"를 다시 보면: `torch.compile` → vLLM/SGLang 도입 → 양자화(Ch.3) → KV cache quantization/PagedAttention(본 장) → 필요시 커널 수동 작성(Ch.10)이라는 순서가, 왜 이 순서인지 이제 이 장의 내용으로 설명할 수 있다 — **먼저 프레임워크·엔진 레벨에서 가장 값싸게 얻을 수 있는 이득을 취하고, 그다음 모델을 압축하고, 마지막으로 하드웨어에 가장 밀착된 최적화(커스텀 커널)를 시도한다**는 원칙이다.

## 시험·복습 체크포인트

- Prefill과 Decode가 "같은 가중치를 쓰지만 다른 모델처럼 동작한다"는 말의 의미는?
- Prefill이 Compute-bound, Decode가 Memory-bound인 경향을 보이는 이유는?
- FlashAttention이 HBM 접근을 줄이기 위해 사용하는 핵심 기법(tiling)은 무엇인가?
- PagedAttention이 KV Cache의 메모리 단편화 문제를 어떻게 해결하는가?
- vLLM과 SGLang의 핵심 기반 기법(PagedAttention vs RadixAttention) 차이를 설명할 수 있는가? (심화는 Ch.11)
{% endraw %}

---

이전: [08. NPU 아키텍처와 데이터플로우](08-npu-architecture-and-dataflow.md) · 다음: [10. Triton으로 GPU 커널 작성하기](10-triton-gpu-kernel-programming.md)
