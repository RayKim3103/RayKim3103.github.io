---
layout: page
title: "HW4. Report - Direct Convolution과 im2col GEMM"
permalink: /studies/systems/parallel-programming/hw4-report-direct-convolution-im2col-gemm/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW4%20Report%20-%20Direct%20Convolution%EA%B3%BC%20im2col%20GEMM.md)

{% raw %}
이전: [Assignment - CUDA Conv2d](hw4-assignment-cuda-conv2d.md)  
다음: [Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md)

## 핵심 요약

이 보고서는 CUDA로 direct convolution과 im2col+GEMM 방식을 구현하고 최적화한 내용을 정리한다. Shared memory, tile overlap, coalescing, bank conflict, occupancy, `__syncthreads()` overhead가 주요 분석 대상이다.

## 2D Convolution

입력 matrix `I`와 filter `K`를 사용하여 output `O`를 만든다. Output의 각 element는 filter window 내부의 multiply-accumulate로 계산된다.

일반 CNN convolution에서는 batch, output channel, input channel, height, width, kernel height, kernel width가 함께 관여한다.

## im2col

im2col은 input image의 sliding window patch를 column 형태로 재배치하여 convolution을 GEMM으로 바꾸는 기법이다.

장점:

- GEMM kernel 최적화를 활용 가능
- GPU가 잘 처리하는 matrix multiplication으로 문제 변환

단점:

- 겹치는 patch가 중복 저장되어 memory overhead 증가
- im2col 변환 kernel 자체의 비용 존재

## Direct Convolution 최적화 포인트

### Shared Memory

인접 output pixel은 input window를 많이 공유한다. 따라서 input tile과 filter를 shared memory에 올리면 global memory 접근을 줄일 수 있다.

### Tile Overlap

Filter size가 `K x K`이면 output tile을 계산하기 위해 input tile 주변의 halo 영역도 필요하다. Load tile은 compute tile보다 커질 수 있다.

### Bank Conflict

Input tile과 filter를 shared memory에 저장하면 warp threads가 같은 bank를 동시에 접근할 수 있다. Padding을 고려하면 conflict를 줄일 수 있다.

### Synchronization

Shared memory를 쓰면 tile load 완료 후 `__syncthreads()`가 필요하다. 이 barrier는 correctness에는 필수지만, 모든 thread가 가장 늦은 thread를 기다리므로 성능 overhead가 된다.

## 숫자로 확인하기 — halo load에 참여하지만 compute에는 빠지는 thread 비율

Output tile `16x16=256`개 thread block에서 filter `K=5`라면, load해야 할 input tile 크기는 $$(16+5-1)^2=20^2=400$$개 값이다. Block의 thread 수는 256개인데 필요한 load는 400개이므로, 단순히 "thread 하나가 load 하나씩" 담당하는 방식으로는 thread 수가 모자란다 — 반대로 이야기하면 **256개 thread 각각이 평균 $$400/256=1.5625$$개의 load를 나눠 맡아야** 한다는 뜻이고, 실제 구현에서는 일부 thread가 2개, 나머지가 1개를 load하는 방식이 된다.

반대로 compute 단계에서는 output tile 256개 pixel을 정확히 256개 thread가 1대1로 담당하므로 idle thread가 없다. 즉 이 예제 자체는 "load 참여 thread 수 부족"이 문제이지, 보고서가 지적한 "load에는 참여하지만 compute에는 사용되지 않는 thread"는 반대로 **load tile이 output tile보다 훨씬 큰 경우**(예: 여러 채널을 한 번에 tiling)에 나타난다 — 그 경우 halo 영역 담당 thread는 load만 하고 자신의 output 위치가 없어 compute 단계에서 유휴 상태가 된다.

## GEMM Kernel

im2col 이후 matmul은 강의안의 shared memory tiled matrix multiplication 구조를 사용했다.

특징:

- shared memory 사용
- load/store coalescing 고려
- temporal locality 향상
- tile size와 occupancy 균형 필요

## Implementation 관점

보고서는 RTX 3090의 hardware specification을 기준으로 thread block size 128, 256, 512 등을 검토했다. Occupancy 100%가 가능하더라도 실제 성능은 memory access와 synchronization overhead의 영향을 함께 받는다.

## 성능 저하 요인

- Shared memory tile load에 참여하지만 실제 compute에 사용되지 않는 thread 발생
- Boundary와 filter size 때문에 idle thread 증가
- `__syncthreads()` overhead
- Filter load에서 같은 값 접근이 많아 bank conflict 가능성
- im2col 변환으로 추가 memory traffic 발생

## Evaluation

보고서는 shared memory 적용 여부, tile size, direct convolution과 im2col+GEMM의 실행 시간을 비교했다. 성능 평가는 단순 correctness가 아니라 어떤 optimization이 실제 benchmark에서 이득을 주는지 확인하는 방식으로 진행되었다.

## 복습 질문

- `TILE=16`, `K=5`일 때 load tile 크기(400)와 block thread 수(256)의 비율(1.5625)이 왜 "thread당 여러 값 load"를 요구하는지 설명할 수 있는가?
- Load 참여 thread 수가 부족한 경우와, load에는 참여하지만 compute에는 참여하지 못하는 thread가 생기는 경우가 왜 반대 상황인지 구분할 수 있는가?
- `__syncthreads()`가 correctness에는 필수지만 성능에는 왜 항상 비용인지 설명할 수 있는가?

## 정리

HW4 보고서의 핵심은 convolution이 matrix multiplication보다 data reuse와 boundary 처리가 더 복잡하다는 점이다. Direct convolution은 중복 read를 shared memory로 줄여야 하고, im2col+GEMM은 GEMM의 장점을 얻는 대신 변환 비용과 memory overhead를 감수해야 한다.

{% endraw %}

---

이전: [HW4. Assignment - CUDA Conv2d](hw4-assignment-cuda-conv2d.md) · 다음: [HW5. Assignment - CUDA Sum Reduction](hw5-assignment-cuda-sum-reduction.md)
