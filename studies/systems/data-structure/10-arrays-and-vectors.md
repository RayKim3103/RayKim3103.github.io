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

{% endraw %}

---

이전: [09. 성능 분석](09-performance-analysis.md) · 다음: [11. 연결 리스트](11-linked-lists.md)
