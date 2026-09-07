---
layout: page
title: "05. C++ 메모리"
permalink: /studies/systems/data-structure/05-cpp-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/05%20C%2B%2B%20%EB%A9%94%EB%AA%A8%EB%A6%AC.md)

## 메모리 관점

컴퓨터 메모리는 주소가 붙은 byte 배열로 볼 수 있다. 코드, 전역 데이터, 힙, 스택 등이 메모리의 서로 다른 영역에 배치된다.

변수는 식별자로 접근할 수 있는 메모리 위치다. 운영체제와 실행 환경이 실제 주소를 정하지만, C++에서는 주소를 직접 다루는 포인터를 사용할 수 있다.

## 포인터

포인터는 다른 변수의 주소를 저장하는 변수다.

```cpp
int var = 7;
int* pVar = &var;

cout << pVar;   // var의 주소
cout << *pVar;  // var의 값 7
cout << &pVar;  // 포인터 변수 자체의 주소
```

| 연산자 | 의미 |
|---|---|
| `&x` | 변수 `x`의 주소 |
| `*p` | 포인터 `p`가 가리키는 대상 |

포인터 값을 바꾸는 것과 포인터가 가리키는 값을 바꾸는 것은 다르다.

```cpp
p1 = &v1;
*p1 = 10;  // v1 값 변경
p1 = p2;   // p1이 v2를 가리키게 변경
*p1 = 20;  // v2 값 변경
```

## 이중 포인터, void 포인터, null 포인터

```cpp
int a = 5;
int* b = &a;
int** c = &b;
```

`c`는 포인터 `b`의 주소를 저장하며, `**c`는 최종적으로 `a` 값을 의미한다.

`void*`는 타입 정보 없이 주소만 저장한다. 사용할 때는 적절한 타입으로 캐스팅해야 하므로 안전성이 낮다.

```cpp
int* p = nullptr;
```

`nullptr`은 아무것도 가리키지 않는 포인터를 명시한다.

## 참조

참조는 이미 존재하는 객체의 다른 이름이다.

```cpp
int A = 5;
int& rA = A;
rA = 10;  // A도 10이 된다.
```

참조는 null이 될 수 없고, 선언 시 반드시 초기화되어야 하며, 한 번 연결되면 다른 객체를 참조하도록 바꿀 수 없다. 함수 인자 전달에서 복사를 줄이거나 값을 직접 수정할 때 자주 사용한다.

## 배열

배열은 같은 타입 값들의 고정 길이 indexed sequence다.

```cpp
int foo[5] = {6, 2, 7, 4, 9};
int matrix[3][2] = {{6, 2}, {7, 4}, {9, 3}};
```

배열 이름은 많은 상황에서 첫 번째 원소를 가리키는 포인터처럼 동작한다.

```cpp
int a[10];
int* pA = a;      // &a[0]와 같은 의미
a[5] = 0;
*(pA + 5) = 0;
```

## 포인터 산술

포인터에 `+1`을 하면 주소가 1 byte 증가하는 것이 아니라, 가리키는 타입의 크기만큼 이동한다.

| 타입 | `p++` 이동 |
|---|---:|
| `char*` | 1 byte |
| `short*` | 2 bytes |
| `int*` | 보통 4 bytes |

배열 순회에서 포인터 산술은 강력하지만, 범위를 벗어나면 정의되지 않은 동작이 발생한다.

## 동적 할당

실행 중 필요한 크기가 정해지는 메모리는 `new`로 할당하고 `delete`로 해제한다.

```cpp
int* p = new int[5];
delete[] p;
```

배열로 할당했으면 반드시 `delete[]`를 써야 한다. `new (nothrow)`는 할당 실패 시 예외 대신 `nullptr`을 반환하도록 할 수 있다.

## 포인터와 `const`

```cpp
int* p1 = &x;              // 비상수 int를 가리키는 비상수 포인터
const int* p2 = &x;        // 값을 수정할 수 없음
int* const p4 = &x;        // 포인터 자체를 바꿀 수 없음
const int* const p5 = &x;  // 둘 다 바꿀 수 없음
```

읽는 방향의 인터페이스에는 `const`를 붙여 의도치 않은 수정을 막는 것이 좋다.

## 함수 포인터

함수도 메모리에 있으므로 포인터로 가리킬 수 있다.

```cpp
int operation(int x, int y, int (*pF)(int, int)) {
    return (*pF)(x, y);
}
```

정렬 비교 함수, 콜백, 전략 선택 등에 사용할 수 있다.

## x86-64 Linux 메모리 배치

| 영역 | 내용 |
|---|---|
| Stack | 지역 변수, 함수 호출 정보 |
| Heap | `malloc`, `calloc`, `new`로 동적 할당한 데이터 |
| Data | 전역 변수, static 변수, 문자열 상수 |
| Text / Shared libraries | 실행 기계어, 읽기 전용 코드 |

스택은 보통 크기 제한이 있으므로 큰 배열은 힙에 할당하는 편이 안전하다.

## 함께 보면 좋은 노트

- [C++ 모듈화](06-cpp-modularization.md)
- [연결 리스트](11-linked-lists.md)
- [트리](17-trees.md)



---

이전: [04. C++ 흐름 제어](04-cpp-control-flow.md) · 다음: [06. C++ 모듈화](06-cpp-modularization.md)
