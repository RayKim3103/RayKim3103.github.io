---
layout: page
title: "16. 효율적인 정렬"
permalink: /studies/systems/data-structure/16-efficient-sorting/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/16%20%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9D%B8%20%EC%A0%95%EB%A0%AC.md)

{% raw %}
## 재귀적 정렬

효율적인 정렬의 핵심은 분할 정복이다. 문제를 같은 형태의 부분 문제로 나누고, 재귀적으로 정렬한 뒤, 결과를 합친다.

## Merge Sort

병합 정렬은 배열을 반으로 나누고, 각각을 정렬한 뒤, 두 정렬된 배열을 병합한다.

```cpp
void mergeSort(int arr[], int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m);
        mergeSort(arr, m + 1, r);
        merge(arr, l, m, r);
    }
}
```

`l + (r - l) / 2`는 `(l + r) / 2`보다 overflow에 안전하다.

## Merge

병합 단계는 두 정렬된 임시 배열 `L`, `R`에서 더 작은 원소를 차례로 원래 배열에 넣는다.

```text
merge(arr, l, m, r)
    L <- arr[l..m]
    R <- arr[m+1..r]
    while L and R are not empty
        take smaller front element
    copy remaining elements
```

## Merge Sort 복잡도

```text
T(N) = 2T(N/2) + N
```

각 level에서 병합 비용이 총 `N`이고, level 수가 `log N`이므로:

```text
T(N) = Theta(N log N)
```

| 경우 | 시간 |
|---|---:|
| 최선 | `Theta(n log n)` |
| 평균 | `Theta(n log n)` |
| 최악 | `Theta(n log n)` |

단점은 보통 `O(n)` 추가 공간이 필요하다는 점이다.

## 알고리즘과 하드웨어 비교

강의의 핵심 메시지는 “좋은 알고리즘은 더 빠른 컴퓨터보다 강하다”이다. `n^2` 정렬은 큰 입력에서 supercomputer를 써도 한계가 오지만, `n log n` 정렬은 일반 PC에서도 현실적일 수 있다.

## Quick Sort

퀵 정렬은 pivot을 고르고, pivot보다 작은 원소를 왼쪽, 큰 원소를 오른쪽으로 partition한 뒤 양쪽을 재귀적으로 정렬한다.

```cpp
int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low;
    for (int j = low; j <= high - 1; j++) {
        if (arr[j] <= pivot) {
            swap(&arr[i], &arr[j]);
            i++;
        }
    }
    swap(&arr[i], &arr[high]);
    return i;
}
```

```cpp
void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}
```

## Quick Sort 복잡도

| 경우 | 시간 | 이유 |
|---|---:|---|
| 최선 | `Theta(n log n)` | 균형 partition |
| 평균 | `Theta(n log n)` | 어느 정도 균형이면 level 수가 log n 수준 |
| 최악 | `Theta(n^2)` | 한쪽이 `n-1`, 다른 쪽이 0 또는 1로 갈라짐 |

최악 case에서는:

```text
T(n) = T(n-1) + n = Theta(n^2)
```

균형 case에서는 merge sort와 같은 recurrence가 된다.

```text
T(n) = 2T(n/2) + n = Theta(n log n)
```

## Merge Sort vs Quick Sort

| 기준 | Merge sort | Quick sort |
|---|---|---|
| 최악 시간 | `O(n log n)` | `O(n^2)` |
| 평균 시간 | `O(n log n)` | `O(n log n)` |
| 추가 공간 | 보통 `O(n)` | 재귀 stack 중심 |
| 장점 | 안정적인 성능 | 실제 평균 성능과 cache 활용이 좋음 |
| 핵심 위험 | 추가 메모리 | pivot 선택 실패 |

## 함께 보면 좋은 노트

- [재귀](14-recursion.md)
- [기본 정렬](15-basic-sorting.md)
- [힙과 우선순위 큐](20-heaps-and-priority-queues.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **16. 효율적인 정렬**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **16. 효율적인 정렬**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [15. 기본 정렬](15-basic-sorting.md) · 다음: [17. 트리](17-trees.md)
