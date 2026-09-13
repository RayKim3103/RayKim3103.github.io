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

**숫자 예 — wraparound 추적**: `length=4`인 배열에서 시작해(`head=tail=size=0`)

| 연산 | 배열 내용 | `head` | `tail` | `size` |
|---|---|---:|---:|---:|
| `enqueue(A,B,C)` | `[A,B,C,_]` | 0 | 3 | 3 |
| `dequeue()` → A | `[A,B,C,_]` | 1 | 3 | 2 |
| `enqueue(D)` | `[A,B,C,D]` | 1 | 0 | 3 |
| `enqueue(E)` | `[E,B,C,D]` | 1 | 1 | 4 |

`enqueue(D)`에서 `tail`이 `3`에서 `(3+1)%4=0`으로 **wrap-around**하고, `enqueue(E)`는 `dequeue`로 비워진 index `0`(원래 `A` 자리)에 `E`를 덮어쓴다. 마지막에 `head==tail==1`이지만 `size==4==length`이므로 이는 empty가 아니라 **full** 상태다 — `size`가 없으면 이 두 상태를 구분할 수 없다.

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

## 복습 질문

- `length=4` circular queue에서 위 5단계를 직접 추적해 각 단계의 `head`, `tail`, `size`를 구할 수 있는가?
- `head==tail`인 상태가 empty일 수도 full일 수도 있는 이유와, `size`가 이를 어떻게 구분해주는지 설명할 수 있는가?
- enqueue와 dequeue가 서로 반대쪽 끝에서 일어나야 하는 연결 리스트 큐에서, 어느 쪽을 head/tail로 정할지가 왜 일관성 있게 고정돼야 하는가?

{% endraw %}

---

이전: [12. 스택](12-stack.md) · 다음: [14. 재귀](14-recursion.md)
