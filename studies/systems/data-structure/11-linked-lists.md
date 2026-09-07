---
layout: page
title: "11. 연결 리스트"
permalink: /studies/systems/data-structure/11-linked-lists/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/11%20%EC%97%B0%EA%B2%B0%20%EB%A6%AC%EC%8A%A4%ED%8A%B8.md)

{% raw %}
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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. 연결 리스트**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **11. 연결 리스트**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. 배열과 벡터](10-arrays-and-vectors.md) · 다음: [12. 스택](12-stack.md)
