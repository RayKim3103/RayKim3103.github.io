---
layout: page
title: "19. 균형 트리"
permalink: /studies/systems/data-structure/19-balanced-trees/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/19%20%EA%B7%A0%ED%98%95%20%ED%8A%B8%EB%A6%AC.md)

{% raw %}
## 핵심 목표

균형 트리는 탐색 트리의 높이를 `O(log n)`으로 유지해 search, insertion, deletion을 안정적으로 빠르게 만드는 구조다.

## 2-3-4 Tree

2-3-4 tree는 모든 internal node가 2, 3, 4개의 child 중 하나를 갖는 self-balancing tree다.

| 노드 종류 | key 수 | child 수 |
|---|---:|---:|
| 2-node | 1 | 2 |
| 3-node | 2 | 3 |
| 4-node | 3 | 4 |

모든 root-to-leaf 경로 길이가 같아 perfect balance를 유지한다.

## 2-3-4 Search

한 노드 안의 key들과 search key를 비교해 어느 구간에 속하는지 결정하고 해당 child로 내려간다.

예를 들어 key가 `[K, R]`인 노드에서:

- `x < K`이면 왼쪽 child
- `K < x < R`이면 가운데 child
- `R < x`이면 오른쪽 child

## 2-3-4 Insertion

삽입은 leaf까지 탐색한 뒤 처리한다.

| Leaf 상태 | 처리 |
|---|---|
| 2-node | key를 추가해 3-node |
| 3-node | key를 추가해 4-node |
| 4-node | 공간이 없으므로 split 필요 |

4-node split에서는 가운데 key가 parent로 올라가고 나머지 key들이 둘로 나뉜다.

## 2-3-4 Deletion

삭제 절차:

1. 삭제할 key를 찾는다.
2. 내부 노드에 있으면 더 작은 key 중 최댓값, 즉 predecessor를 찾아 swap해 leaf 삭제로 바꾼다.
3. leaf가 2-node가 아니면 key만 지운다.
4. leaf가 2-node면 adjustment가 필요하다.

## Deletion adjustment

| 상황 | 처리 |
|---|---|
| adjacent sibling이 3-node 또는 4-node | sibling과 rotation |
| sibling들이 2-node이고 parent가 3/4-node | parent key와 sibling을 fusion |
| parent가 root 2-node이고 sibling도 2-node | 세 요소를 4-node로 결합하고 tree height 감소 |

## 2-3-4 성능

| 항목 | 결과 |
|---|---|
| 최악 높이 | `log N` 수준 |
| 최선 높이 | `log4 N = 1/2 log N` 수준 |
| Search | `O(log n)` |
| Insertion | `O(log n)` |
| Deletion | `O(log n)` |

백만 개 노드에서도 높이가 대략 10-20 사이, 십억 개에서도 15-30 사이로 작다.

## Red-Black Tree

Red-black tree는 각 노드가 red 또는 black인 self-balancing BST다.

속성:

- 각 노드는 red 또는 black이다.
- root는 black이다.
- 모든 NIL leaf는 black이다.
- red node의 child는 모두 black이다.
- 어떤 노드에서 descendant NIL까지 가는 모든 경로는 같은 수의 black node를 포함한다.

이 규칙들이 height를 `O(log n)`으로 제한한다.

## 2-3-4 Tree와의 관계

Red-black tree는 2-3-4 tree를 binary tree 형태로 표현한 것과 유사하다. black node는 2-3-4 tree의 기본 key에 대응하고, red link는 같은 multi-key node 안에 묶인 key를 표현한다고 볼 수 있다.

## Red-Black Search와 Insertion

Search는 색을 무시하면 일반 BST search와 같다.

Insertion은 bottom에 새 node를 넣고, 2-3-4 tree와의 대응을 유지하도록 recoloring과 rotation을 수행한다.

대표 상황:

- 단순 red child 추가
- single rotation 필요
- double rotation 필요
- 4-node split에 대응하는 recoloring

## Red-Black 성능

| 연산 | 시간 |
|---|---:|
| Search | `O(log n)` |
| Insertion | `O(log n)` |
| Deletion | `O(log n)` |

AVL보다 균형 조건이 약해 삽입/삭제가 상대적으로 실용적이고, 많은 표준 라이브러리의 ordered map/set 구현에 활용된다.

## B-Tree

B-tree는 한 노드가 두 개보다 훨씬 많은 child를 가질 수 있는 self-balancing tree다. 2-3-4 tree를 order `M`으로 일반화한 구조로 볼 수 있다.

| 선택 | 효과 |
|---|---|
| 큰 `M` | tree level 감소, page access 감소 |
| 작은 `M` | wasted space 감소 |

주요 응용은 파일 시스템과 데이터베이스다.

## Disk/page 관점

디스크에서 page 하나를 읽는 비용은 크고, 메모리에 올라온 page 안에서 비교하는 비용은 상대적으로 작다. 따라서 B-tree는 node size를 page size에 맞춰 tree height와 page access 수를 줄인다.

최악 page access 수는 대략 `log_M N`이다.

## B+ Tree

B+ tree는 B-tree 변형으로 leaf node들이 다음 leaf를 가리키는 포인터를 가질 수 있다. 이 때문에 순차 접근이 빠르다.

응용:

- 파일 시스템 metadata indexing
- 디렉터리 저장
- 데이터베이스 index

## 함께 보면 좋은 노트

- [이진 탐색 트리](18-binary-search-trees.md)
- [해싱](21-hashing.md)
- [그래프](22-graphs.md)


{% endraw %}

---

이전: [18. 이진 탐색 트리](18-binary-search-trees.md) · 다음: [20. 힙과 우선순위 큐](20-heaps-and-priority-queues.md)
