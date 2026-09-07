---
layout: page
title: "09. 성능 분석"
permalink: /studies/systems/data-structure/09-performance-analysis/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/09%20%EC%84%B1%EB%8A%A5%20%EB%B6%84%EC%84%9D.md)

## 왜 성능을 분석하는가

같은 문제도 알고리즘에 따라 입력이 커질수록 실행 시간이 크게 달라진다. 선택 정렬과 병합 정렬 예시는 “작은 입력에서는 비슷해 보여도 큰 입력에서는 성장률이 결정적”이라는 점을 보여준다.

성능 분석의 목적은 다음과 같다.

- 실행 시간과 메모리 사용량을 예측한다.
- 여러 알고리즘을 공정하게 비교한다.
- 최악, 최선, 평균 성능에 대한 보장을 제공한다.
- 자료구조 선택의 이론적 근거를 만든다.

## 실험적 분석

실제 프로그램을 실행해 입력 크기 `N`에 따른 시간을 측정한다.

```cpp
gettimeofday(&tv1, NULL);
selectionSort(array, size);
gettimeofday(&tv2, NULL);
```

측정값을 그래프로 그리면 성장 패턴을 관찰할 수 있다. log-log scale에서 기울기가 2에 가까우면 대략 `N^2` 성장으로 해석할 수 있다.

## 이론적 분석

이론적 분석은 machine, compiler, 현재 부하의 영향을 줄이고 알고리즘 자체의 구조를 본다. 보통 pseudocode를 기준으로 각 연산의 실행 횟수를 입력 크기의 함수로 세고, 낮은 차수 항과 상수는 버린다.

선택 정렬의 핵심 반복은 다음과 같다.

```text
for i = 0 to n - 1
    min_idx = i
    for j = i + 1 to n
        if A[j] < A[min_idx]
            min_idx = j
    swap(A[min_idx], A[i])
```

내부 비교 횟수는 `(n-1) + (n-2) + ... + 1 = n(n-1)/2`이므로 성장률은 `Theta(n^2)`이다.

## 공간 복잡도

공간 복잡도는 알고리즘 실행에 필요한 메모리 양이다.

```text
Total space = Input space + Auxiliary space
```

대형 문제에서는 실행 시간보다 메모리가 먼저 병목이 될 수 있다. 예를 들어 병합 정렬은 빠르지만 보통 선형 추가 배열을 사용한다.

## 시간 복잡도

시간 복잡도는 입력 크기에 따라 실행 시간이 어떻게 증가하는지 나타낸다. 실제 시간은 연산 비용과 연산 빈도의 곱으로 볼 수 있다.

```text
Total running time = sum(cost_i * frequency_i)
```

그러나 자료구조 과목에서는 대개 정확한 nanosecond보다 성장률을 중시한다.

## 대표 성장 함수

| 성장률 | 예 | 직관 |
|---|---|---|
| `O(1)` | 배열 인덱스 접근 | 입력 크기와 무관 |
| `O(log n)` | 이진 탐색 | 매번 절반으로 줄임 |
| `O(n)` | 선형 탐색 | 모든 원소 한 번 확인 |
| `O(n log n)` | 병합 정렬 | 각 level에서 n 작업, level은 log n |
| `O(n^2)` | 중첩 반복문, 버블 정렬 | 모든 쌍 수준 비교 |
| `O(n^3)` | 단순 행렬 곱 | 삼중 반복 |
| `O(2^n)`, `O(n!)` | 완전 탐색 일부 | 빠르게 비현실적 |

## Big-O, Big-Omega, Big-Theta

| 표기 | 의미 | 자주 쓰는 해석 |
|---|---|---|
| `O(f(n))` | 상한 | 이보다 더 나쁘게 커지지는 않음 |
| `Omega(f(n))` | 하한 | 적어도 이 정도는 걸림 |
| `Theta(f(n))` | 상하한 일치 | 정확한 성장률 분류 |

실무와 과제에서는 “최악 시간 `O(...)`”처럼 Big-O를 가장 자주 쓴다.

## 상수와 낮은 차수 항 제거

```text
4n^3 + 2n + 3 = O(n^3)
7n = O(n)
```

입력이 충분히 커지면 최고차항이 지배하고, 상수 계수는 machine이나 구현 차이에 묻히기 쉽다.

## 정렬 예시

| 알고리즘 | 최선 | 평균 | 최악 | 특징 |
|---|---:|---:|---:|---|
| Selection sort | `Theta(n^2)` | `Theta(n^2)` | `Theta(n^2)` | 교환 횟수 적음 |
| Merge sort | `Theta(n log n)` | `Theta(n log n)` | `Theta(n log n)` | 안정적, 추가 공간 |
| Quick sort | `Theta(n log n)` | `Theta(n log n)` | `Theta(n^2)` | 평균적으로 빠름 |

## 함께 보면 좋은 노트

- [기본 정렬](15-basic-sorting.md)
- [효율적인 정렬](16-efficient-sorting.md)
- [계산 난해성](25-computational-intractability.md)



---

이전: [08. C++ 객체지향 프로그래밍](08-cpp-oop.md) · 다음: [10. 배열과 벡터](10-arrays-and-vectors.md)
