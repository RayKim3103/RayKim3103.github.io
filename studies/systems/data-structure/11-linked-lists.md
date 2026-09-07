---
layout: page
title: "11. 연결 리스트"
permalink: /studies/systems/data-structure/11-linked-lists/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/11%20%EC%97%B0%EA%B2%B0%20%EB%A6%AC%EC%8A%A4%ED%8A%B8.md)

## 등장 배경

벡터는 내부 배열을 사용하기 때문에 크기 증가 시 재할당이 발생하고, 중간 삽입/삭제 시 원소 이동 비용이 든다. 이 overhead의 핵심 원인은 데이터 자체를 복사하거나 이동해야 한다는 점이다.

연결 리스트는 포인터를 바꾸어 순서를 재구성한다.

## 연결 리스트

연결 리스트는 노드들의 sequence로 구성된다. 각 노드는 데이터와 다른 노드로 가는 link를 가진다.

특징은 다음과 같다.

- 전체 구조를 재할당하지 않고 삽입/삭제할 수 있다.
- 원소들이 메모리에 연속적으로 있을 필요가 없다.
- 인덱스로 즉시 접근하는 random access는 지원하지 않는다.

## 단일 연결 리스트

단일 연결 리스트의 각 노드는 데이터와 다음 노드 포인터를 가진다.

```cpp
template <class T>
struct Node {
    Node(const T& d) : data(d), next(nullptr) {}
    T data;
    Node<T>* next;
};
```

보통 `head`는 첫 노드, `tail`은 마지막 노드를 가리킨다.

## 단일 연결 리스트 삽입

### Head 삽입

```cpp
void push_front(const T& val) {
    Node<T>* newNode = new Node<T>(val);
    newNode->next = head;
    head = newNode;
    if (newNode->next == nullptr) tail = newNode;
}
```

과정은 새 노드를 만들고, 새 노드가 기존 첫 노드를 가리키게 한 뒤, `head`를 새 노드로 바꾸는 것이다. 시간은 `O(1)`이다.

### Tail 삽입

```cpp
void push_back(const T& val) {
    Node<T>* newNode = new Node<T>(val);
    if (tail == nullptr) {
        head = newNode;
        tail = newNode;
        return;
    }
    tail->next = newNode;
    tail = newNode;
}
```

`tail` 포인터가 있으면 끝 삽입도 `O(1)`이다.

## 단일 연결 리스트 삭제

### Head 삭제

```cpp
void pop_front() {
    if (head) {
        Node<T>* node = head;
        head = head->next;
        if (head == nullptr) tail = nullptr;
        delete node;
    }
}
```

첫 노드만 제거하면 되므로 `O(1)`이다.

### Tail 삭제

단일 연결 리스트에서는 마지막 바로 이전 노드를 찾기 위해 처음부터 순회해야 한다.

```cpp
while (node->next != tail) node = node->next;
tail = node;
delete tail->next;
tail->next = nullptr;
```

따라서 tail 삭제는 `O(n)`이다.

## 이중 연결 리스트

이중 연결 리스트의 노드는 이전 노드와 다음 노드 포인터를 모두 가진다.

```cpp
template <class T>
struct Node {
    T data;
    Node<T>* prev;
    Node<T>* next;
};
```

이전 방향으로도 이동할 수 있기 때문에 tail 삭제를 `O(1)`에 처리할 수 있다.

## 이중 연결 리스트 삽입

```cpp
void push_front(const T& val) {
    Node<T>* newNode = new Node<T>(val);
    newNode->prev = nullptr;
    newNode->next = head;
    if (head != nullptr) head->prev = newNode;
    head = newNode;
    if (newNode->next == nullptr) tail = newNode;
}
```

```cpp
void push_back(const T& val) {
    Node<T>* newNode = new Node<T>(val);
    newNode->prev = tail;
    newNode->next = nullptr;
    if (tail != nullptr) tail->next = newNode;
    tail = newNode;
    if (newNode->prev == nullptr) head = newNode;
}
```

## 이중 연결 리스트 삭제

```cpp
void pop_front() {
    if (head) {
        Node<T>* node = head;
        head = head->next;
        if (head != nullptr) head->prev = nullptr;
        else tail = nullptr;
        delete node;
    }
}
```

```cpp
void pop_back() {
    if (tail) {
        Node<T>* node = tail;
        tail = tail->prev;
        if (tail != nullptr) tail->next = nullptr;
        else head = nullptr;
        delete node;
    }
}
```

## 단일 vs 이중 연결 리스트

| 기준 | 단일 연결 리스트 | 이중 연결 리스트 |
|---|---|---|
| 노드 link | `next` | `prev`, `next` |
| head 삽입/삭제 | `O(1)` | `O(1)` |
| tail 삽입 | `O(1)` with tail | `O(1)` |
| tail 삭제 | `O(n)` | `O(1)` |
| 메모리 | 적음 | 포인터 하나 더 필요 |
| 구현 복잡도 | 낮음 | 높음 |

## 구현 실수 포인트

- 빈 리스트에서 `head`, `tail`을 모두 갱신해야 한다.
- 마지막 원소 삭제 후 `head = tail = nullptr` 상태가 되어야 한다.
- 동적 할당한 노드는 반드시 `delete`해야 한다.
- 포인터를 바꾸는 순서를 잘못 잡으면 기존 노드에 접근할 길을 잃는다.

## 함께 보면 좋은 노트

- [C++ 메모리](05-cpp-memory.md)
- [배열과 벡터](10-arrays-and-vectors.md)
- [스택](12-stack.md)
- [큐](13-queue.md)



---

이전: [10. 배열과 벡터](10-arrays-and-vectors.md) · 다음: [12. 스택](12-stack.md)
