---
layout: page
title: "04. C++ 흐름 제어"
permalink: /studies/systems/data-structure/04-cpp-control-flow/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/04%20C%2B%2B%20%ED%9D%90%EB%A6%84%20%EC%A0%9C%EC%96%B4.md)

{% raw %}
## Control Flow

Control flow는 프로그램에서 실제 실행되는 문장의 순서다. 순차 실행만 있으면 단순하지만, 조건문과 반복문이 들어오면 실행 경로가 입력값에 따라 달라진다.

## `if` 문

```cpp
if (condition) {
    statementA;
} else {
    statementB;
}
```

조건식은 `bool`로 평가된다. 참이면 `if` 블록, 거짓이면 `else` 블록이 실행된다. `else`는 선택 사항이다.

동전 던지기 예시는 난수의 홀짝으로 분기한다.

```cpp
srand(time(NULL));
if ((rand() % 2) == 1) cout << "Heads!\n";
else cout << "Tails!\n";
```

## `switch` 문

`switch`는 하나의 값을 여러 상수 case와 비교한다.

```cpp
switch (answer) {
case 'y':
    return true;
case 'n':
    return false;
default:
    cout << "I'll take that for a no.\n";
    return false;
}
```

`break` 또는 `return`이 없으면 다음 case로 실행이 이어질 수 있다. case 상수들은 서로 달라야 한다.

## `while` 문

`while`은 조건을 먼저 검사하고 반복한다.

```cpp
while (i <= n) {
    cout << v << endl;
    i = i + 1;
    v = 2 * v;
}
```

조건이 처음부터 거짓이면 본문은 한 번도 실행되지 않는다.

## Newton-Raphson 제곱근 계산

제곱근 계산 예시는 반복문을 수치 알고리즘에 적용하는 방식이다.

```cpp
double error = 1E-5;
t = n;
while (abs(t - n / t) > error) {
    t = (t + n / t) / 2.0;
}
```

현재 추정값 `t`와 `n/t`의 평균을 새 추정값으로 삼아 원하는 오차 이하가 될 때까지 반복한다.

## `do-while` 문

`do-while`은 본문을 먼저 실행하고 조건을 검사한다.

```cpp
do {
    t = (t + t2) / 2.0;
    t2 = n / t;
} while (abs(t - t2) > error);
```

최소 한 번은 실행해야 하는 입력 검증이나 반복 계산에 적합하다.

## `for` 문

`for`는 초기화, 조건, 증가식을 한 곳에 묶는다.

```cpp
for (int i = 0, v = 1; i <= n; i = i + 1) {
    cout << v << endl;
    v = 2 * v;
}
```

반복 횟수가 명확할 때 `while`보다 읽기 쉽다.

## 중첩 조건문과 반복문

복잡한 시뮬레이션은 반복문 안의 반복문과 조건문으로 구성된다. 도박사의 파산 문제에서는 여러 trial을 돌리고, 각 trial 안에서 목표 금액이나 파산에 도달할 때까지 동전 던지기를 반복한다.

```cpp
for (int i = 0; i < trials; i++) {
    int t = stake;
    while (t > 0 && t < goal) {
        if (rand() % 2 == 1) t++;
        else t--;
    }
    if (t == goal) wins++;
}
```

이 예시는 Monte Carlo simulation의 기본 구조를 보여준다.

## 오류의 종류

| 오류 | 의미 | 발견 방법 |
|---|---|---|
| Syntax error | 문법적으로 잘못된 프로그램 | 컴파일러 메시지 |
| Semantic error | 실행은 되지만 의미가 틀린 프로그램 | 테스트, trace |
| Performance error | 답은 맞지만 지나치게 느린 프로그램 | 시간 측정, 병목 분석 |

자료구조 과제에서는 semantic error와 performance error가 특히 중요하다.

## Assertion

`assert`는 “이 조건은 반드시 참이어야 한다”는 가정을 코드로 적는 방법이다.

```cpp
#include <cassert>
assert(2 + 2 == 4);
```

조건이 거짓이면 프로그램이 중단된다. `NDEBUG`가 정의되어 있으면 assert는 비활성화된다.

## Exception

Exception은 예외 상황에서 제어 흐름을 handler로 이동시키는 방식이다.

```cpp
double division(int a, int b) {
    if (b == 0) throw "Division by zero condition!";
    return a / b;
}

try {
    z = division(x, y);
} catch (const char* msg) {
    cerr << msg << endl;
}
```

스택, 큐처럼 비어 있는 자료구조에서 `pop`을 시도하는 경우 `underflow` 예외를 던지는 식으로 응용된다.

## 함께 보면 좋은 노트

- [C++ 타입](03-cpp-types.md)
- [C++ 입출력](07-cpp-io.md)
- [스택](12-stack.md)
- [큐](13-queue.md)


{% endraw %}

---

이전: [03. C++ 타입](03-cpp-types.md) · 다음: [05. C++ 메모리](05-cpp-memory.md)
