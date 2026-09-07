---
layout: page
title: "15. 기본 정렬"
permalink: /studies/systems/data-structure/15-basic-sorting/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/15%20%EA%B8%B0%EB%B3%B8%20%EC%A0%95%EB%A0%AC.md)

{% raw %}
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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **15. 기본 정렬**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **15. 기본 정렬**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [14. 재귀](14-recursion.md) · 다음: [16. 효율적인 정렬](16-efficient-sorting.md)
