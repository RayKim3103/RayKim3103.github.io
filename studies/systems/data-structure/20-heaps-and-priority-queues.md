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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **20. 힙과 우선순위 큐**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **20. 힙과 우선순위 큐**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [19. 균형 트리](19-balanced-trees.md) · 다음: [21. 해싱](21-hashing.md)
