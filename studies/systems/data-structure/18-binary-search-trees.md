---
layout: page
title: "18. 이진 탐색 트리"
permalink: /studies/systems/data-structure/18-binary-search-trees/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/18%20%EC%9D%B4%EC%A7%84%20%ED%83%90%EC%83%89%20%ED%8A%B8%EB%A6%AC.md)

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



---

이전: [17. 트리](17-trees.md) · 다음: [19. 균형 트리](19-balanced-trees.md)
