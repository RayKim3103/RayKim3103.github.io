---
layout: page
title: "HW3. Assignment - CUDA LoRA"
permalink: /studies/systems/parallel-programming/hw3-assignment-cuda-lora/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/HW3%20Assignment%20-%20CUDA%20LoRA.md)

{% raw %}
이전: [Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md)  
다음: [Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)

## 핵심 요약

HW3는 LoRA 연산을 CUDA C++로 구현하는 과제다. LoRA 자체의 세부 이론보다, LoRA가 matrix multiplication에 크게 의존한다는 점에 초점을 둔다. 제출 대상은 `lora.h`이며, CUDA matmul과 scaling/addition을 정확하고 빠르게 구현해야 한다.

## LoRA 연산

NumPy 형태:

```python
def lora(x, W, A, B, alpha):
    out_linear = x @ W.T
    out_lora = x @ A.T @ B.T * scale
    return out_linear + out_lora
```

Shape:

| Tensor | Shape | 의미 |
|---|---|---|
| `x` | `[B, in_dim]` | input |
| `W` | `[out_dim, in_dim]` | original weight |
| `A` | `[r, in_dim]` | down projection |
| `B` | `[out_dim, r]` | up projection |
| output | `[B, out_dim]` | LoRA 적용 결과 |

## 숫자로 확인하기 — LoRA가 원본 weight보다 훨씬 가벼운 이유

`B=32`(batch), `in_dim=out_dim=4096`, rank `r=8`이라고 하자(LoRA의 핵심은 $$r \ll \text{in\_dim, out\_dim}$$).

**원본 linear** `x @ W.T`의 곱셈 횟수: $$B \times \text{in\_dim} \times \text{out\_dim} = 32\times4096\times4096 \approx 5.37\times10^8$$

**LoRA 경로** `x @ A.T @ B.T`의 곱셈 횟수(두 단계 합):
$$
\underbrace{B\times\text{in\_dim}\times r}_{x@A^T,\ 32\times4096\times8} + \underbrace{B\times r\times\text{out\_dim}}_{(\cdot)@B^T,\ 32\times8\times4096} = 1{,}048{,}576 + 1{,}048{,}576 = 2{,}097{,}152 \approx 2.1\times10^6
$$

LoRA 경로는 원본 대비 $$5.37\times10^8 / 2.1\times10^6 \approx 256$$**배** 적은 곱셈만 필요하다 — 정확히는 $$2\times\text{in\_dim}\times\text{out\_dim} \times r$$ 대비 $$\text{in\_dim}\times\text{out\_dim}$$의 비율이므로 $$\frac{\text{in\_dim}\times\text{out\_dim}}{2r\times\max(\text{in\_dim},\text{out\_dim})} \approx \frac{4096}{16}=256$$에 가깝다. `r`이 작을수록 이 배율은 더 커진다. 다만 최종 output은 원본 linear 결과와 LoRA 경로 결과를 더해야 하므로, 전체 연산량은 "원본 linear + 작은 LoRA 경로"이지 LoRA가 원본을 대체하는 것은 아니다 —과제에서 두 GEMM 모두 정확히 구현해야 하는 이유다.

## 구현 요구사항

- `lora.h`에 CUDA C++ 구현
- CUDA 기반 matrix multiplication
- LoRA scaling과 addition
- 정확한 output 형식 유지
- OpenMP 사용 금지

## 채점 기준

성능 순위 기반 점수:

| 점수 | 조건 |
|---:|---|
| 10 | top 10% |
| 9 | top 30% |
| 8 | top 50% |
| 7 | correctness 만족 |

## 제출 규칙

- HW3 directory를 지정 경로에 배치
- `make run`이 정상 작동해야 함
- `std::cout` 출력 변경 금지
- deadline 이후 timestamp 변경 주의
- file permission 유지

## 관련 강의 연결

- [Intro to CUDA - CUDA 프로그래밍 모델](04-intro-to-cuda.md)
- [CUDA Matrix Multiplication - Shared Memory Tiling](05-cuda-matrix-multiplication-shared-memory-tiling.md)
- [CUDA Transpose and Bank Conflict - Shared Memory 심화](06-cuda-transpose-and-bank-conflict-shared-memory.md)

## 복습 질문

- `in_dim=out_dim=4096`, `r=8`일 때 LoRA 경로가 원본 linear보다 왜 약 256배 적은 곱셈을 필요로 하는지 계산할 수 있는가?
- 이 배율이 $$d/(2r)$$ 형태로 일반화되는 이유를, `B*r*(in+out)` vs `B*in*out` 식에서 유도할 수 있는가?
- LoRA를 구현할 때 원본 linear GEMM을 생략할 수 없는 이유(왜 두 경로를 더해야 하는지)를 설명할 수 있는가?

## 정리

HW3는 CUDA matmul을 실제 deep learning layer 계산에 적용하는 과제다. 핵심은 LoRA 식을 여러 GEMM과 element-wise operation으로 분해하고, shared memory, coalescing, tiling, occupancy를 고려해 구현하는 것이다.

{% endraw %}

---

이전: [HW2. Report - Parallel GEMM과 Freivalds 최적화](hw2-report-parallel-gemm-freivalds.md) · 다음: [HW3. Report - CUDA LoRA Tiled MatMul 최적화](hw3-report-cuda-lora-tiled-matmul.md)
