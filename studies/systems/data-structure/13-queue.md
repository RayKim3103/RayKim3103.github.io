---
layout: page
title: "13. 큐"
permalink: /studies/systems/data-structure/13-queue/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/13%20%ED%81%90.md)

{% raw %}
## 문제 상황

카페 주문 관리는 먼저 들어온 주문을 먼저 처리해야 한다. 이런 순서를 FIFO, First-In First-Out이라고 한다.

## 큐 ADT

| 연산 | 의미 |
|---|---|
| `enqueue(val)` | tail에 원소 추가 |
| `dequeue()` | head의 원소 제거 및 반환 |
| `peek()` | 제거하지 않고 head 확인 |
| `empty()` | 비었는지 확인 |
| `size()` | 원소 수 반환 |

## 배열 기반 큐

배열 기반 큐는 고정 크기 배열과 `head`, `tail`, `size`를 둔다.

```cpp
template <class T>
class queue {
private:
    size_t length, size, head, tail;
    T* items;
};
```

생성 시 배열을 할당하고 포인터들을 0으로 초기화한다.

```cpp
queue(size_t l) {
    length = l;
    items = new T[length];
    size = 0;
    head = 0;
    tail = 0;
}
```

## Circular array

tail과 head는 배열 끝에 도달하면 다시 0으로 돌아간다.

```cpp
tail = (tail + 1) % length;
head = (head + 1) % length;
```

`head == tail`만으로는 full과 empty를 구분할 수 없으므로 `size`를 함께 관리한다.

## Enqueue

```cpp
void enqueue(const T& val) {
    if (size == length)
        throw std::out_of_range("overflow");
    items[tail] = val;
    tail = (tail + 1) % length;
    size++;
}
```

배열 큐의 enqueue는 `O(1)`이다.

## Dequeue

```cpp
T dequeue() {
    if (size == 0)
        throw std::out_of_range("underflow");
    T item = items[head];
    head = (head + 1) % length;
    size--;
    return item;
}
```

dequeue도 `O(1)`이다.

## 연결 리스트 기반 큐

강의에서는 linked list를 이용해 큐를 구현한다. enqueue와 dequeue가 서로 반대쪽 끝에서 일어나야 하므로, 어느 쪽을 head/tail로 쓸지 일관되게 정해야 한다.

```cpp
void enqueue(const T& val) {
    length++;
    items.push_front(val);
}
```

```cpp
void dequeue() {
    if (empty()) throw std::out_of_range("underflow");
    length--;
    items.pop_back();
}
```

이중 연결 리스트나 tail 삭제가 `O(1)`인 구조를 사용하면 큐 연산을 모두 상수 시간에 만들 수 있다.

## 배열 vs 연결 리스트

| 구현 | 장점 | 단점 |
|---|---|---|
| Array circular queue | 모든 연산 `O(1)`, 공간 overhead 작음 | 최대 크기 고정 |
| Linked list queue | 크기 제한이 사실상 동적 | 포인터와 동적 할당 overhead |

## 큐 응용

- 음악 재생목록
- 데이터 버퍼
- 비동기 데이터 전송
- 파일 I/O, pipe, socket
- 프린터나 processor 같은 공유 자원 요청 처리
- 콜센터, 교통, 마트 계산대 시뮬레이션
- 그래프 BFS

## 함께 보면 좋은 노트

- [연결 리스트](11-linked-lists.md)
- [스택](12-stack.md)
- [그래프](22-graphs.md)

{% endraw %}

---

이전: [12. 스택](12-stack.md) · 다음: [14. 재귀](14-recursion.md)
