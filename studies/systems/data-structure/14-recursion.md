---
layout: page
title: "14. 재귀"
permalink: /studies/systems/data-structure/14-recursion/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/14%20%EC%9E%AC%EA%B7%80.md)

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



---

이전: [13. 큐](13-queue.md) · 다음: [15. 기본 정렬](15-basic-sorting.md)
