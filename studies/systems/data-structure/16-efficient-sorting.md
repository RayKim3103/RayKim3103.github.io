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

{% endraw %}

---

이전: [15. 기본 정렬](15-basic-sorting.md) · 다음: [17. 트리](17-trees.md)
