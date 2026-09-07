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

## 강의 노트

1. [00. Course Overview - 병렬 프로그래밍 개요](00-course-overview.md)
2. [01. Basic Parallel Architectures - 기본 병렬 아키텍처](01-basic-parallel-architectures.md)
3. [02. Thread Programming - C++ Thread와 동기화](02-thread-programming-c.md)
4. [03. Matrix Multiplication - CPU Cache와 병렬 행렬곱](03-matrix-multiplication-cpu-cache.md)
5. [04. Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md)
6. [05. CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
7. [06. CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)
8. [07. CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)
9. [08. CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)
10. [09. CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md)
11. [10. Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)
12. [10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)
13. [11. Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)
14. [12. CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md)
15. [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)
16. [13. CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)
17. [14. Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md)
18. [15. More Notes - DL Compiler와 LLM Inference](15-more-notes-dl-compiler-llm-inference.md)
19. [HW2. Assignment - Matrix Verification Challenge](hw2-assignment-matrix-verification-challenge.md)
20. [HW2. Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md)
21. [HW3. Assignment - CUDA LoRA](hw3-assignment-cuda-lora.md)
22. [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)
23. [HW4. Assignment - CUDA Conv2d](hw4-assignment-cuda-conv2d.md)
24. [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)
25. [HW5. Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md)
26. [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)
27. [HW6. Assignment - Triton ResNet](hw6-assignment-triton-resnet.md)
28. [HW6. Report - Triton ResNet18 구현과 Conv2d 분석](hw6-report-triton-resnet18-conv2d.md)

## 과목 학습 지도

- CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 목차 순서를 따라가되, 각 문서에서 정의-핵심 식/구조-예제-실수 포인트-연결 단원을 따로 표시하며 복습한다.
- 시험이나 프로젝트 전에는 각 노트의 보강 학습 노트에 있는 점검 질문을 먼저 풀어 보고 막히는 단원을 역추적한다.
- 각 하위 노트는 빠른 요약으로 시작해 세부 설명으로 내려가도록 읽고, 마지막에는 직접 설명할 수 있는 질문을 하나 이상 남긴다.

## 복습 루틴

- 1회독: 제목과 목차를 훑으며 이 과목이 다루는 대상과 추상화 층을 잡는다.
- 2회독: 각 노트의 핵심 식, 회로, 알고리즘, 시스템 흐름을 손으로 다시 써 본다.
- 3회독: 예제나 과제 문서를 기준으로 입력 조건을 바꾸었을 때 결과가 어떻게 달라지는지 스스로 질문한다.
- 시험 직전: 자주 하는 실수와 점검 질문만 모아 빠르게 훑고, 설명이 막히는 노트로 되돌아간다.

