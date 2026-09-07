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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. C++ 흐름 제어**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **04. C++ 흐름 제어**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. C++ 타입](03-cpp-types.md) · 다음: [05. C++ 메모리](05-cpp-memory.md)
