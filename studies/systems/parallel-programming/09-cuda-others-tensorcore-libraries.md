---
layout: page
title: "09. CUDA Others - TensorCore와 CUDA Libraries"
permalink: /studies/systems/parallel-programming/09-cuda-others-tensorcore-libraries/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/09%20CUDA%20Others%20-%20TensorCore%EC%99%80%20CUDA%20Libraries.md)

{% raw %}
이전: [CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)  
다음: [Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)

## 핵심 요약

이 강의는 TensorCore, CUDA library, 그리고 최신 GPU에서 기존 설명이 단순화였던 부분들을 보완한다. 핵심은 모든 kernel을 직접 작성할 필요는 없으며, GEMM/DNN/parallel primitive는 cuBLAS, cuDNN, Thrust 같은 library가 매우 강력하다는 점이다.

## TensorCore

Matrix multiplication은 작은 matrix multiply-accumulate의 반복으로 볼 수 있다. TensorCore는 이런 작은 matrix 연산을 hardware에서 매우 빠르게 처리하는 특수 unit이다.

특징:

- 작은 tile 단위 matrix multiply-accumulate 수행
- 16bit multiplication과 32bit accumulation 지원
- GPU 세대별 지원 precision이 다름
- deep learning GEMM/conv 성능의 핵심 hardware

## CUDA Libraries

| Library | 역할 |
|---|---|
| cuBLAS | BLAS, GEMM/GEMV 등 dense linear algebra |
| cuDNN | convolution, pooling, normalization 등 DNN primitive |
| Thrust | C++ STL 스타일 parallel algorithms |

직접 kernel을 쓰는 것은 학습과 특수 최적화에는 중요하지만, 실제 production에서는 library kernel이 더 빠르고 안정적인 경우가 많다.

## Independent Thread Scheduling

초기 CUDA 설명에서는 warp가 lockstep으로 움직인다고 단순화한다. 하지만 Volta 이후 GPU는 thread마다 program counter를 갖고 더 독립적으로 scheduling될 수 있다.

결과:

- 더 유연한 scheduling 가능
- 일부 warp-synchronous trick이 더 이상 안전하지 않을 수 있음
- warp 내부 통신이나 reduction에서는 `__syncwarp()` 필요 가능

## Large L2 Cache

최신 GPU는 더 큰 L2 cache를 가진다. 예를 들어 A100은 40MB L2 cache를 갖지만 SM 수로 나누면 SM당 절대적으로 무한히 큰 것은 아니다. 그래도 높은 bandwidth와 cache 효과는 성능 분석에서 중요하다.

## 숫자로 확인하기 — A100의 SM당 L2 cache 몫

A100은 L2 cache 40MB, SM(Streaming Multiprocessor) 108개를 가진다. "SM당 절대적으로 무한히 큰 것은 아니다"라는 문장을 수로 확인하면, L2를 SM 수만큼 균등하게 나눴을 때 SM 하나가 쓸 수 있는 몫은

$$
\frac{40\text{MB}}{108} \approx 0.37\text{MB} = 379\text{KB}
$$

이는 SM 하나의 shared memory 용량(보통 최대 164KB~228KB 수준, 세대별로 다름)과 비슷한 자릿수다 — 40MB라는 절대값은 크지만, 108개 SM이 동시에 나눠 쓰면 SM 하나 몫은 shared memory 정도의 크기로 줄어든다. 그래서 "L2가 크다"는 사실이 곧 "모든 kernel의 working set이 L2에 다 들어간다"는 뜻은 아니며, 여전히 shared memory로 명시적 재사용을 관리하는 것이 중요하다.

## 정리

이 강의의 핵심은 “직접 kernel 작성”과 “library 사용”의 균형이다. CUDA를 잘하려면 low-level 최적화를 이해해야 하지만, 동시에 TensorCore와 cuBLAS/cuDNN/Thrust 같은 검증된 고성능 primitive를 언제 사용할지 판단할 수 있어야 한다.

## 복습 질문

- A100의 L2 cache 40MB를 SM 108개로 나누면 SM당 몫이 왜 shared memory 용량과 비슷한 자릿수(약 379KB)가 되는지 계산할 수 있는가?
- "L2 cache가 크다"는 사실이 왜 shared memory의 명시적 관리를 대체하지 못하는지 설명할 수 있는가?
- Independent thread scheduling 이후 왜 기존의 warp-synchronous trick(예: 마지막 warp unrolling)이 `__syncwarp()` 없이는 위험해지는지 설명할 수 있는가?

{% endraw %}

---

이전: [08. CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md) · 다음: [10. Prefix Sum - GPU Scan 알고리즘](10-prefix-sum-gpu-scan.md)
