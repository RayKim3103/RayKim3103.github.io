---
layout: page
title: "10. Prefix Sum - GPU Scan 알고리즘"
permalink: /studies/systems/parallel-programming/10-prefix-sum-gpu-scan/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Parallel_Programming/lecture_notes/10%20Prefix%20Sum%20-%20GPU%20Scan%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98.md)

{% raw %}
이전: [CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md)  
다음: [Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)

## 핵심 요약

Prefix sum 또는 scan은 배열의 각 위치에 그 앞 원소들의 누적값을 저장하는 병렬 primitive다. Quicksort partition, histogram, polynomial evaluation, radix sort, regular expression search 등 다양한 알고리즘의 building block이다.

## Reduce와 Prefix Sum

| 연산 | 결과 |
|---|---|
| Reduce | 전체 배열을 하나의 값으로 결합 |
| Prefix sum | 각 index별 partial accumulation을 모두 계산 |

예를 들어 `[1,2,3,4]`의 inclusive scan은 `[1,3,6,10]`이고, exclusive scan은 `[0,1,3,6]`이다.

## Naive Parallel Prefix Sum

각 단계마다 거리 `1, 2, 4, ...`의 neighbor를 더한다. Hillis-Steele 또는 Kogge-Stone 형태다.

장점:

- `O(log N)` 단계
- 병렬성이 매우 큼

단점:

- 전체 work가 `O(N log N)`으로 work-efficient하지 않다.
- 각 단계마다 barrier가 필요하다.

## Kogge-Stone Algorithm

Kogge-Stone은 가까운 neighbor부터 점점 먼 neighbor까지 더한다.

```text
step 1: i >= 1이면 a[i] += a[i-1]
step 2: i >= 2이면 a[i] += a[i-2]
step 4: i >= 4이면 a[i] += a[i-4]
...
```

특징:

- 빠른 depth: `O(log N)`
- 많은 연산량
- 많은 thread가 계속 active

## Brent-Kung Algorithm

Brent-Kung은 balanced binary tree pattern을 사용한다.

두 phase:

1. Up-sweep 또는 reduce phase: tree 위로 partial sum 생성
2. Down-sweep phase: prefix 값을 아래로 전파

특징:

- 시간: 대략 `2 log N`
- work: `O(N)`
- Kogge-Stone보다 단계는 많지만 계산량이 적다.

## Kogge-Stone vs Brent-Kung

| 항목 | Kogge-Stone | Brent-Kung |
|---|---|---|
| Time complexity | `log N` | `2 log N` |
| Work | `O(N log N)` | `O(N)` |
| 장점 | 짧은 latency | work-efficient |
| 단점 | 연산량 많음 | 단계 수 많음 |

## GPU 구현 포인트

- Shared memory에 block 내부 데이터를 올린다.
- 단계마다 `__syncthreads()`를 사용한다.
- Exclusive scan이면 입력을 한 칸 shift하고 첫 값을 identity로 둔다.
- 큰 배열은 block별 scan 후 block sum을 다시 scan하고 각 block에 더한다.
- Shared memory tree access에서 bank conflict가 생길 수 있어 padding이 필요하다.

## 숫자로 확인하기 — `N=8` Kogge-Stone 연산 횟수 세기

`[1,2,3,4,5,6,7,8]`에 Kogge-Stone naive scan을 적용해 실제로 몇 번의 덧셈이 일어나는지 단계별로 세어본다. Sequential scan이라면 덧셈이 정확히 $$N-1=7$$번이면 충분하다.

| Step (거리 $$d$$) | 덧셈이 일어나는 index | 덧셈 횟수 |
|---|---|---:|
| $$d=1$$ | $$i \ge 1$$인 모든 $$i$$ (1~7) | 7 |
| $$d=2$$ | $$i \ge 2$$인 모든 $$i$$ (2~7) | 6 |
| $$d=4$$ | $$i \ge 4$$인 모든 $$i$$ (4~7) | 4 |

$$\log_2 8 = 3$$단계 동안 총 덧셈 횟수는 $$7+6+4=17$$번으로, sequential scan의 7번보다 **약 2.4배 많은 연산**을 수행한다. $$N$$이 커질수록 이 배율은 $$O(N\log N)/O(N) = O(\log N)$$으로 계속 커진다 — $$N=8$$에서 이미 나타나는 이 "더 많은 총 작업량"이 "work-efficient하지 않다"는 문장의 실제 의미이며, 다음 노트([10. Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md))의 Blelloch scan이 해결하려는 문제다.

## Lesson

강의의 마지막 메시지는 lock 기반 누적보다 병렬 알고리즘 구조를 찾는 것이 더 좋다는 것이다. Prefix sum은 단순해 보이지만 많은 병렬 알고리즘의 기반이며, scan을 잘 구현하면 여러 상위 문제를 효율적으로 풀 수 있다.

## 복습 질문

- `N=8` 배열에서 Kogge-Stone scan의 3단계 각각에서 덧셈이 몇 번 일어나는지, 그리고 총합 17번을 직접 셀 수 있는가?
- 이 17번이 sequential scan의 7번보다 왜 더 많은지, 그리고 $$N$$이 커질수록 이 차이가 왜 $$O(\log N)$$ 배로 커지는지 설명할 수 있는가?
- Inclusive scan `[1,3,6,10]`과 exclusive scan `[0,1,3,6]`의 차이를 `[1,2,3,4]` 예시로 설명할 수 있는가?

## 정리

Prefix sum은 reduce보다 더 많은 정보를 유지하는 누적 연산이다. GPU에서는 Kogge-Stone과 Brent-Kung의 depth/work tradeoff, shared memory bank conflict, 큰 배열 decomposition이 핵심이다.

{% endraw %}

---

이전: [09. CUDA Others - TensorCore와 CUDA Libraries](09-cuda-others-tensorcore-libraries.md) · 다음: [10. Prefix Sum NVIDIA Supplement - Work-Efficient Scan](10-prefix-sum-nvidia-supplement-work-efficient-scan.md)
