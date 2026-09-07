---
layout: page
title: "06. C++ 모듈화"
permalink: /studies/systems/data-structure/06-cpp-modularization/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/06%20C%2B%2B%20%EB%AA%A8%EB%93%88%ED%99%94.md)

{% raw %}
## 모듈화의 의미

모듈화는 프로그램 기능을 독립적이고 교체 가능한 부분으로 나누는 설계 방식이다. 핵심은 인터페이스와 구현을 구분하는 것이다.

| 개념 | 의미 |
|---|---|
| Declaration | 이름과 사용 방법을 소개한다. |
| Definition | 실제 구현과 표현을 완전히 지정한다. |

```cpp
double sqrt(double);       // declaration
double sqrt(double d) {    // definition
    // algorithm
}
```

## 함수

함수는 특정 작업을 수행하는 문장들의 묶음이다.

```cpp
return_type name(formal_arguments);
return_type name(formal_arguments) {
    // body
}
```

함수를 쓰면 프로그램을 작은 단위로 나눌 수 있고, 테스트, 유지보수, 협업이 쉬워진다.

## 함수 호출과 흐름

제곱근 계산 예시는 반복 계산을 `mySqrt` 함수로 분리한다.

```cpp
double mySqrt(double n) {
    double error = 1E-5;
    if (n < 0) return -1;
    double t = n;
    while (abs(t - n / t) > error) t = (t + n / t) / 2.0;
    return t;
}
```

함수는 실행 흐름을 다른 위치로 이동시키고, 작업이 끝나면 호출 지점으로 결과를 반환한다.

## 오버로딩

같은 이름의 함수라도 매개변수 타입이나 개수가 다르면 다른 함수로 취급된다.

```cpp
double phi(double x);
double phi(double x, double mu, double sigma);
```

컴파일러는 호출 인자의 타입을 보고 어떤 함수를 사용할지 결정한다.

## 값 전달과 참조 전달

| 방식 | 의미 | 호출자 변수 변경 |
|---|---|---|
| Call by value | 값을 복사해서 전달 | 변경되지 않음 |
| Call by reference | 원본에 대한 별칭 전달 | 변경될 수 있음 |

```cpp
void f(int a, int& b) {
    a *= 2;
    b *= 2;
}
```

자료구조에서는 큰 객체 복사를 피하기 위해 `const T&` 인자를 자주 쓴다.

## 템플릿

템플릿은 타입을 매개변수화해 같은 알고리즘을 여러 타입에 적용한다.

```cpp
template <class T>
T sum(T a, T b) {
    return a + b;
}
```

여러 타입 매개변수도 가능하다.

```cpp
template <class T, class U>
bool are_equal(T a, U b) {
    return a == b;
}
```

비타입 템플릿 인자는 컴파일 시간에 정해지는 값이다.

```cpp
template <class T, int N>
T fixed_multiply(T val) {
    return val * N;
}
```

## Scope

Scope는 이름이 보이는 프로그램 영역이다.

| Scope | 예 |
|---|---|
| Global scope | 전역 함수, 전역 변수 |
| Class scope | 클래스 멤버 |
| Local scope | 중괄호 내부 지역 변수 |
| Statement scope | `for`문의 제어 변수 |

복잡한 중첩 scope와 이름 가리기(name hiding)는 버그를 만들기 쉬우므로 단순하게 유지한다.

## Namespace

Namespace는 이름 충돌을 줄이기 위한 범위다.

```cpp
namespace foo {
    int value() { return 5; }
}

cout << foo::value();
```

`using namespace`는 편리하지만 넓은 범위에서 사용하면 충돌 위험이 커진다.

## 구조체

구조체는 서로 다른 타입의 변수를 하나의 이름 아래 묶는다.

```cpp
struct Vector {
    int sz;
    double* elem;
};
```

구조체 변수는 `.`으로 멤버에 접근하고, 구조체 포인터는 `->`로 접근한다.

## 클래스

클래스는 데이터와 함수를 함께 묶는다.

```cpp
class Rectangle {
    int width, height;
public:
    void set_values(int, int);
    int area() { return width * height; }
};
```

| 접근 지정자 | 의미 |
|---|---|
| `private` | 같은 클래스 내부에서만 접근 |
| `protected` | 같은 클래스와 파생 클래스에서 접근 |
| `public` | 객체가 보이는 곳에서 접근 |

클래스의 기본 접근 수준은 `private`이다.

## 생성자와 소멸자

생성자는 객체가 만들어질 때 자동 호출되고, 소멸자는 객체가 파괴될 때 자동 호출된다.

```cpp
class_name();
class_name(parameters);
~class_name();
```

동적 메모리를 가진 클래스에서는 소멸자, 복사 생성자, 대입 연산자, 이동 생성자, 이동 대입 연산자를 함께 고려해야 한다.

## 연산자 오버로딩과 클래스 템플릿

사용자 정의 타입에 `+`, `-`, `[]`, `()` 같은 연산자를 정의할 수 있다. 또한 클래스 자체도 템플릿화할 수 있다.

```cpp
template <class T>
class mypair {
    T values[2];
public:
    mypair(T first, T second);
};
```

## 함께 보면 좋은 노트

- [C++ 객체지향 프로그래밍](08-cpp-oop.md)
- [배열과 벡터](10-arrays-and-vectors.md)
- [연결 리스트](11-linked-lists.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. C++ 모듈화**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **06. C++ 모듈화**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. C++ 메모리](05-cpp-memory.md) · 다음: [07. C++ 입출력](07-cpp-io.md)
