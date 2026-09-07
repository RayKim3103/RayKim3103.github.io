---
layout: page
title: "18. 이진 탐색 트리"
permalink: /studies/systems/data-structure/18-binary-search-trees/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/18%20%EC%9D%B4%EC%A7%84%20%ED%83%90%EC%83%89%20%ED%8A%B8%EB%A6%AC.md)

{% raw %}
## 문제 상황

트리에서 데이터를 빠르게 찾으려면 저장 순서가 필요하다. 이진 탐색 트리는 왼쪽에는 작은 key, 오른쪽에는 큰 key를 둔다.

## BST 정의

Binary Search Tree는 symmetric order를 만족하는 이진 트리다.

각 노드의 key에 대해:

- 왼쪽 subtree의 모든 key는 현재 key보다 작다.
- 오른쪽 subtree의 모든 key는 현재 key보다 크다.
- 두 subtree도 각각 BST다.

BST를 in-order traversal하면 key가 증가하는 순서로 방문된다.

## Search

```text
search(key, node)
    if node == NULL
        return NULL
    if node.key == key
        return node
    else if key < node.key
        return search(key, node.left)
    else
        return search(key, node.right)
```

루트에서 시작해 비교 결과에 따라 왼쪽 또는 오른쪽으로 내려간다.

## Insertion

삽입은 탐색과 거의 같다. key가 이미 있으면 value를 갱신하고, 없으면 탐색이 끝난 leaf 위치에 새 노드를 만든다.

```text
insert(node, key, value)
    if node == NULL
        node = new Node(key, value)
    else if key == node.key
        node.value = value
    else if key < node.key
        insert(node.left, key, value)
    else
        insert(node.right, key, value)
```

## Deletion

삭제는 세 경우로 나뉜다.

| 경우 | 처리 |
|---|---|
| Leaf node | null로 대체 |
| Child가 하나 | 그 child로 대체 |
| Child가 둘 | 오른쪽 subtree의 최소 key 또는 왼쪽 subtree의 최대 key로 대체 |

강의 코드는 두 child가 있을 때 오른쪽 subtree의 최소값을 현재 노드로 가져오고, 그 최소 노드를 다시 삭제한다.

```cpp
if (t->left != NULL && t->right != NULL) {
    t->element = findMin(t->right)->element;
    remove(t->element, t->right);
} else {
    Node<T>* oldNode = t;
    t = (t->left != NULL) ? t->left : t->right;
    delete oldNode;
}
```

## BST 성능

BST의 search, insert, remove는 모두 height `h`에 비례한다.

| 트리 형태 | 높이 | 연산 시간 |
|---|---:|---:|
| Balanced | `O(log n)` | `O(log n)` |
| Skewed | `O(n)` | `O(n)` |

입력 순서가 이미 정렬되어 있으면 BST가 list처럼 기울어질 수 있다. 따라서 균형 유지가 중요하다.

## Self-balancing BST

Self-balancing BST는 트리가 선형으로 망가지지 않도록 구조를 조정한다.

대표 예:

- AVL tree
- Red-black tree

## AVL Tree

AVL tree는 모든 internal node에서 양쪽 child의 높이 차이가 최대 1인 BST다.

Balance factor:

```text
BF(node) = height(node.right) - height(node.left)
```

허용되는 값은 보통 `-1`, `0`, `1`이다.

AVL tree의 높이는 `O(log n)`이다. 최소 노드 수가 피보나치식으로 증가하므로 높이가 로그 수준으로 제한된다.

## AVL Search

AVL search는 일반 BST search와 같다. 차이는 트리가 균형을 유지하므로 높이가 `O(log n)`이라는 점이다.

## AVL Insertion과 Deletion

삽입과 삭제도 기본 동작은 BST와 같지만, 높이가 변한 뒤 AVL property를 위반할 수 있다. 따라서 영향을 받은 노드에서 root 방향으로 올라가며 height를 갱신하고 불균형을 회전으로 고친다.

## AVL 회전 네 가지

| 경우 | 조건 | 처리 |
|---|---|---|
| LL | `z`의 왼쪽 child `y` 쪽이 무겁고, `y`도 왼쪽 계열 | Right rotation |
| RR | `z`의 오른쪽 child `y` 쪽이 무겁고, `y`도 오른쪽 계열 | Left rotation |
| LR | 왼쪽 child가 오른쪽으로 무거움 | Left rotation on `y`, then right rotation on `z` |
| RL | 오른쪽 child가 왼쪽으로 무거움 | Right rotation on `y`, then left rotation on `z` |

회전 한 번은 `O(1)`이다.

## AVL 성능

| 연산 | 시간 |
|---|---:|
| Search | `O(log n)` |
| Insertion | `O(log n)` |
| Deletion | `O(log n)` |
| 단일 restructuring | `O(1)` |

삽입/삭제에서 위로 올라가며 height를 확인하는 총 비용이 `O(log n)`이다.

## 함께 보면 좋은 노트

- [트리](17-trees.md)
- [균형 트리](19-balanced-trees.md)
- [해싱](21-hashing.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **18. 이진 탐색 트리**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **18. 이진 탐색 트리**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [17. 트리](17-trees.md) · 다음: [19. 균형 트리](19-balanced-trees.md)
