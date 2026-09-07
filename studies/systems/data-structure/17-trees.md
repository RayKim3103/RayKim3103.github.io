---
layout: page
title: "17. 트리"
permalink: /studies/systems/data-structure/17-trees/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/17%20%ED%8A%B8%EB%A6%AC.md)

{% raw %}
## 문제 상황

Unix/Linux 파일 시스템은 디렉터리와 파일이 계층적으로 배치된다. 이런 계층 구조를 표현하는 대표 자료구조가 트리다.

## 트리 자료구조

트리는 parent-child 관계를 가진 노드들의 계층적 구조다. 하나의 root와 여러 subtree로 구성된다.

대표 응용:

- 조직도
- 족보
- 파일 시스템
- 프로그래밍 환경의 syntax/parse tree
- AI decision tree
- 컴파일러 parse tree

## 용어

| 용어 | 의미 |
|---|---|
| Root | parent가 없는 노드 |
| Internal node | child가 하나 이상 있는 노드 |
| External node / Leaf | child가 없는 노드 |
| Siblings | 같은 parent를 공유하는 노드 |
| Subtree | 한 노드와 그 descendant로 이루어진 트리 |
| Ancestors | parent, grandparent 등 위쪽 노드들 |
| Descendants | child, grandchild 등 아래쪽 노드들 |
| Depth | root에서 해당 노드까지의 edge 수 |
| Height | 트리에서 가능한 최대 depth |
| Degree of node | child 수 |
| Degree of tree | 모든 노드 degree의 최댓값 |

## Tree ADT

| 메서드 | 의미 |
|---|---|
| `root()` | root 반환 |
| `parent(v)` | 노드 `v`의 parent 반환 |
| `children(v)` | `v`의 children 반환 |
| `isRoot(v)` | root 여부 |
| `isInternal(v)` | internal node 여부 |
| `isExternal(v)` | leaf 여부 |
| `size()` | 노드 수 |
| `isEmpty()` | 빈 트리 여부 |
| `iterator()` | 저장된 element 순회 |
| `positions()` | 모든 node position 순회 |
| `replace(v, e)` | `v`의 element 교체 |

## 트리 구현

노드는 보통 다음 정보를 가진다.

- data element
- parent node link
- children node list

일반 트리는 child 수가 고정되지 않을 수 있으므로 children을 list나 vector로 저장할 수 있다.

## 이진 트리

이진 트리는 각 노드가 최대 두 child를 갖는 트리다. 각 child는 left child 또는 right child다.

응용 예:

- 수식 parse tree
- binary search tree
- heap
- decision process

수식 `(5 * (3 + 2)) - (6 * 4)`는 operator를 internal node, operand를 leaf로 하는 binary expression tree로 표현할 수 있다.

## 이진 트리 성질

강의에서는 각 internal node가 정확히 두 child를 갖는 proper/full 성격의 이진 트리 성질을 다룬다.

| 기호 | 의미 |
|---|---|
| `n` | 전체 노드 수 |
| `e` | external node 수 |
| `i` | internal node 수 |
| `h` | height |

대표 관계:

- `e = i + 1`
- `n = 2e - 1`
- `h >= log2(e)`
- `h >= log2(n + 1) - 1`

## Linked structure 구현

이진 트리 노드는 data, parent, left, right를 가진다.

```cpp
struct Node {
    Element data;
    Node* parent;
    Node* left;
    Node* right;
};
```

포인터 기반 구현은 sparse한 트리에도 공간을 효율적으로 쓸 수 있다.

## 배열 기반 구현

Complete binary tree처럼 빈자리가 적은 트리는 배열로 표현하기 좋다.

| 노드 | 배열 위치 |
|---|---|
| root | `A[1]` |
| left child | `2 * rank(parent)` |
| right child | `2 * rank(parent) + 1` |

`A[0]`은 비워두면 부모/자식 인덱스 계산이 단순해진다. 단, skewed tree에서는 빈 칸이 많아져 공간 낭비가 크다.

## Tree Traversal

Traversal은 트리의 원소들을 정해진 순서로 방문하는 방법이다.

| 순회 | 순서 | 특징 |
|---|---|---|
| Pre-order | Node, Left, Right | 노드를 descendant보다 먼저 처리 |
| Post-order | Left, Right, Node | descendant를 먼저 처리 |
| In-order | Left, Node, Right | BST에서 정렬 순서 |
| Out-order | Right, Node, Left | 내림차순 계열 |

## Pre-order

```text
preOrder(v)
    visit(v)
    for each child w of v
        preOrder(w)
```

디렉터리 구조 출력처럼 상위 항목을 먼저 보여줄 때 자연스럽다.

## Post-order

```text
postOrder(v)
    for each child w of v
        postOrder(w)
    visit(v)
```

폴더 용량 계산처럼 하위 결과를 모두 계산한 뒤 parent를 처리할 때 좋다.

## In-order

```text
inOrder(v)
    if left child exists
        inOrder(left)
    visit(v)
    if right child exists
        inOrder(right)
```

이진 탐색 트리에서는 in-order traversal이 key를 오름차순으로 방문한다.

## 트리 균형 형태

| 형태 | 의미 |
|---|---|
| Full/Perfect binary tree | 모든 internal node가 두 child를 갖고 leaf가 같은 level |
| Complete binary tree | 마지막 level을 제외하고 꽉 차며 마지막 level은 왼쪽부터 채움 |
| Skewed binary tree | 한쪽으로 긴 선형 구조 |

균형은 탐색 성능과 직결된다.

## 함께 보면 좋은 노트

- [이진 탐색 트리](18-binary-search-trees.md)
- [힙과 우선순위 큐](20-heaps-and-priority-queues.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **17. 트리**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **17. 트리**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [16. 효율적인 정렬](16-efficient-sorting.md) · 다음: [18. 이진 탐색 트리](18-binary-search-trees.md)
