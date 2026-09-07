---
layout: page
title: "14. 재귀"
permalink: /studies/systems/data-structure/14-recursion/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/14%20%EC%9E%AC%EA%B7%80.md)

{% raw %}
## 문제 상황: 최대공약수

두 정수 `p`, `q`의 최대공약수는 두 수를 모두 나누는 가장 큰 정수다. 분수 약분, 암호 시스템 등에서 중요하다.

## 유클리드 알고리즘

기본 성질은 다음과 같다.

```text
gcd(p, 0) = p
gcd(p, q) = gcd(q, p % q)
```

예:

```text
gcd(1885, 1595)
= gcd(1595, 290)
= gcd(290, 145)
= gcd(145, 0)
= 145
```

## 재귀 프로그램

```cpp
int gcd(int p, int q) {
    if (q == 0) return p;
    else return gcd(q, p % q);
}
```

재귀 함수는 자기 자신을 직접 또는 간접적으로 호출한다.

## 재귀의 구성 요소

| 요소 | 의미 |
|---|---|
| Base case | 더 이상 쪼개지 않고 바로 답을 낼 수 있는 경우 |
| Reduction step | 더 작은 같은 형태의 문제로 줄이는 단계 |

재귀는 수학적 귀납법과 밀접하다. 작은 입력에서 함수가 맞다고 가정하고, 그 결과를 이용해 더 큰 입력을 푼다.

## 피보나치 예시

```cpp
int fibonacci(int x) {
    if (x == 0 || x == 1) return x;
    return fibonacci(x - 1) + fibonacci(x - 2);
}
```

이 단순 재귀는 같은 부분 문제를 반복해서 계산한다.

```text
T(n) = T(n-1) + T(n-2) < 2T(n-1) = O(2^n)
```

따라서 직관적으로는 간단하지만 성능은 매우 나쁠 수 있다.

## 재귀와 반복

재귀와 반복은 표현력이 같다. 재귀는 명시적 call stack 없이도 문제 구조를 자연스럽게 표현하고, 반복은 stack overhead를 줄이거나 상태를 명시적으로 관리할 때 유리하다.

재귀 템플릿:

```text
recursive(n)
    if n == base
        return x_base
    else
        return f(n, recursive(n-1))
```

반복 템플릿:

```text
iterative(n)
    x = x_base
    for i = n downto base
        x = f(i, x)
    return x
```

## Factorial

재귀:

```cpp
int fact(int n) {
    if (n == 0 || n == 1) return 1;
    return n * fact(n - 1);
}
```

반복:

```cpp
int fact(int n) {
    int result = 1;
    for (int i = n; i >= 1; i--)
        result *= i;
    return result;
}
```

## 반복 피보나치

배열에 이전 값을 저장하면 `O(n)`에 계산할 수 있다.

```cpp
fibs[0] = 0;
fibs[1] = 1;
for (int i = 2; i < x; i++)
    fibs[i] = fibs[i-1] + fibs[i-2];
```

더 최적화하면 마지막 두 값만 저장해 `O(1)` 공간으로도 가능하다.

## Divide and Conquer

분할 정복은 문제를 같은 구조의 더 작은 문제로 나누고, 재귀적으로 풀고, 결과를 합친다.

- FFT
- Parser
- Quicksort
- Mergesort
- Recursive graphics

## 함께 보면 좋은 노트

- [스택](12-stack.md)
- [효율적인 정렬](16-efficient-sorting.md)
- [트리](17-trees.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14. 재귀**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **14. 재귀**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. 큐](13-queue.md) · 다음: [15. 기본 정렬](15-basic-sorting.md)
