---
layout: page
title: "15. 기본 정렬"
permalink: /studies/systems/data-structure/15-basic-sorting/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/15%20%EA%B8%B0%EB%B3%B8%20%EC%A0%95%EB%A0%AC.md)

## 정렬 문제

정렬 알고리즘은 리스트의 원소를 특정 순서로 재배열한다. 중복 원소 찾기, 검색 전처리, 데이터 표시 등에 기본적으로 사용된다.

강의에서는 입력 데이터가 random access 가능한 배열에 저장되어 있다고 가정한다.

## Bubble Sort

버블 정렬은 이웃한 원소를 왼쪽에서 오른쪽으로 비교하며 순서가 틀리면 교환한다. 한 pass가 끝나면 가장 큰 원소가 오른쪽 끝에 놓인다.

```cpp
void bubbleSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++)
        for (int j = 0; j < n - i - 1; j++)
            if (arr[j] > arr[j + 1])
                swap(&arr[j], &arr[j + 1]);
}
```

| 경우 | 시간 |
|---|---:|
| 최선 | `O(n)` |
| 평균 | `O(n^2)` |
| 최악 | `O(n^2)` |

최선 `O(n)`은 조기 종료 최적화가 있을 때의 해석이다. 기본 이중 반복 구현은 이미 정렬된 입력에서도 모든 비교를 수행할 수 있다.

## Selection Sort

선택 정렬은 아직 정렬되지 않은 부분에서 최솟값을 찾고, 현재 위치와 교환한다.

```cpp
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int min_idx = i;
        for (int j = i + 1; j < n; j++)
            if (arr[j] < arr[min_idx])
                min_idx = j;
        swap(&arr[min_idx], &arr[i]);
    }
}
```

| 경우 | 비교 | 교환 |
|---|---:|---:|
| 최선 | `O(n^2)` | `O(n)` |
| 평균 | `O(n^2)` | `O(n)` |
| 최악 | `O(n^2)` | `O(n)` |

비교는 항상 많이 하지만 교환 횟수가 적다.

## Insertion Sort

삽입 정렬은 왼쪽의 정렬된 구간에 새 원소를 알맞은 위치로 삽입한다.

```cpp
void insertionSort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j = j - 1;
        }
        arr[j + 1] = key;
    }
}
```

| 경우 | 시간 | 설명 |
|---|---:|---|
| 최선 | `O(n)` | 이미 오름차순이면 이동 거의 없음 |
| 평균 | `O(n^2)` | 평균적으로 많은 이동 |
| 최악 | `O(n^2)` | 내림차순이면 약 `n^2/2` 이동 |

입력이 거의 정렬되어 있을 때 강하다.

## Shell Sort

Shell sort는 삽입 정렬의 과도한 한 칸 이동 문제를 줄인다. gap `h`만큼 떨어진 원소끼리 삽입 정렬을 수행하고, 점차 gap을 줄여 마지막에 `h=1` 정렬을 한다.

```cpp
int shellSort(int arr[], int n) {
    for (int gap = n / 2; gap > 0; gap /= 2) {
        for (int i = gap; i < n; i++) {
            int temp = arr[i];
            int j;
            for (j = i; j >= gap && arr[j - gap] > temp; j -= gap)
                arr[j] = arr[j - gap];
            arr[j] = temp;
        }
    }
    return 0;
}
```

Shell sort의 시간 복잡도는 gap sequence에 의존한다.

| 항목 | 복잡도 |
|---|---|
| 일반적 최선 | `O(n log n)` 수준 |
| 알려진 나쁜 gap의 최악 | `O(n^2)` |
| 더 좋은 gap sequence의 최악 | `O(n log^2 n)` 수준 |
| 평균 | gap sequence 의존 |

## 정렬 비교

| 알고리즘 | 장점 | 약점 |
|---|---|---|
| Bubble | 개념 단순 | 실용성 낮음 |
| Selection | 교환 횟수 적음 | 비교가 항상 많음 |
| Insertion | 거의 정렬된 입력에 강함 | 역순에 약함 |
| Shell | 삽입 정렬보다 이동 감소 | 분석이 gap에 의존 |

## 함께 보면 좋은 노트

- [성능 분석](09-performance-analysis.md)
- [효율적인 정렬](16-efficient-sorting.md)



---

이전: [14. 재귀](14-recursion.md) · 다음: [16. 효율적인 정렬](16-efficient-sorting.md)
