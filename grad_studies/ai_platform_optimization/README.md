---
layout: page
title: AI Platform Optimization
description: >
  연세대학교 대학원 "인공지능 플랫폼 최적화(HW/SW Platform Optimization for Machine Learning)" 강의 노트.
hide_description: false
sitemap: false
permalink: /grad_studies/ai_platform_optimization/
---

- **강의명**: HW/SW Platform Optimization for Machine Learning (인공지능 플랫폼 최적화)
- **강의자**: Yongjun Park (박영준), Yonsei University, Department of Computer Science
- **학기**: 2026년 봄학기
- **강의 목표**: 머신러닝 애플리케이션을 CPU·GPU·NPU 등 다양한 타깃 디바이스에서 어떻게 빠르고, 효율적이고, 메모리를 적게 쓰면서 돌릴 것인가 — 즉 "딥러닝 이론"이 아니라 "딥러닝 시스템"의 관점에서 학습/추론 파이프라인 전체를 최적화하는 법을 다룬다.

## 강의 진행 방식

이론 슬라이드 강의 외에도, 매 회차 강의를 녹음한 뒤 구술 내용을 텍스트로 정리한 "강의 노트"가 함께 제공되었다. 슬라이드가 교과서적인 개념(연산·아키텍처·컴파일러 이론)을 다룬다면, 구술 노트는 담당 교수가 실무에서 겪은 경험과 2025~2026년 시점의 최신 산업 동향(FlashAttention, vLLM/SGLang, PyTorch 2.x, NPU 생태계 등)을 담고 있다. 아래 노트들은 이 두 자료를 함께 종합해서 정리했다.

## 강의 노트

### Part 1. 딥러닝 시스템 기초

1. [00. 강의 개요](00-course-overview.md)
2. [01. AI와 딥러닝 시스템의 지형도](01-ai-and-deep-learning-landscape.md)
3. [02. CNN 아키텍처와 DNN 핵심 연산](02-cnn-architectures-and-dnn-core-operations.md)
4. [03. 역전파·자동미분과 양자화 기초](03-backpropagation-autodiff-and-quantization-basics.md)

### Part 2. 프레임워크와 컴파일러

5. [04. PyTorch 내부 구조와 PyTorch 2.x 컴파일 스택](04-pytorch-internals-and-pytorch2-compilation.md)
6. [05. 딥러닝 컴파일러 (TVM·XLA·TensorRT·Halide·Glow)](05-deep-learning-compilers.md)

### Part 3. 시스템 최적화

7. [06. 메모리 최적화와 네트워크 프루닝](06-memory-optimization-and-network-pruning.md)
8. [07. 엣지·CPU 추론 최적화](07-edge-and-cpu-inference-optimization.md)

### Part 4. 하드웨어 가속기와 LLM 서빙

9. [08. NPU 아키텍처와 데이터플로우](08-npu-architecture-and-dataflow.md)
10. [09. LLM 추론 기초: Prefill/Decode, FlashAttention, vLLM](09-llm-inference-prefill-decode-flashattention-vllm.md)
11. [10. Triton으로 GPU 커널 작성하기](10-triton-gpu-kernel-programming.md)
12. [11. LLM 서빙·추론 가속 최신 연구 동향](11-llm-serving-research-trends.md)

## 참고

- 강의 진행: 이론 강의 + 프로그래밍 HW(TVM, OpenCL) + 논문 발표(10분) + 팀 프로젝트(시스템 최적화 주제)
- 주요 참고 자료: [Dive into Deep Learning](https://www.d2l.ai/), [Apache TVM](https://tvm.apache.org/), Pedro Domingos, *A Few Useful Things to Know About Machine Learning*, CACM 2012
