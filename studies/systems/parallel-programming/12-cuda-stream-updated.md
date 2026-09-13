---
layout: page
title: "12. CUDA Stream Updated - 업데이트본 요약"
permalink: /studies/systems/parallel-programming/12-cuda-stream-updated/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/12%20CUDA%20Stream%20Updated%20-%20%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8%EB%B3%B8%20%EC%9A%94%EC%95%BD.md)

{% raw %}
이전: [CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md)  
다음: [CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)

## 핵심 요약

이 업데이트본은 CUDA stream 강의와 같은 주제인 pinned memory, asynchronous copy, stream pipeline, NVVP timeline 확인을 다룬다. 추출된 내용 기준으로 기존 stream 자료와 구조가 거의 같으므로, 이 노트는 업데이트본에서 확인해야 할 실무 포인트를 중심으로 정리한다.

## 확인해야 할 핵심 개념

| 개념 | 내용 |
|---|---|
| Pageable memory | 일반 host memory, DMA 전송 전 staging 비용 가능 |
| Pinned memory | page-locked host memory, H2D/D2H 전송 가속 |
| `cudaMemcpyAsync` | stream에 비동기 copy enqueue |
| CUDA stream | 작업 순서를 보존하는 비동기 queue |
| Pipeline | copy와 kernel을 겹쳐 전체 처리량 향상 |

## Pinned Memory API

```cpp
cudaMallocHost(&h_ptr, bytes);
cudaHostRegister(h_ptr, bytes, 0);
```

Pinned memory는 많이 쓰면 OS memory management에 부담이 되므로 필요한 buffer에 제한적으로 사용해야 한다.

## Stream Pipeline 작성 패턴

각 stream 안에서는 순서가 보장된다.

```cpp
cudaMemcpyAsync(d_in, h_in, size, cudaMemcpyHostToDevice, stream);
kernel<<<grid, block, 0, stream>>>(d_in, d_out);
cudaMemcpyAsync(h_out, d_out, size, cudaMemcpyDeviceToHost, stream);
```

여러 stream 간에는 독립적으로 실행될 수 있으므로, data chunk 또는 여러 matrix를 stream별로 분배하면 overlap이 가능하다.

## 숫자로 확인하기 — stream 수와 파이프라인 깊이

[원본 강의 노트](12-cuda-stream-pinned-memory.md)의 8192x8192 transpose 수치(H2D/D2H 각 20ms pinned 기준, kernel 2.3ms)를 그대로 쓰면, stream 수를 1개→2개→4개로 늘렸을 때 8개 matrix를 처리하는 이상적인 총 시간이 어떻게 줄어드는지 대략적으로 짚을 수 있다.

- Stream 1개(완전 순차): $$8 \times (20+2.3+20) = 8\times42.3 = 338.4\text{ms}$$
- 여러 stream으로 8개를 연속 pipeline 처리(H2D/kernel/D2H 3단계, 병목은 20ms copy): $$(8-1)\times20 + (20+2.3+20) = 140+42.3 = 182.3\text{ms}$$ (pipeline을 채우고 비우는 시간을 더한 표준 pipeline 공식)

stream 수를 늘릴수록 이득이 커지지만, GPU의 copy engine 수(보통 1~2개)나 SM 자원이 한계에 도달하면 stream을 더 늘려도 추가 overlap이 생기지 않는다 — "불필요한 global device synchronization을 줄인다"는 체크리스트 항목이 왜 중요한지가 바로 이 한계 지점 때문이다.

## 업데이트본 관점의 체크리스트

- Host buffer가 pinned memory인지 확인한다.
- `cudaMemcpyAsync`에 stream argument를 명시한다.
- Kernel launch에도 같은 stream을 지정해 한 data chunk의 순서를 유지한다.
- 불필요한 global device synchronization을 줄인다.
- NVVP 또는 profiler timeline에서 실제 overlap을 확인한다.

## 복습 질문

- 8개 matrix를 완전 순차(338.4ms)로 처리할 때와 pipeline(182.3ms)으로 처리할 때의 차이를 계산할 수 있는가?
- Pipeline 시간 공식 $$(n-1)\times\text{bottleneck} + \sum\text{stage}$$에서 "bottleneck"이 왜 20ms copy 단계인지 설명할 수 있는가?
- Stream 수를 계속 늘려도 성능 개선이 멈추는 이유를 GPU의 copy engine 개수 한계와 연결해 설명할 수 있는가?

## 정리

업데이트본의 핵심은 stream 최적화를 코드에 넣었다고 끝나는 것이 아니라, profiler timeline에서 copy/compute overlap이 실제 발생하는지 검증해야 한다는 점이다.

{% endraw %}

---

이전: [12. CUDA Stream - Pinned Memory와 비동기 파이프라인](12-cuda-stream-pinned-memory.md) · 다음: [13. CUDA Debug and Profiling - cuda-gdb와 nvprof](13-cuda-debug-and-profiling-gdb-nvprof.md)
