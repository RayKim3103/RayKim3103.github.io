---
layout: page
title: "12. CUDA Stream - Pinned Memory와 비동기 파이프라인"
permalink: /studies/systems/parallel-programming/12-cuda-stream-pinned-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/12%20CUDA%20Stream%20-%20Pinned%20Memory%EC%99%80%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8.md)

{% raw %}
이전: [Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md)  
다음: [CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)

## 핵심 요약

이 강의는 host-device memory copy 병목을 줄이기 위한 pinned memory와 CUDA stream을 다룬다. GPU kernel이 빠르더라도 PCIe/NVLink를 통한 H2D/D2H copy가 오래 걸리면 전체 시간이 copy에 지배된다. Pinned memory와 `cudaMemcpyAsync`, multiple stream을 사용하면 copy와 kernel 실행을 overlap하여 pipeline을 만들 수 있다.

## Memcpy 병목

8192x8192 transpose 예시에서 kernel 자체는 약 2.3ms인데, host-device copy는 각각 44ms 수준으로 더 크다. 이 경우 kernel만 최적화해도 전체 실행시간은 크게 줄지 않는다.

## Pinned Memory

Pinned memory는 OS가 page out하지 않도록 고정한 host memory다. GPU DMA가 안정적으로 접근할 수 있어 pageable memory보다 transfer가 빠르다.

사용 API:

```cpp
cudaMallocHost(&h_ptr, bytes);
```

또는 기존 malloc memory를 등록:

```cpp
h_ptr = malloc(bytes);
cudaHostRegister(h_ptr, bytes, 0);
```

예시에서는 H2D/D2H copy가 44ms에서 20ms 정도로 줄어드는 효과가 제시된다.

## Stream

CUDA stream은 비동기 작업 queue다. 서로 다른 stream의 copy와 kernel은 resource가 겹치지 않으면 overlap될 수 있다.

```cpp
cudaStream_t stream1, stream2;
cudaStreamCreate(&stream1);
cudaStreamCreate(&stream2);
```

Kernel launch도 stream을 지정할 수 있다.

```cpp
MatMulKernel<<<dimGrid, dimBlock, 0, stream1>>>(...);
```

## Pipeline 구조

여러 matrix를 처리할 때 각 matrix에 대해 다음 흐름을 하나의 stream에 배치한다.

```text
H2D copy -> kernel -> D2H copy
```

그리고 여러 stream을 동시에 사용하면:

```text
stream1: M1 H2D -> M1 kernel -> M1 D2H
stream2: M2 H2D -> M2 kernel -> M2 D2H
stream3: M3 H2D -> M3 kernel -> M3 D2H
stream4: M4 H2D -> M4 kernel -> M4 D2H
```

Copy engine과 compute engine이 동시에 일할 수 있으면 전체 throughput이 좋아진다.

## 숫자로 확인하기 — pinned memory와 stream overlap의 시간 절감

8192x8192 transpose 예시의 실측값(kernel ≈2.3ms, H2D/D2H 각 ≈44ms)을 그대로 사용해 총 실행 시간을 단계별로 계산한다.

**Pageable memory, 순차 실행**(H2D → kernel → D2H를 차례로 대기):
$$
44 + 2.3 + 44 = 90.3\text{ms}
$$

**Pinned memory 적용**(H2D/D2H가 각각 44ms→20ms로 단축, 여전히 순차 실행):
$$
20 + 2.3 + 20 = 42.3\text{ms}
$$
이것만으로 $$90.3 \to 42.3$$, 약 **2.1배** 단축된다.

**Pinned memory + 4개 stream으로 4개의 독립 matrix를 pipeline 처리**: 이상적으로 copy와 kernel이 완전히 겹치면, 전체 시간은 "가장 긴 자원(H2D/D2H copy, 각 20ms)이 4번 연속 도는 시간 + 파이프라인을 채우고 비우는 데 걸리는 kernel 한 번분"으로 근사할 수 있다.

$$
\underbrace{4 \times 20\text{ms}}_{\text{H2D 4개 연속}} + 2.3\text{ms}(\text{마지막 kernel}) + 20\text{ms}(\text{마지막 D2H}) \approx 102.3\text{ms} \quad (\text{matrix 4개 총합})
$$

4개를 순차(non-pipeline) pinned 방식으로 처리하면 $$4\times42.3=169.2\text{ms}$$가 걸리므로, pipeline은 $$169.2\to102.3$$, 약 **1.65배** 추가 단축을 준다. 세 방식을 이어보면 원래 pageable 순차(4개 기준 $$4\times90.3=361.2\text{ms}$$) 대비 pinned+stream pipeline은 총 **약 3.5배** 빠르다 — "kernel만 최적화해도 전체 시간이 크게 줄지 않는다"는 문장과, "pinned memory와 stream이 전체 성능을 좌우한다"는 결론이 이 숫자에서 함께 확인된다.

## NVVP 확인

NVIDIA Visual Profiler에서는 timeline을 통해 stream별 H2D, kernel, D2H가 실제로 overlap되는지 확인할 수 있다. Zoom in해서 copy와 kernel 사이의 빈 공간이 줄었는지 보는 것이 중요하다.

## 복습 질문

- pageable 순차 실행(90.3ms)과 pinned 순차 실행(42.3ms)의 차이가 왜 약 2.1배인지 계산할 수 있는가?
- 4개 matrix를 pinned+stream pipeline으로 처리할 때(약 102.3ms)와 pinned 순차 처리(169.2ms)를 비교해 pipeline의 이득을 설명할 수 있는가?
- 이 예제에서 kernel 실행 시간(2.3ms)이 전체 시간에서 왜 거의 무시할 수준인지, 그리고 그것이 "무엇을 최적화해야 하는가"에 주는 시사점을 설명할 수 있는가?

## 정리

CUDA stream 최적화는 kernel 내부 최적화가 아니라 application-level scheduling 최적화다. Copy가 kernel보다 훨씬 긴 workload에서는 pinned memory와 async stream pipeline이 전체 성능을 크게 좌우한다.

{% endraw %}

---

이전: [11. Triton Introduction - Triton DSL과 Kernel Fusion](11-triton-introduction-dsl-kernel-fusion.md) · 다음: [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)
