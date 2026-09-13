---
layout: page
title: Parallel Programming
description: >
  Parallel Programming 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/systems/parallel-programming/
---

원본: [GitHub — Parallel Programming](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Parallel_Programming)

> 강의 노트와 HW 과제/보고서 노트에 이론을 실제 값까지 끝까지 추적하는 worked example(power wall 전력 계산, SIMD 처리량 비교, Amdahl's Law speedup 계산, cache miss rate 계산, CUDA grid/block index 추적, shared memory tiling traffic 절감량, bank conflict modular 계산, im2col 메모리 증폭, reduction 알고리즘 cascading, scan work-efficiency 비교, pinned memory·stream pipeline 시간 계산, PCIe·NVLink 대역폭 비교, LLM prefill/decode FLOP 비교, Freivalds error bound, LoRA FLOP 절감, occupancy·register 계산 등)을 추가해 보강했습니다.

## 강의 노트

1. [00. Course Overview - 병렬 프로그래밍 개요](00-course-overview.md) — power wall 동적 전력 계산
2. [01. Basic Parallel Architectures - 기본 병렬 아키텍처](01-basic-parallel-architectures.md) — SIMD 폭별 GFLOP/s 계산
3. [02. Thread Programming - C++ Thread와 동기화](02-thread-programming-c.md) — Amdahl's Law speedup 표
4. [03. Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md) — cache line miss rate 계산
5. [04. Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md) — grid/block index와 boundary check 추적
6. [05. CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md) — tiling의 16배 traffic 절감 계산
7. [06. CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md) — 32-way bank conflict와 padding 계산
8. [07. CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md) — im2col 메모리 증폭(19배) 계산
9. [08. CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md) — algorithm cascading thread 수 절감
10. [09. CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md) — A100 SM당 L2 cache 몫 계산
11. [10. Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md) — Kogge-Stone work 카운트(N=8)
12. [10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md) — Blelloch scan work 계산
13. [11. Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md) — softmax fusion traffic 절감(2.5배)
14. [12. CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md) — pinned+stream 전체 시간 계산(3.5배)
15. [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md) — pipeline 공식으로 시간 계산
16. [13. CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md) — memcheck silent error 추적
17. [14. Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md) — PCIe vs NVLink 전송 시간 비교(24배)
18. [15. More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md) — prefill/decode FLOP 비교
19. [HW2. Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md) — Freivalds error bound 계산
20. [HW2. Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md) — 16x16 block L1 cache 적합성 계산
21. [HW3. Assignment - CUDA LoRA](hw3-assignment-cuda-lora.md) — LoRA FLOP 절감(256배) 계산
22. [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md) — register 기반 occupancy 계산
23. [HW4. Assignment - CUDA Conv2d](hw4-assignment-cuda-conv2d.md) — filter 크기별 output·halo 계산
24. [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md) — halo load thread 비율 계산
25. [HW5. Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md) — `2^24` 원소 block 구성 계산
26. [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md) — warp unrolling instruction 절감
27. [HW6. Assignment - Triton ResNet](hw6-assignment-triton-resnet.md) — ResNet18 conv 연산 비중 계산
28. [HW6. Report - Triton ResNet18 구현과 Conv2d 분석](hw6-report-triton-resnet18-conv2d.md) — im2col 데이터 증폭(9배) 계산
