---
layout: page
title: "06. C++ 모듈화"
permalink: /studies/systems/data-structure/06-cpp-modularization/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/06%20C%2B%2B%20%EB%AA%A8%EB%93%88%ED%99%94.md)

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



---

이전: [05. C++ 메모리](05-cpp-memory.md) · 다음: [07. C++ 입출력](07-cpp-io.md)
