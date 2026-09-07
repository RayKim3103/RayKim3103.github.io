---
layout: page
title: "24. 최단 경로"
permalink: /studies/systems/data-structure/24-shortest-paths/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/24%20%EC%B5%9C%EB%8B%A8%20%EA%B2%BD%EB%A1%9C.md)

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



---

이전: [23. 최소 신장 트리](23-minimum-spanning-trees.md) · 다음: [25. 계산 난해성](25-computational-intractability.md)
