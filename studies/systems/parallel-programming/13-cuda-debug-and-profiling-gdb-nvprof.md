---
layout: page
title: "13. CUDA Debug and Profiling - cuda-gdb와 nvprof"
permalink: /studies/systems/parallel-programming/13-cuda-debug-and-profiling-gdb-nvprof/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/13%20CUDA%20Debug%20and%20Profiling%20-%20cuda-gdb%EC%99%80%20nvprof.md)

{% raw %}
이전: [CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md)  
다음: [Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md)

## 핵심 요약

이 강의는 CUDA kernel의 correctness와 performance를 확인하기 위한 debugging/profiling 도구를 다룬다. `cuda-gdb`로 kernel 내부 thread/block/warp/lane에 focus를 맞추고, memcheck/racecheck로 silent error를 찾으며, profiler로 kernel timeline과 memory behavior를 분석한다.

## Compile Option

CUDA debugging에는 host와 device debug 정보가 필요하다.

| 옵션 | 의미 |
|---|---|
| `-g` | host code debug symbol |
| `-G` | device code debug symbol |

`-G`는 device optimization을 비활성화하고 성능을 크게 낮출 수 있으므로 debugging 때만 사용한다.

## cuda-gdb 기본 명령

| 명령 | 역할 |
|---|---|
| `run` 또는 `r` | 프로그램 시작 |
| `continue` 또는 `c` | 실행 재개 |
| `list` | source 표시 |
| `next` | 다음 source line |
| `step` | 함수 안으로 진입 |
| `nexti`, `stepi` | assembly instruction 단위 실행 |
| `print var` | 변수 값 출력 |
| `print $pc`, `print $R0` | register 확인 |

## Breakpoint

```gdb
break bcast
break bcast.cu:12
set cuda break_on_launch application
```

Kernel launch 시점에 breakpoint를 걸거나, 특정 source line에 걸 수 있다.

## CUDA Focus

CUDA kernel은 grid/block/thread/warp/lane이 많으므로 현재 focus를 확인하고 바꿀 수 있어야 한다.

```gdb
info cuda kernels
cuda kernel block thread
cuda kernel 0 block 0 thread 3
cuda lane 5
```

`*` 표시는 현재 focus를 나타낸다. Divergent thread/warp는 별도로 표시될 수 있다.

## Memcheck

Memcheck는 runtime error checker다. 특히 out-of-bounds access처럼 결과만 보고 찾기 어려운 silent error에 유용하다.

사용 방식:

- cuda-gdb 내부: `set cuda memcheck on`
- standalone tool
- 옵션: `--leak-check full`
- race 확인: `--tool racecheck`

## Profiling

성능 분석 도구:

- `nvprof`
- CSV 출력: `nvprof --csv`
- GPU trace: `nvprof --print-gpu-trace`
- 파일 출력: `nvprof -o tp.prof ./app`
- Visual profiler timeline

최신 GPU에서는 `nvprof` 지원이 제한될 수 있으므로 Nsight 계열 도구를 함께 고려해야 한다.

## 정리

CUDA debugging은 일반 C++ debugging보다 focus 차원이 많다. block/thread/warp/lane을 명시적으로 좁혀야 하며, memory error와 race는 memcheck/racecheck로 확인해야 한다. 성능 최적화는 profiler timeline과 metric을 보고 병목을 찾는 과정이다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. CUDA Debug and Profiling - cuda-gdb와 nvprof**를 다루며, CPU thread, CUDA, Triton, MPI를 통해 병렬 하드웨어에서 성능을 끌어내는 방법을 배운다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 병렬 프로그래밍 주제에서는 thread mapping, memory hierarchy, synchronization, profiling metric을 같은 표에 놓고 본다.
- GPU 최적화는 correctness baseline을 고정한 뒤 coalescing, tiling, occupancy, register pressure를 하나씩 바꾸며 측정한다.
- 병렬화는 일을 나누는 것뿐 아니라 memory hierarchy, synchronization, occupancy, load balance를 맞추는 문제다.
- CUDA kernel 성능은 global memory coalescing, shared memory bank conflict, register pressure, occupancy가 함께 결정한다.
- Triton과 GPU library도 tile size, data layout, fusion, bandwidth 병목을 이해해야 잘 쓸 수 있다.

### 문제 풀이 또는 구현 루틴

- arithmetic intensity를 계산해 compute-bound인지 memory-bound인지 가른다.
- thread/block mapping, memory access pattern, shared memory reuse, synchronization 지점을 표로 적는다.
- 최적화 전후에는 correctness check, timing methodology, profiler metric을 같이 남긴다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- thread 수를 늘리는 것만으로 speedup이 선형 증가하지 않는다.
- shared memory를 쓰면 항상 빠른 것이 아니라 bank conflict와 load overhead를 함께 봐야 한다.
- 비동기 stream/memory copy는 dependency를 명확히 기록하지 않으면 race처럼 보이는 오류가 생긴다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 병목은 연산량, memory bandwidth, synchronization, launch overhead 중 무엇인가?
- 각 thread가 담당하는 데이터와 인접 thread의 memory access가 coalesced 되는가?
- 최적화가 정확도를 유지한다는 검증과 성능 개선 근거가 함께 있는가?
- **13. CUDA Debug and Profiling - cuda-gdb와 nvprof**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md) · 다음: [14. Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md)
