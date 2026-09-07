---
layout: page
title: "05. C++ 메모리"
permalink: /studies/systems/data-structure/05-cpp-memory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/05%20C%2B%2B%20%EB%A9%94%EB%AA%A8%EB%A6%AC.md)

{% raw %}
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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. C++ 메모리**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **05. C++ 메모리**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. C++ 흐름 제어](04-cpp-control-flow.md) · 다음: [06. C++ 모듈화](06-cpp-modularization.md)
