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

## 예시로 확인하기 — memcheck가 잡는 silent error

`N=1000`인데 `blockDim.x=256`으로 launch하면서 `if (i < n)` boundary check를 실수로 빠뜨렸다고 하자. [04주차](04-intro-to-cuda.md)의 계산대로 마지막 block(`blockIdx.x=3`)의 `threadIdx.x=232~255`인 24개 thread는 전역 index $$i=1000{\sim}1023$$을 만들어 배열 밖 주소에 read/write를 시도한다.

이 24개 thread의 out-of-bounds 접근은 결과값이 우연히 이상해 보이지 않는 이상(다른 메모리 영역을 조용히 덮어써도) 눈에 띄는 crash 없이 넘어갈 수 있다 — 이것이 "결과만 보고 찾기 어려운 silent error"의 실제 사례다. `cuda-gdb` 안에서 `set cuda memcheck on` 상태로 같은 kernel을 실행하면, 이 24개 thread가 접근하는 순간 정확히 "어떤 thread(`block 3, thread 232~255`)가 어떤 주소에 잘못 접근했는지"를 즉시 보고한다 — `if (i<n)` 한 줄을 놓치는 흔한 실수를 memcheck가 바로 이 방식으로 잡아낸다.

## Profiling

성능 분석 도구:

- `nvprof`
- CSV 출력: `nvprof --csv`
- GPU trace: `nvprof --print-gpu-trace`
- 파일 출력: `nvprof -o tp.prof ./app`
- Visual profiler timeline

최신 GPU에서는 `nvprof` 지원이 제한될 수 있으므로 Nsight 계열 도구를 함께 고려해야 한다.

## 복습 질문

- `N=1000`, block당 256 thread에서 `if (i<n)`을 빠뜨렸을 때 정확히 어떤 thread들(block 3의 232~255)이 out-of-bounds 접근을 하는지 설명할 수 있는가?
- 이런 out-of-bounds 오류가 왜 "silent error"인지, 그리고 memcheck가 이를 어떻게 잡아내는지 설명할 수 있는가?
- `-G` 옵션이 device optimization을 비활성화하는데도 debugging에만 쓰고 release build에는 쓰지 않는 이유를 설명할 수 있는가?

## 정리

CUDA debugging은 일반 C++ debugging보다 focus 차원이 많다. block/thread/warp/lane을 명시적으로 좁혀야 하며, memory error와 race는 memcheck/racecheck로 확인해야 한다. 성능 최적화는 profiler timeline과 metric을 보고 병목을 찾는 과정이다.

{% endraw %}

---

이전: [12. CUDA Stream Updated - 업데이트본 요약](12-cuda-stream-updated.md) · 다음: [14. Multi GPU - 단일 노드와 MPI](14-multi-gpu-mpi.md)
