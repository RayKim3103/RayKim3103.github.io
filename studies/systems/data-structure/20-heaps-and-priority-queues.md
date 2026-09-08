---
layout: page
title: "20. 힙과 우선순위 큐"
permalink: /studies/systems/data-structure/20-heaps-and-priority-queues/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/20%20%ED%9E%99%EA%B3%BC%20%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84%20%ED%81%90.md)

{% raw %}
## 문제 상황

경로 탐색, 이벤트 시뮬레이션, 운영체제 스케줄링처럼 “가장 우선순위가 높은 항목”을 반복해서 선택해야 하는 문제가 있다. 이를 위한 ADT가 priority queue다.

## Priority Queue ADT

각 entry는 `(key, value)` 쌍이며, key가 priority 역할을 한다.

| 연산 | 의미 |
|---|---|
| `insert(k, x)` | priority key `k`와 값 `x` 삽입 |
| `deleteMax()` / `deleteMin()` | 최고/최저 priority entry 제거 및 반환 |
| `max()` / `min()` | 제거하지 않고 최고/최저 priority 확인 |
| `size()` | entry 수 |
| `isEmpty()` | 비었는지 확인 |

강의에서는 기본적으로 key가 클수록 priority가 높다고 가정한다.

## 구현별 성능

| 구현 | insert | deleteMax | max |
|---|---:|---:|---:|
| Unsorted array/list | `O(1)` | `O(n)` | `O(n)` |
| Sorted array/list | `O(n)` | `O(1)` | `O(1)` |
| BST | 최악 `O(n)` | 최악 `O(n)` | 최악 `O(n)` |
| AVL tree | `O(log n)` | `O(log n)` | `O(log n)` |
| Binary heap | `O(log n)` | `O(log n)` | `O(1)` |

Binary heap은 priority queue에 매우 잘 맞는 절충점이다.

## Binary Heap

Binary heap은 heap-ordered complete binary tree다.

조건:

- Complete binary tree: 마지막 level을 제외하고 가득 차며 마지막 level은 왼쪽부터 채운다.
- Heap order:
  - Max heap: parent key가 child key 이상
  - Min heap: parent key가 child key 이하

Heap의 높이는 `O(log n)`이다.

## 배열 표현

Complete binary tree이므로 포인터 없이 배열로 표현할 수 있다.

강의 배열 표현:

| 위치 | 인덱스 |
|---|---|
| root | `heap[0]` |
| left child of `i` | `2i + 1` |
| right child of `i` | `2i + 2` |
| parent of `i` | `floor((i - 1) / 2)` |

필요한 상태는 배열 `heap`과 현재 원소 수 `heapSize`다.

## Insertion

절차:

1. 새 원소를 배열 끝에 넣는다.
2. heap order가 깨지면 parent와 교환하며 위로 올린다.

```text
insert(x)
    heap[heapSize] = x
    i = heapSize
    heapSize++
    while i > 0 and heap[parent(i)] < heap[i]
        swap(heap[parent(i)], heap[i])
        i = parent(i)
```

시간은 height에 비례하므로 `O(log n)`이다.

## DeleteMax

절차:

1. root를 제거한다.
2. 마지막 노드를 root로 옮긴다.
3. 더 큰 child와 교환하며 아래로 내린다.

```text
deleteMax()
    max = heap[0]
    heap[0] = heap[heapSize - 1]
    heapSize--
    shiftDown(0)
    return max
```

시간은 `O(log n)`이다.

## Heapify

무작위 배열을 heap으로 바꾸려면 아래쪽 subtree부터 root 방향으로 heapify한다.

```text
for i = lastInternalNode downto 0
    shiftDown(i)
```

각 노드의 최대 이동 거리 합을 세면 전체 heapify는 `O(n)`이다. 모든 노드가 `log n`만큼 이동하는 것이 아니기 때문이다.

## Heap Sort

힙 정렬은 max heap을 이용해 큰 값을 뒤쪽부터 확정한다.

절차:

1. 배열을 heap으로 만든다.
2. root의 최댓값과 heap의 마지막 값을 교환한다.
3. 마지막 값은 정렬 완료 영역으로 제외한다.
4. 남은 heap을 heapify한다.
5. 반복한다.

## Heap Sort 성능

| 항목 | 결과 |
|---|---|
| 비교 횟수 | 최대 `2N log N` 수준 |
| 시간 | `O(n log n)` 최악 보장 |
| 추가 공간 | in-place 가능 |

의미:

- Merge sort는 `O(n log n)`이지만 보통 추가 배열이 필요하다.
- Quick sort는 평균적으로 빠르지만 최악 `O(n^2)`이다.
- Heap sort는 최악 시간과 공간 면에서 강하지만, 내부 loop가 길고 cache locality가 quick sort보다 떨어질 수 있다.

## 함께 보면 좋은 노트

- [트리](17-trees.md)
- [효율적인 정렬](16-efficient-sorting.md)
- [최단 경로](24-shortest-paths.md)

{% endraw %}

---

이전: [19. 균형 트리](19-balanced-trees.md) · 다음: [21. 해싱](21-hashing.md)
