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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **13. 큐**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **13. 큐**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [12. 스택](12-stack.md) · 다음: [14. 재귀](14-recursion.md)
