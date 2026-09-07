---
layout: page
title: "12. 스택"
permalink: /studies/systems/data-structure/12-stack/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/12%20%EC%8A%A4%ED%83%9D.md)

{% raw %}
## 문제 상황

웹 브라우저의 뒤로가기 버튼은 가장 최근에 방문한 페이지를 먼저 보여준다. 최근에 들어온 데이터가 먼저 나가는 구조가 필요하다.

## 스택 ADT

스택은 LIFO, Last-In First-Out 구조다.

| 연산 | 의미 |
|---|---|
| `push(val)` | top에 원소 추가 |
| `pop()` | 가장 최근 원소 제거 및 반환 |
| `peek()` | 제거하지 않고 top 확인 |
| `empty()` | 비었는지 검사 |
| `size()` | 원소 수 반환 |

## 벡터 기반 구현

```cpp
template <class T>
class stack {
private:
    size_t top;
    vector<T> items;
};
```

`top`은 다음에 삽입될 위치 또는 현재 원소 수로 해석할 수 있다.

```cpp
void push(const T& val) {
    items.push_back(val);
    top++;
}
```

```cpp
T pop() {
    if (empty()) throw std::out_of_range("underflow");
    top--;
    T item = items.back();
    items.pop_back();
    return item;
}
```

강의 코드의 핵심 의도는 `top`을 감소시키고 그 위치의 원소를 반환하는 것이다. 실제 C++에서는 제거한 위치에 대한 참조를 반환하지 않도록 주의한다.

## 연결 리스트 기반 구현

스택의 top을 리스트의 head로 두면 삽입과 삭제가 모두 `O(1)`이다.

```cpp
void push(const T& val) {
    length++;
    Node<T>* newNode = new Node<T>(val);
    newNode->next = items.head;
    items.head = newNode;
}
```

```cpp
void pop() {
    if (empty()) throw std::out_of_range("underflow");
    length--;
    Node<T>* node = items.head;
    items.head = node->next;
    delete node;
}
```

## 벡터 vs 연결 리스트

| 구현 | 장점 | 단점 |
|---|---|---|
| Vector | 모든 연산 amortized `O(1)`, 공간 효율 좋음, cache 친화적 | 재할당 순간 비용 발생 |
| Linked list | 모든 주요 연산 worst-case `O(1)` | 포인터 저장 공간, 동적 할당 overhead |

클라이언트는 같은 stack API를 쓰므로 내부 구현은 교체 가능하다.

## 스택 응용

- 컴파일러의 parsing
- 가상 머신 실행 스택
- 워드프로세서 undo
- 웹 브라우저 back button
- PostScript 같은 stack 기반 언어
- 함수 호출 구현
- 재귀를 반복문으로 바꿀 때 explicit stack 사용

## 함수 호출과 스택

함수 호출 시 지역 환경과 return address가 runtime stack에 push되고, 함수가 끝나면 pop된다. 재귀 함수는 자신을 다시 호출하므로 stack frame이 여러 겹 쌓인다.

```cpp
int fib(int x) {
    if (x == 1 || x == 0) return x;
    return fib(x - 1) + fib(x - 2);
}
```

명시적 스택을 쓰면 많은 재귀를 반복 구조로 바꿀 수 있다.

## 함께 보면 좋은 노트

- [연결 리스트](11-linked-lists.md)
- [재귀](14-recursion.md)
- [그래프](22-graphs.md)


{% endraw %}

---

이전: [11. 연결 리스트](11-linked-lists.md) · 다음: [13. 큐](13-queue.md)
