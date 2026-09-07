---
layout: page
title: "03. C++ 타입"
permalink: /studies/systems/data-structure/03-cpp-types/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/03%20C%2B%2B%20%ED%83%80%EC%9E%85.md)

{% raw %}
## 최소 C++ 프로그램

C++ 프로그램은 `main` 함수에서 시작한다.

```cpp
int main() { }
```

`int`는 반환 타입, `main`은 함수 이름, 괄호는 인자 목록, 중괄호는 함수 본문이다. 주석은 `//` 또는 `/* ... */`로 작성한다.

## Hello World와 표준 입출력

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!\n";
}
```

`#include <iostream>`은 표준 입출력 라이브러리를 포함한다. `std::cout`은 표준 출력 스트림이며, `std::`는 이름이 표준 라이브러리 네임스페이스에 있음을 뜻한다.

`using namespace std;`를 사용하면 `std::cout` 대신 `cout`으로 쓸 수 있지만, 큰 프로그램에서는 이름 충돌을 피하기 위해 필요한 범위에서만 사용하는 편이 좋다.

## 타입의 의미

타입은 가능한 값의 집합과 그 값에 수행 가능한 연산의 집합을 정의한다.

| 분류 | 키워드/예 | 값 | 대표 연산 |
|---|---|---|---|
| Boolean | `bool` | `true`, `false` | 논리 연산 |
| Character | `char` | 문자 | 비교 |
| Integer | `int`, `short`, `long` | 정수 | 산술, 나머지 |
| Floating point | `float`, `double` | 실수 근사값 | 산술 |
| String | `string` | 문자들의 sequence | 연결, 비교 |

`string`, `vector`, `complex`는 언어 내장 타입은 아니지만 표준 라이브러리가 제공하는 사용자 정의 타입이다.

## 선언과 대입

```cpp
int a, b;
a = 1234;
b = 56;
int c = a + b;
```

- 선언: 이름과 타입을 연결한다.
- 대입: 이름이 가리키는 변수에 값을 저장한다.
- 초기화: 선언과 동시에 값을 정한다.

초기화되지 않은 지역 변수는 정의되지 않은 값을 가질 수 있으므로, 가능한 한 선언 시 초기화한다.

## Trace

Trace는 각 문장 실행 후 변수 값이 어떻게 변하는지 표로 추적하는 방법이다. 포인터나 반복문 디버깅에서 특히 유용하다.

| 문장 | `a` | `b` | `c` |
|---|---:|---:|---:|
| 선언 직후 | undefined | undefined | undefined |
| `a = 1234` | 1234 | undefined | undefined |
| `b = 56` | 1234 | 56 | undefined |
| `int c = a + b` | 1234 | 56 | 1290 |
| `a = b` | 56 | 56 | 1290 |
| `b = c` | 56 | 1290 | 1290 |

## 입력과 문자열

```cpp
string first, second;
cin >> first >> second;
string name = first + ' ' + second;
cout << "Hello, " << name << '\n';
```

`cin >>`은 공백 전까지 한 단어를 읽는다. 공백을 포함한 한 줄 전체가 필요하면 `getline`을 사용해야 한다.

## 연산 차이

| 연산 | `string` | 정수/실수 |
|---|---|---|
| `+` | 문자열 연결 | 덧셈 |
| `+=` | 뒤에 붙이기 | 증가 |
| `++` | 불가능 | 1 증가 |
| `-`, `*`, `/` | 불가능 | 산술 연산 |
| `%` | 불가능 | 정수 나머지 |

## Boolean과 흐름 제어

윤년 판정처럼 `bool`은 조건문과 논리식을 표현할 때 쓰인다.

```cpp
bool isLeapYear;
isLeapYear = (year % 4 == 0) && (year % 100 != 0);
isLeapYear = isLeapYear || (year % 400 == 0);
```

## `auto`

`auto`는 초기화식의 타입을 변수 타입으로 사용한다.

```cpp
auto x = 1;      // int
auto d = 1.2;    // double
auto sq = sqrt(2);
```

초기화식 없이 `auto`를 사용할 수는 없다. 문자열 리터럴의 타입은 `string`이 아니라 문자 배열 계열이므로, `auto s = "hi";`의 의미를 정확히 모를 때는 `string s = "hi";`가 더 명확하다.

## `const`

`const`는 값이 변하지 않는다는 약속이며 컴파일러가 이를 검사한다.

```cpp
const double pi = 3.14159;
```

함수 인터페이스에서 `const`를 적절히 사용하면 데이터를 안전하게 전달할 수 있다.

## 함께 보면 좋은 노트

- [C++ 흐름 제어](04-cpp-control-flow.md)
- [C++ 메모리](05-cpp-memory.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. C++ 타입**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **03. C++ 타입**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. Linux 명령어 요약](02-linux-commands.md) · 다음: [04. C++ 흐름 제어](04-cpp-control-flow.md)
