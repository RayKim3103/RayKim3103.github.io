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

## 숫자로 확인하기 — ResNet18에서 Conv2d가 차지하는 연산 비중

ResNet18의 첫 7x7 conv(입력 채널 3, 출력 채널 64, $$112\times112$$ output 기준)와, 이후 반복되는 3x3 conv block 하나(채널 64, $$56\times56$$ output)의 곱셈 수를 비교하면 왜 Conv2d 최적화가 우선순위 1순위인지 알 수 있다.

**7x7 conv 1개**: $$112\times112 \times 64\times3 \times 7\times7 = 112^2\times64\times3\times49 \approx 1.18\times10^8$$

**3x3 conv 1개**(채널 64→64): $$56\times56\times64\times64\times3\times3 \approx 1.16\times10^8$$

반면 ResNet18의 최종 `Linear`(512→1000)는 batch 1 기준 $$512\times1000 \approx 5.12\times10^5$$번의 곱셈뿐이다. 즉 conv layer 하나(3x3, 약 $$1.16\times10^8$$)가 전체 network의 마지막 Linear layer보다 **약 226배** 많은 연산을 차지하고, ResNet18 전체에는 이런 conv layer가 20개 가까이 있다 — "Conv2d가 가장 큰 비중을 차지하므로 최적화 우선순위가 높다"는 문장이 이 비교에서 정량적으로 확인된다. 보고서가 direct convolution 대신 im2col+GEMM을 택한 이유도, 이 압도적인 conv 연산량을 이미 고도로 최적화된 GEMM 패턴으로 처리하려는 시도다.

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

## 복습 질문

- $$112\times112$$ output, 채널 3→64, $$7\times7$$ filter인 첫 conv의 곱셈 수(약 1.18억)를 직접 계산할 수 있는가?
- 3x3 conv 하나가 최종 Linear layer보다 왜 약 226배 많은 연산을 차지하는지 설명할 수 있는가?
- ResNet18에 conv layer가 20개 가까이 있다는 사실이 "Conv2d 최적화 우선순위가 가장 높다"는 결론과 어떻게 연결되는지 설명할 수 있는가?

## 정리

HW6는 강의의 CUDA 최적화 지식을 Triton과 DNN 전체 모델 구현으로 확장하는 과제다. 핵심은 ResNet18의 각 layer를 Triton kernel로 구현하면서, high-level DSL의 생산성과 low-level CUDA/library 대비 성능 한계를 함께 분석하는 것이다.

{% endraw %}

---

이전: [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md) · 다음: [HW6. Report - Triton ResNet18 구현과 Conv2d 분석](hw6-report-triton-resnet18-conv2d.md)
