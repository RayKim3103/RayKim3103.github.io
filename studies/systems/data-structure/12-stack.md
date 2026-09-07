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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. 스택**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **12. 스택**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11. 연결 리스트](11-linked-lists.md) · 다음: [13. 큐](13-queue.md)
