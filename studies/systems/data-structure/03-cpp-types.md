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


{% endraw %}

---

이전: [02. Linux 명령어 요약](02-linux-commands.md) · 다음: [04. C++ 흐름 제어](04-cpp-control-flow.md)
