---
layout: page
title: "10. 배열과 벡터"
permalink: /studies/systems/data-structure/10-arrays-and-vectors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/10%20%EB%B0%B0%EC%97%B4%EA%B3%BC%20%EB%B2%A1%ED%84%B0.md)

{% raw %}
## 문제 상황

카페 고객 관리 프로그램을 생각한다. 고객 기록은 `(이름, 포인트)` 쌍이며, 새 고객 추가, 포인트 갱신, 탈퇴 처리가 필요하다. 이때 데이터를 어떤 구조에 저장하느냐에 따라 연산 비용이 달라진다.

## 배열

배열은 같은 타입의 원소가 엄격한 선형 순서로 저장되는 고정 크기 컨테이너다.

```cpp
Customer customers[5];
customers[0] = Tom;
customers[1] = Jane;
```

## 배열의 장점

| 연산 | 시간 | 이유 |
|---|---:|---|
| 인덱스 접근 | `O(1)` | 주소 계산으로 바로 접근 |
| 인덱스 위치 갱신 | `O(1)` | 해당 위치만 수정 |
| 인덱스 위치 삭제 표시 | `O(1)` | 값을 null/empty로 표시 가능 |

배열은 원소 수가 고정되어 있고, 인덱스를 알고 있을 때 매우 빠르다.

## 배열의 한계

배열의 크기는 선언 또는 할당 시 정해진다. 저장해야 할 데이터가 배열 크기를 넘으면 새 원소를 넣을 공간이 없다.

또한 중간 삽입과 삭제를 “빈칸 없이 순서 유지” 방식으로 처리하려면 많은 원소를 이동해야 한다.

## 벡터

`std::vector`는 크기가 바뀔 수 있는 배열 기반 sequence container다.

```cpp
std::vector<Customer> vCustomers;
vCustomers.push_back(Tom);
vCustomers.push_back(Jane);
```

내부적으로 동적 배열을 사용하고, 미래의 증가를 대비해 실제 원소 수보다 큰 capacity를 잡을 수 있다.

## 벡터 주요 연산

| 연산 | 의미 | 시간 |
|---|---|---:|
| `push_back` | 끝에 추가 | amortized `O(1)` |
| `pop_back` | 끝 원소 제거 | `O(1)` |
| `operator[]` | 인덱스 접근/갱신 | `O(1)` |
| `insert` | 지정 위치 앞에 삽입 | 최악 `O(n)` |
| `erase` | 지정 위치 또는 범위 삭제 | 최악 `O(n)` |

중간 삽입/삭제가 `O(n)`인 이유는 뒤쪽 원소들을 이동해야 하기 때문이다.

## 내부 배열 재할당

벡터가 꽉 찬 상태에서 `push_back`을 하면 더 큰 배열을 새로 만들고 기존 원소를 복사 또는 이동한다.

```text
replaceArray(A, newSize)
    S <- new array of newSize
    for i <- 0 to n-1
        S[i] <- A[i]
    A <- S
```

재할당은 비싸지만 매번 발생하지 않는다.

## 증가 전략

| 전략 | 재할당 방식 | n번 추가 총 시간 | 추가 1회 amortized |
|---|---|---:|---:|
| Incremental | capacity를 상수만큼 증가 | `O(n^2)` | `O(n)` |
| Doubling | capacity를 2배로 증가 | `O(n)` | `O(1)` |

벡터가 보통 doubling 계열 전략을 쓰기 때문에 `push_back`은 amortized constant time으로 본다.

## 배열 vs 벡터

| 기준 | 배열 | 벡터 |
|---|---|---|
| 크기 | 고정 | 동적 증가 |
| 인덱스 접근 | `O(1)` | `O(1)` |
| 끝 삽입 | 공간 있으면 `O(1)` | amortized `O(1)` |
| 중간 삽입/삭제 | `O(n)` 가능 | `O(n)` |
| 메모리 관리 | 직접 고려 | 표준 라이브러리가 관리 |

## 함께 보면 좋은 노트

- [C++ 메모리](05-cpp-memory.md)
- [연결 리스트](11-linked-lists.md)
- [성능 분석](09-performance-analysis.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. 배열과 벡터**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
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
- **10. 배열과 벡터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. 성능 분석](09-performance-analysis.md) · 다음: [11. 연결 리스트](11-linked-lists.md)
