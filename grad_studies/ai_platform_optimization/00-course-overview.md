---
layout: page
title: "00. 강의 개요"
permalink: /grad_studies/ai_platform_optimization/00-course-overview/
sitemap: false
---

- **강의**: HW/SW Platform Optimization for Machine Learning (2026 Spring, Yongjun Park, Yonsei University)

{% raw %}
## 개요

이 강의는 "딥러닝을 잘 아는 것"이 아니라 **딥러닝을 구동하는 시스템을 잘 만드는 것**을 목표로 한다. 같은 모델이라도 어떤 하드웨어(CPU/GPU/NPU)에, 어떤 프레임워크·컴파일러를 거쳐, 어떤 메모리 전략으로 올리느냐에 따라 학습 속도·추론 지연시간(latency)·처리량(throughput)·메모리 사용량이 수 배에서 수십 배까지 달라진다. 강의는 이 "시스템 레이어"를 3주간의 딥러닝 이론 리뷰를 시작으로, 프레임워크 내부 구조 → 컴파일러 → 메모리/프루닝 최적화 → NPU 하드웨어 → LLM 추론 가속이라는 순서로 파고든다.

## 강의 구성

```text
Lecture (이론) + Programming HW (TVM, OpenCL) + Paper Presentation (10분) + Team Project (시스템 최적화)
```

| 항목 | 비중 | 내용 |
|---|---|---|
| Programming HW | 30% | TVM 컴파일러 실습, OpenCL GEMM 최적화 실습 |
| 논문 발표 | 10% | 시스템 최적화 관련 논문 1편, 10분 발표(5분 요약 + 3분 본인 의견 + 2분 Q&A) |
| 팀 프로젝트 | 50% | 3인 1팀, ML 프레임워크 기반 최적화 연구(그래프/GPU/CPU/NPU 최적화, 프루닝·스케줄링·병렬화·양자화 등 자유 주제) |
| 출석 | 10% | 시험 없음 |

시험은 없다. 대신 팀 프로젝트가 성적의 절반을 차지하며, "내가 무언가를 바꿨더니 latency/throughput이 실제로 변했다"를 데이터로 증명하는 것이 핵심 요구사항이다.

## 선수 지식

- **필수**: Python 숙련도, C/C++ 프로그래밍, Linux/GCC 환경 경험
- **도움이 되는 것**: FCNN/CNN/DNN/RNN 등 머신러닝 기초, 컴파일러·OS·컴퓨터 구조(CPU/GPU/NPU) 기초

## 학기 진행 (Tentative)

| Week | 주제 |
|---|---|
| 1 | Deep Learning System Overview 1: Introduction |
| 2 | Deep Learning System Overview 2: DNN Core Operations |
| 3 | Deep Learning System Overview 3: Backpropagation and Quantization |
| 4 | Deep Learning Framework (PyTorch) |
| 5 | Deep Learning Compiler, Lab 1 |
| 6 | Memory Optimization and Network Pruning |
| 7 | Introduction to NPUs, Lab 2 |
| 8 | 중간고사 기간 (강의 없음) |
| 9 | Kernel Libraries (cuBLAS/CUTLASS/FlashAttention), Neural Accelerators & LLM Basics |
| 10 | Advanced Topic (Triton) & Project Overview |
| 11 | Project Proposal |
| 12–14 | Paper Presentation / Project Checkpoint |
| 15–16 | Paper Presentation / Team Project Presentation |

> 실제 강의는 위 스케줄보다 한 주 정도 밀려서 진행되었다. 이 노트의 각 chapter는 "실제로 다뤄진 내용" 기준(강의 자료 파일의 lecture 번호)으로 정리했다.

## 이 강의가 말하고자 하는 것 — 시대적 맥락

담당 교수가 첫 두 주에 반복해서 강조한 메시지는 다음과 같다.

> **"지금은 하드웨어를 잘 쓰는 법보다, 빠르게 변하는 소프트웨어 생태계를 따라가는 법이 더 중요해지는 시대다."**

- 현재 공개되는 거의 모든 오픈소스 LLM(Qwen, Mistral, Gemma 등)은 내부적으로 **Llama 스타일 Transformer 구조**를 크게 벗어나지 않는다. 즉 **Attention + Feed-Forward(FFN)** 두 블록만 잘 최적화해도 최신 모델 대부분을 어느 정도 빠르게 돌릴 수 있다.
- 하드웨어는 여전히 **NVIDIA GPU가 사실상 독점**하고 있다. 국내 NPU 스타트업(Rebellions, Furiosa, DEEPX 등)은 특정 모델·배치 조건에서는 빠르지만, 범용성과 연산자(operator) 커버리지가 부족해 결국 GPU로 폴백(fallback)하는 경우가 많다. "NPU가 몇 배 빠르다"는 광고를 그대로 믿지 말고 직접 벤치마크해야 한다.
- GPU 자체보다 **GPU 간 인터커넥트(NVLink, InfiniBand, RoCE), CPU 성능(전처리·스케줄링), 대용량 CPU 메모리(KV cache offloading)**가 실무에서는 더 큰 병목이 되는 경우가 많다.
- 시스템 최적화의 본질은 결국 **"같은 일을 더 적은 자원으로, 혹은 같은 자원으로 더 좋은 결과를 내는 것"**이다.

## 실무 추론 최적화 체크리스트 (강의에서 제시된 우선순위)

실제로 LLM 추론 속도를 올릴 때 고려하는 순서는 대략 다음과 같다. 이 순서 자체가 이후 chapter들의 전체 흐름과도 대응된다.

1. `torch.compile()` + TorchInductor 적용 → Ch.4
2. vLLM/SGLang 같은 추론 엔진 도입 → Ch.9
3. 4bit/8bit 양자화 (AWQ, GPTQ, bitsandbytes) → Ch.3, Ch.11
4. KV cache quantization / PagedAttention → Ch.9, Ch.11
5. 필요 시 CUTLASS/Triton으로 커널 수동 작성 → Ch.10

## 참고

- No official textbook. [Dive into Deep Learning](https://www.d2l.ai/), [Apache TVM](https://tvm.apache.org/), Andrew Ng — Machine Learning (Coursera)
- Pedro Domingos, *A Few Useful Things to Know About Machine Learning*, CACM 2012 (1주차 권장 독서)
{% endraw %}

---

다음: [01. AI와 딥러닝 시스템의 지형도](01-ai-and-deep-learning-landscape.md)
