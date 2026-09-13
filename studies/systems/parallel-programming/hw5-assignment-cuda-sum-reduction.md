---
layout: page
title: "HW5. Assignment - CUDA Sum Reduction"
permalink: /studies/systems/parallel-programming/hw5-assignment-cuda-sum-reduction/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW5%20Assignment%20-%20CUDA%20Sum%20Reduction.md)

{% raw %}
이전: [Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)  
다음: [Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)

## 핵심 요약

HW5는 CUDA sum reduction을 구현하는 과제다. 강의자료에서 제공한 7가지 reduction kernel version을 실행할 수 있도록 kernel invocation을 작성하고, 최종 제출에서는 `2^24 = 16777216`개 item reduction에 대해 가장 빠른 version을 사용해야 한다.

## Reduction 정의

Parallel reduction은 배열 원소를 결합해 하나의 값을 만드는 알고리즘이다.

예:

```text
[a0, a1, a2, ..., an] -> sum
```

## 제공 변수

| 변수 | 의미 |
|---|---|
| `g_idata` | host input sequence |
| `g_odata` | host output buffer |
| `d_idata` | device input sequence |
| `d_odata` | device output buffer, 최종 결과는 `d_odata[0]` |

입력 sequence의 총합은 32bit signed integer 범위를 넘지 않도록 보장된다.

## 구현 범위

`main()`은 수정할 수 없고, 필요한 추가 allocation은 `reduction_optimized()` 내부에서 수행해야 한다. Host-device copy와 기본 device memory allocation/deallocation은 main에서 처리된다.

## 숫자로 확인하기 — `2^24`개 원소의 block 구성

$$2^{24}=16{,}777{,}216$$개 원소를 block당 256 thread, thread당 원소 2개(first-add-during-load)로 처리한다고 하면, thread 하나가 원소 2개를 담당하므로 필요한 thread 수는

$$
16{,}777{,}216 / 2 = 8{,}388{,}608\text{개}
$$

필요한 block 수는

$$
8{,}388{,}608 / 256 = 32{,}768\text{개} = 2^{15}\text{개}
$$

이는 하나의 kernel launch로 처리 가능한 grid 크기(x축 최대 $$2^{31}-1$$) 안에 충분히 들어간다. 이 32,768개 block 각각이 만든 partial sum은 다시 global memory에 저장되고, 두 번째 kernel launch(또는 남은 32,768개를 다시 reduce하는 단계)에서 최종 하나의 값으로 합쳐진다 — "block 간 global sync가 없어 kernel decomposition이 필요하다"는 문장이 실제로 요구하는 두 번째 launch가 바로 이 32,768개 partial sum을 다시 reduce하는 단계다.

## 실행 및 실험

제공된 Makefile로 여러 version을 실행한다.

```bash
make 1
make 2
make 3
make 4
make run
```

목표는 7가지 version을 모두 이해하고, 최종적으로 가장 빠른 version을 선택하는 것이다.

## 채점 조건

- 출력 형식 변경 금지
- CUDA library 사용 금지
- deadline 이후 timestamp 변경 주의
- grading server에서 5회 실행 중 maximum 기준
- local congestion 또는 server congestion을 고려해 여유 있는 성능 필요

## 관련 강의 연결

- [CUDA Reduction - Parallel Reduction 최적화](08-cuda-reduction-parallel.md)

## 복습 질문

- $$2^{24}$$개 원소, block당 256 thread, thread당 2개 원소 처리일 때 필요한 block 수(32,768)를 직접 계산할 수 있는가?
- 이 32,768개 block이 만든 partial sum을 왜 두 번째 kernel launch로 다시 reduce해야 하는지, CUDA의 global synchronization 제약과 연결해 설명할 수 있는가?
- 7가지 reduction version 중 어떤 것이 이 정도 규모(`2^24`)에서 가장 빠를지 예상하고, 그 근거를 memory bandwidth 관점에서 설명할 수 있는가?

## 정리

HW5는 CUDA reduction 최적화의 실습 과제다. 단순 합계 계산이지만, divergent branch, bank conflict, idle thread, loop unrolling, multiple adds per thread, kernel decomposition을 모두 확인해야 한다.

{% endraw %}

---

이전: [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md) · 다음: [HW5. Reference - Optimizing Parallel Reduction in CUDA](hw5-reference-optimizing-parallel-reduction-in-cuda.md)
