---
layout: page
title: "22. 그래프"
permalink: /studies/systems/data-structure/22-graphs/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/22%20%EA%B7%B8%EB%9E%98%ED%94%84.md)

{% raw %}
## 문제 상황

쾨니히스베르크의 일곱 다리 문제는 “모든 다리를 정확히 한 번씩 건널 수 있는가?”라는 질문에서 출발한다. 땅을 vertex, 다리를 edge로 표현하면 현실 문제를 그래프로 모델링할 수 있다.

## 그래프 정의

그래프는 vertex와 edge의 집합이다.

```text
G = (V, E)
```

- `V`: 유한한 vertex 집합
- `E`: vertex 쌍으로 이루어진 edge 집합
- vertex와 edge는 element를 저장할 수 있다.

응용:

- 전자 회로
- 교통망
- 컴퓨터 네트워크
- 데이터베이스 관계

## 무방향 그래프와 방향 그래프

| 종류 | Edge 의미 |
|---|---|
| Undirected graph | `(v, w)`와 `(w, v)`를 같은 연결로 본다. |
| Directed graph | `(source, destination)` 순서가 중요하다. |

방향 그래프에서는 한쪽으로 reachable하더라도 반대 방향은 아닐 수 있다.

## Vertex와 Edge 용어

| 용어 | 의미 |
|---|---|
| Endpoint | edge가 연결하는 양끝 vertex |
| Incident edge | 특정 vertex에 연결된 edge |
| Adjacent vertices | edge로 직접 연결된 vertex들 |
| Self-loop | 같은 vertex에서 출발해 같은 vertex로 돌아오는 edge |

방향 그래프에서 `w`가 `v`에 adjacent하다는 말은 보통 `(v, w)` edge가 있음을 뜻한다.

## Degree

무방향 그래프에서 degree는 incident edge 수다.

방향 그래프에서는:

- in-degree: vertex로 들어오는 edge 수
- out-degree: vertex에서 나가는 edge 수

그래프 밀도:

| 종류 | edge 수 |
|---|---|
| Sparse graph | `|E| = O(|V|)` |
| Dense graph | `|E| = O(|V|^2)` |

## Path와 Cycle

Path는 인접 edge를 따라가는 vertex sequence다.

```text
v1, v2, ..., vn such that (vi, vi+1) in E
```

Path length는 edge 수다. 자기 자신으로 가는 path length는 0이다.

Cycle은 시작 vertex와 끝 vertex가 같은 path다. Directed graph에서는 edge 방향을 반드시 고려해야 한다.

DAG, Directed Acyclic Graph는 cycle이 없는 방향 그래프다.

## Connectivity

| 개념 | 의미 |
|---|---|
| Reachability | 한 vertex에서 다른 vertex까지 path 존재 |
| Connected graph | 무방향 그래프에서 모든 vertex 쌍이 연결 |
| Strongly connected | 방향 그래프에서 모든 vertex 쌍이 양방향 reachable |
| Weakly connected | 방향을 무시하면 연결 |
| Connected component | maximal connected subgraph |
| Complete graph | 모든 vertex 쌍 사이에 edge 존재 |

## Weighted graph

Weighted graph는 edge에 비용, 거리, 시간, hop 수 같은 숫자를 부여한다. 최소 신장 트리와 최단 경로 문제의 입력이 된다.

## Graph ADT

| 메서드 | 의미 |
|---|---|
| `endVertices(e)` | edge `e`의 양 끝 vertex |
| `opposite(v, e)` | edge `e`에서 `v`의 반대편 vertex |
| `areAdjacent(v, w)` | 인접 여부 |
| `getAdjacent(v)` | 인접 vertex 목록 |
| `insertVertex(o)` | vertex 삽입 |
| `insertEdge(v, w, o)` | edge 삽입 |
| `removeVertex(v)` | vertex와 incident edge 제거 |
| `removeEdge(e)` | edge 제거 |
| `incidentEdges(v)` | incident edge 목록 |
| `getDegree(v)` | degree 반환 |

## 인접 행렬

`|V| x |V|` matrix `A`를 사용한다.

| 그래프 | `A[i][j]` |
|---|---|
| Unweighted | 인접하면 1, 아니면 0 |
| Weighted | 인접하면 weight, 아니면 0 또는 infinity |
| Undirected | 대칭 행렬 |
| Directed | 대칭일 필요 없음 |

장점은 `areAdjacent(v, w)`가 `O(1)`이라는 점이고, 단점은 공간이 `O(|V|^2)`이라는 점이다. Dense graph에 적합하다.

## 인접 리스트

인접 리스트는 각 vertex마다 인접한 vertex 또는 edge 목록을 저장한다.

공간:

```text
O(|V| + |E|)
```

Sparse graph에 특히 적합하다.

## 표현별 성능

| 연산 | Adjacency matrix | Adjacency list |
|---|---:|---:|
| Space | `|V|^2` | `|V| + |E|` |
| `incidentEdges(v)` | `|V|` | `deg(v)` |
| `areAdjacent(v,w)` | `O(1)` | `min(deg(v), deg(w))` |
| `insertVertex` | `O(|V|)` | `O(1)` |
| `insertEdge` | `O(1)` | `O(1)` |

## Graph traversal

그래프 순회는 한 vertex에서 시작해 reachable한 vertex들을 체계적으로 방문한다. 트리와 달리 cycle이 있으므로 같은 vertex를 반복 방문하지 않도록 marked/visited가 필요하다.

## DFS

DFS는 한 경로를 끝까지 내려간 뒤 되돌아와 다른 경로를 탐색한다. 재귀 또는 스택으로 구현한다.

```text
DFS(v)
    mark v
    for each edge (v, w)
        if w is unmarked
            label (v, w) as discovery edge
            DFS(w)
        else
            label as back edge if appropriate
```

시간 복잡도는 `O(|V| + |E|)`이다.

DFS 응용:

- connected component
- spanning tree/forest
- path 찾기
- cycle 찾기
- biconnected component

## BFS

BFS는 시작점에서 가까운 level부터 차례로 방문한다. 큐 기반으로 구현한다.

```text
BFS(s)
    mark s
    enqueue s
    while queue not empty
        v = dequeue
        for each neighbor w of v
            if w is unmarked
                mark w
                enqueue w
```

시간 복잡도는 `O(|V| + |E|)`이다.

BFS의 level은 시작 vertex에서 edge 몇 개로 도달하는지를 나타낸다. Unweighted graph에서 최단 edge 수 경로를 찾을 수 있다.

## DFS vs BFS

| 문제 | DFS | BFS |
|---|---:|---:|
| Spanning forest | 가능 | 가능 |
| Connected components | 가능 | 가능 |
| Path | 가능 | 가능 |
| Cycle | 가능 | 가능 |
| 최소 edge 수 shortest path | 부적합 | 적합 |
| Biconnected components | 적합 | 일반적으로 DFS 기반 |

## 함께 보면 좋은 노트

- [스택](12-stack.md)
- [큐](13-queue.md)
- [최소 신장 트리](23-minimum-spanning-trees.md)
- [최단 경로](24-shortest-paths.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **22. 그래프**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 자료구조 주제에서는 operation별 복잡도와 구현 invariant를 함께 외워야 실제 코드에서 흔들리지 않는다.
- C++ 구현에서는 값 복사, 참조, 포인터 소유권, 예외 안전성을 코드 리뷰 기준으로 삼는다.
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
- **22. 그래프**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [21. 해싱](21-hashing.md) · 다음: [23. 최소 신장 트리](23-minimum-spanning-trees.md)
