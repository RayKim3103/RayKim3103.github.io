---
layout: page
title: "HW4. Assignment - CUDA Conv2d"
permalink: /studies/systems/parallel-programming/hw4-assignment-cuda-conv2d/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW4%20Assignment%20-%20CUDA%20Conv2d.md)

{% raw %}
이전: [Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)  
다음: [Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)

## 핵심 요약

HW4는 CUDA로 convolution을 두 방식으로 구현하는 과제다. 첫째는 direct Conv2d kernel이고, 둘째는 im2col로 convolution을 matrix multiplication으로 변환한 뒤 matmul을 수행하는 방식이다. 제출 대상은 `conv.h`이며, filter size가 구현에 미치는 영향을 신중히 고려해야 한다.

## 구현 방식

| 방식 | 설명 |
|---|---|
| Parallel Conv2d | GPU에서 convolution을 직접 계산 |
| im2col + Matmul | input patch를 column matrix로 펼친 뒤 GEMM 수행 |

## 문제 조건

- Grading 중 input size는 고정
- input value는 달라질 수 있음
- batch size는 항상 1
- filter size가 memory access와 output size에 미치는 영향을 고려해야 함

## 숫자로 확인하기 — filter size가 output 크기와 halo에 미치는 영향

입력이 $$64\times64$$(batch=1)이고 filter가 각각 $$3\times3$$, $$7\times7$$일 때 valid convolution의 output 크기를 비교한다.

$$
H_{out}=W_{out}=H-K+1
$$

| Filter | Output 크기 | Output pixel 수 |
|---|---|---:|
| $$3\times3$$ | $$62\times62$$ | 3844 |
| $$7\times7$$ | $$58\times58$$ | 3364 |

Filter가 커질수록 output이 작아지는 것은 물론, shared memory tiling을 쓸 때 필요한 "halo"(출력 tile 하나를 계산하려고 더 읽어야 하는 주변 영역)도 커진다. `TILE=16` 출력 tile 하나를 계산하는 데 필요한 input load tile 크기는 $$(\text{TILE}+K-1)^2$$이므로

$$
K=3: (16+2)^2=324, \qquad K=7: (16+6)^2=484
$$

즉 같은 16x16 output tile이라도 $$K=7$$은 $$K=3$$보다 약 $$484/324\approx1.49$$**배** 더 많은 input 값을 shared memory에 load해야 한다 — "filter size가 memory access에 미치는 영향을 고려해야 한다"는 문제 조건이 이 halo 크기 계산에서 구체화된다.

## 구현 대상

`conv.h` 안의 세 함수를 구현한다.

예상 구성:

- direct convolution
- im2col 변환
- matmul 기반 convolution

## 채점 포인트

- Correctness
- CUDA kernel 성능
- memory access pattern 최적화
- strict performance benchmark 만족

## 규칙

- 지정된 HW4 directory 구조 유지
- `make run` 정상 실행
- 출력 문구 변경 금지
- deadline 이후 timestamp 변경 주의
- CUDA library 사용 금지
- file permission 유지

## 관련 강의 연결

- [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
- [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)
- [CUDA DNN - Convolution과 im2col](07-cuda-dnn-convolution-im2col.md)

## 복습 질문

- $$64\times64$$ 입력에서 $$3\times3$$과 $$7\times7$$ filter의 output 크기(62x62 vs 58x58)를 직접 계산할 수 있는가?
- `TILE=16` output tile에서 halo를 포함한 load tile 크기가 $$K=3$$일 때와 $$K=7$$일 때 왜 각각 324, 484가 되는지 계산할 수 있는가?
- Filter가 커질수록 halo load 비용이 늘어나는 것이 direct convolution의 shared memory 최적화에 어떤 부담을 주는지 설명할 수 있는가?

## 정리

HW4는 convolution 자체와 GEMM으로 변환한 convolution을 비교하는 과제다. Direct 방식은 중간 memory가 적지만 최적화가 어렵고, im2col+GEMM은 matrix multiplication 최적화를 재사용할 수 있지만 중간 데이터 변환 비용이 생긴다.

{% endraw %}

---

이전: [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md) · 다음: [HW4. Report - Direct Convolution과 im2col GEMM](hw4-report-direct-convolution-im2col-gemm.md)
