---
layout: page
title: "08. C++ 객체지향 프로그래밍"
permalink: /studies/systems/data-structure/08-cpp-oop/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/08%20C%2B%2B%20%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D.md)

{% raw %}
## 절차적 프로그래밍과 객체지향

절차적 프로그래밍은 “무엇을 어떤 순서로 수행할지”에 초점을 둔다. 객체지향 프로그래밍은 문제 세계의 사물과 개념을 타입으로 모델링하고, 각 객체가 데이터와 동작을 함께 가지게 한다.

| 관점 | 중심 |
|---|---|
| Procedural programming | 절차, 함수 호출 |
| Object-oriented programming | 객체, 타입, 상태, 메서드 |

## 캡슐화

캡슐화는 데이터와 그 데이터를 조작하는 함수를 한 클래스 안에 묶고, 내부 표현을 숨기는 원칙이다.

```cpp
class Encapsulation {
private:
    int x;
public:
    void set(int a) { x = a; }
    int get() { return x; }
};
```

내부 데이터는 `private`로 감추고, 외부에는 안전한 public API를 제공한다. 이렇게 하면 구현을 바꿔도 사용 코드를 덜 깨뜨린다.

## Friend class

`friend`는 특정 클래스나 함수가 private/protected 멤버에 접근할 수 있게 한다.

```cpp
class Square {
    friend class Rectangle;
private:
    int side;
};
```

편리하지만 캡슐화를 약화시키므로 필요한 관계에만 제한적으로 사용한다.

## 상속

상속은 기존 class의 특성을 유지하면서 새 class를 만드는 방식이다.

```cpp
class Rectangle : public Polygon { };
```

| 용어 | 의미 |
|---|---|
| Base class | 상속의 출발 클래스 |
| Derived class | base class로부터 파생된 클래스 |

파생 클래스는 생성자, 소멸자, 대입 연산자, friend, private member를 그대로 상속받는 것은 아니다.

## 상속에서 접근 지정자

상속 관계의 접근 지정자는 base class 멤버의 최대 공개 수준을 제한한다.

| Base member | `public` 상속 | `protected` 상속 | `private` 상속 |
|---|---|---|---|
| public | public | protected | private |
| protected | protected | protected | private |
| private | 직접 접근 불가 | 직접 접근 불가 | 직접 접근 불가 |

## 상속 예시

```cpp
class Polygon {
protected:
    int width, height;
public:
    void set_values(int a, int b) { width = a; height = b; }
};

class Rectangle : public Polygon {
public:
    int area() { return width * height; }
};

class Triangle : public Polygon {
public:
    int area() { return width * height / 2; }
};
```

공통 상태와 동작은 base class에 두고, 구체적 계산은 derived class에서 정의한다.

## 생성자와 소멸자 호출 순서

생성은 base class constructor가 먼저, derived class constructor가 나중에 호출된다. 소멸은 그 반대 순서다.

```cpp
Son(int a) : Mother(a) {
    // derived initialization
}
```

base class에 기본 생성자가 없으면 derived class 생성자에서 명시적으로 호출해야 한다.

## 다중 상속

C++은 하나의 class가 여러 base class를 상속할 수 있다.

```cpp
class Rectangle : public Polygon, public Output { };
```

다중 상속은 표현력이 있지만 이름 충돌과 설계 복잡도를 만들 수 있으므로 신중히 사용한다.

## 다형성

다형성은 하나의 interface로 여러 구체 타입을 다룰 수 있게 하는 성질이다.

```cpp
Rectangle rect;
Polygon* ppoly = &rect;
ppoly->set_values(4, 5);
```

base class 포인터가 derived object를 가리킬 수 있다.

## Virtual member

`virtual` 함수는 런타임에 실제 객체 타입에 맞춰 호출된다.

```cpp
class Base {
public:
    void print();
    virtual void vprint();
};
```

base pointer로 호출해도 `virtual` 함수는 derived version으로 dispatch될 수 있다.

## 추상 클래스

순수 가상 함수는 `= 0`으로 선언한다.

```cpp
class Polygon {
public:
    virtual int area() = 0;
};
```

이런 class는 직접 객체를 만들 수 없고 base class로만 사용된다.

## Runtime Type Identification

`dynamic_cast`는 관련 클래스 포인터 사이에서 런타임 타입 검사를 수행한다. 실패하면 포인터 변환에서는 `nullptr`을 반환한다.

`static_cast`는 런타임 검사를 하지 않으므로 빠르지만 잘못된 변환을 잡지 못한다.

## 자료구조와의 연결

객체지향은 자료구조 API를 숨기고 일관되게 제공하는 데 쓰인다.

- `Stack<T>`는 내부가 vector인지 linked list인지 숨길 수 있다.
- `Tree`는 node 연결 구조를 private으로 감추고 traversal API를 제공할 수 있다.
- `PriorityQueue`는 heap 구현을 숨기고 `insert`, `deleteMax`만 공개할 수 있다.

## 함께 보면 좋은 노트

- [C++ 모듈화](06-cpp-modularization.md)
- [스택](12-stack.md)
- [힙과 우선순위 큐](20-heaps-and-priority-queues.md)

{% endraw %}

---

이전: [07. C++ 입출력](07-cpp-io.md) · 다음: [09. 성능 분석](09-performance-analysis.md)
