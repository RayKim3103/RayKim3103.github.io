---
layout: page
title: "24. 최단 경로"
permalink: /studies/systems/data-structure/24-shortest-paths/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/24%20%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C.md)

{% raw %}
## 문제 상황

지도 서비스에서 한 출발지에서 목적지까지 가장 짧은 경로를 찾는 문제를 생각한다. 도로는 edge, 교차점이나 장소는 vertex, 거리나 시간은 weight로 표현할 수 있다.

## Single-source shortest path

Single-source shortest path 문제:

```text
weighted directed graph G와 source vertex s가 주어졌을 때,
s에서 모든 다른 vertex까지의 shortest path를 찾는다.
```

결과는 parent-link representation으로 shortest path tree 형태로 저장할 수 있다.

| 배열 | 의미 |
|---|---|
| `dist[v]` | 현재까지 알려진 `s`에서 `v`까지의 최단 거리 |
| `prev[v]` | 최단 경로에서 `v` 직전에 오는 vertex 또는 edge |

## Relaxation

Relaxation은 edge `(v, w)`를 통해 `w`로 가는 더 짧은 경로가 발견되면 정보를 갱신하는 연산이다.

```cpp
int v = e.from();
int w = e.to();
if (dist[w] > dist[v] + e.weight()) {
    dist[w] = dist[v] + e.weight();
    pred[w] = e;
}
```

최단 경로 알고리즘 대부분은 relaxation을 반복하는 형태다.

## Dijkstra 알고리즘

Dijkstra 알고리즘은 음수가 아닌 edge weight에서 동작하는 greedy 최단 경로 알고리즘이다.

핵심 아이디어:

- `S`: 최단 거리가 확정된 vertex 집합
- 매 단계에서 `S` 바깥 vertex 중 `dist`가 가장 작은 vertex를 선택해 확정한다.
- 그 vertex에서 나가는 edge들을 relaxation한다.

```text
Dijkstra(G, s)
    for each v in V
        dist[v] = infinity
        prev[v] = null
    dist[s] = 0
    S = empty
    while S != V
        u = vertex outside S with minimum dist[u]
        add u to S
        for each edge (u, w)
            if dist[w] > dist[u] + weight(u, w)
                dist[w] = dist[u] + weight(u, w)
                prev[w] = u
```

## 예시 해석

강의 예시에서는 source `s`에서 각 vertex까지의 최종 결과가 다음처럼 저장된다.

| v | s | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---:|---:|---:|---:|---:|---:|---:|
| `dist[v]` | 0 | 4 | 6 | 10 | 8 | 13 | 11 |
| `prev[v]` | - | s | 1 | 1 | 2 | 6 | 4 |

`prev`를 따라가면 실제 shortest path를 복원할 수 있다.

## 정당성 증명 직관

명제:

```text
v in S이면 dist[v]는 s에서 v까지의 최단 거리다.
```

증명은 `|S|`에 대한 귀납법으로 한다.

다음으로 추가되는 vertex `w`를 생각한다. 만약 `w`로 가는 더 짧은 다른 경로 `P`가 있다면, 그 경로는 어느 순간 `S` 바깥의 첫 vertex `x`로 나가야 한다. 하지만 greedy choice에 의해 `dist[w]`는 `S` 바깥 후보 중 최소였으므로, `x`에 도달하는 시점부터 이미 `w` 후보 거리보다 짧을 수 없다. 따라서 `dist[w]`는 확정해도 된다.

## MST와 최단 경로의 차이

| 문제 | 목표 |
|---|---|
| Minimum Spanning Tree | 모든 vertex를 총 edge weight 최소로 연결 |
| Shortest Path | 특정 source에서 각 vertex까지의 거리 최소 |

MST의 path가 두 vertex 사이 최단 경로일 필요는 없다. 반대로 shortest path tree가 전체 edge weight 합이 최소인 spanning tree일 필요도 없다.

## 자료구조 관점

Dijkstra를 효율적으로 구현하려면 아직 확정되지 않은 vertex 중 최소 `dist`를 빠르게 꺼내야 한다. 이때 priority queue, 특히 binary heap이 자연스럽다.

## 함께 보면 좋은 노트

- [그래프](22-graphs.md)
- [힙과 우선순위 큐](20-heaps-and-priority-queues.md)
- [최소 신장 트리](23-minimum-spanning-trees.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **24. 최단 경로**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 자료구조 선택은 기능이 아니라 operation별 시간/공간 복잡도와 access pattern의 선택이다.
- C++에서는 객체 수명, copy/move, pointer/reference, const correctness가 자료구조 안정성을 좌우한다.
- 알고리즘 분석은 Big-O뿐 아니라 input size, worst/average case, hidden constant, memory locality를 함께 본다.

### 문제 풀이 또는 구현 루틴

- 필요한 operation을 insert/delete/search/traverse/update로 나눈 뒤 빈도를 추정한다.
- 구현 전 invariant를 적고, 각 함수가 invariant를 보존하는지 확인한다.
- 복잡도는 loop 중첩, recursion recurrence, data movement 비용을 분리해 계산한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 포인터 소유권을 명확히 하지 않으면 leak, dangling pointer, double delete가 생긴다.
- 평균 O(1)인 hash table도 충돌과 rehash 비용을 고려해야 한다.
- 정렬/그래프 알고리즘은 안정성, 메모리 사용, 입력 조건에 따라 적합성이 달라진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 자료구조가 유지해야 하는 invariant는 무엇인가?
- 가장 자주 호출되는 operation의 복잡도는 무엇인가?
- 구현이 edge case인 empty, one element, duplicate, overflow를 처리하는가?
- **24. 최단 경로**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [23. 최소 신장 트리](23-minimum-spanning-trees.md) · 다음: [25. 계산 난해성](25-computational-intractability.md)
