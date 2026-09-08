---
layout: page
title: "23. 최소 신장 트리"
permalink: /studies/systems/data-structure/23-minimum-spanning-trees/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/23%20%EC%B5%9C%EC%86%8C%20%EC%8B%A0%EC%9E%A5%20%ED%8A%B8%EB%A6%AC.md)

{% raw %}
## 문제 상황

여러 도시를 모두 연결하는 네트워크를 만들되, 건설 비용 합을 최소화하고 싶다. 모든 도시를 연결해야 하지만 cycle은 불필요한 비용을 만들 수 있다.

## Spanning Tree

Spanning tree는 원래 연결 그래프의 모든 vertex를 포함하는 connected subgraph이며 cycle이 없다.

성질:

- 원래 그래프가 `n`개의 vertex를 가지면 spanning tree도 `n`개의 vertex를 가진다.
- edge 수는 정확히 `n - 1`개다.
- 연결성을 유지하는 데 필요한 최소 edge 수를 가진다.

## Minimum Spanning Tree

Minimum Spanning Tree, MST는 positive edge weight를 가진 연결 그래프에서 총 weight가 최소인 spanning tree다.

MST는 네트워크 설계, 클러스터링, 회로/배선 문제 등에서 쓰인다.

## Cycle property

가정: 모든 edge weight가 서로 다르다.

Cycle property:

```text
어떤 cycle C에서 가장 무거운 edge f는 MST에 포함되지 않는다.
```

증명 직관:

MST에 `f`가 들어 있다고 가정한다. `f`를 제거하면 tree가 두 component로 나뉘고, 같은 cycle의 다른 edge `e`가 그 cut을 다시 연결한다. `e`가 더 가벼우므로 `f`를 `e`로 바꾸면 더 싼 spanning tree가 되어 모순이다.

## Cut property

Cut property:

```text
어떤 vertex subset S에 대해, S와 V-S를 잇는 edge 중 가장 가벼운 edge e는 MST에 포함된다.
```

증명 직관:

MST에 `e`가 없다고 가정한다. `e`를 추가하면 cycle이 생기고, 그 cycle에는 같은 cut을 가로지르는 다른 edge `f`가 있다. `e`가 더 가벼우므로 `f`를 제거하고 `e`를 넣으면 더 싼 spanning tree가 되어 모순이다.

## Kruskal 알고리즘

Kruskal은 edge를 weight 오름차순으로 보고, cycle을 만들지 않는 edge만 선택한다.

```text
Kruskal(G)
    T = empty
    sort edges by increasing weight
    for each edge e = (v, w)
        if adding e does not create a cycle
            add e to T
    return T
```

선택된 edge들은 중간 과정에서 여러 MST forest를 이룬다.

## Union-Find와 cycle 검사

Kruskal의 핵심 문제는 `(v, w)`를 추가할 때 cycle이 생기는지 확인하는 것이다.

Union-find는 각 connected component를 set으로 관리한다.

- `find(v) == find(w)`이면 이미 같은 component라서 edge를 추가하면 cycle이 생긴다.
- 다르면 edge를 추가하고 두 set을 `union`한다.

## Kruskal 정당성

edge `e`를 볼 때:

- 추가하면 cycle이 생긴다: `e`는 그 cycle에서 가장 무거운 edge로 볼 수 있으므로 cycle property에 의해 MST에 필요 없다.
- 추가해도 cycle이 생기지 않는다: `e`는 어떤 cut을 가로지르는 가장 싼 edge로 볼 수 있으므로 cut property에 의해 MST에 포함될 수 있다.

## Kruskal 복잡도

강의 분석:

```text
Sort: |E| log |E|
Union: |V| log |V|
Find: |E| log |V|
```

`|E| < |V|^2`이므로 `log |E| < 2 log |V|`이고 전체는:

```text
O(|E| log |V|)
```

## Prim 알고리즘

Prim은 하나의 시작 vertex에서 tree를 점점 키운다. 매 단계에서 현재 tree와 바깥 vertex를 잇는 가장 가벼운 edge를 선택한다.

```text
Prim(G, s)
    U = {s}
    T = empty
    while U != V
        choose min-weight edge (u, v) where u in U and v not in U
        add (u, v) to T
        add v to U
    return T
```

Prim은 cut property를 직접 사용하는 greedy 알고리즘이다.

## Kruskal vs Prim

| 기준 | Kruskal | Prim |
|---|---|---|
| 성장 방식 | edge 중심, forest 병합 | vertex 중심, 하나의 tree 확장 |
| 핵심 자료구조 | union-find | priority queue |
| 적합한 경우 | edge 정렬이 자연스럽고 sparse한 그래프 | 특정 시작점에서 확장, adjacency list와 heap 사용 |
| 정당성 | cycle/cut property | cut property |

## 함께 보면 좋은 노트

- [그래프](22-graphs.md)
- [힙과 우선순위 큐](20-heaps-and-priority-queues.md)
- [최단 경로](24-shortest-paths.md)

{% endraw %}

---

이전: [22. 그래프](22-graphs.md) · 다음: [24. 최단 경로](24-shortest-paths.md)
