---
layout: page
title: "21. 해싱"
permalink: /studies/systems/data-structure/21-hashing/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/21%20%ED%95%B4%EC%8B%B1.md)

## Map ADT

Map은 key-value entry의 searchable collection이다. 같은 key를 가진 entry는 여러 개 허용하지 않는다.

응용:

- 주소록
- 학생 기록 데이터베이스
- 고객 ID 기반 포인트 조회

## Map 주요 연산

| 연산 | 의미 |
|---|---|
| `get(key)` | key가 있으면 value 반환, 없으면 null |
| `put(key, value)` | key-value 삽입, 기존 key면 value 갱신 후 old value 반환 |
| `remove(key)` | key가 있으면 제거하고 value 반환 |
| `size()` | entry 수 |
| `isEmpty()` | 빈 map 여부 |
| `entrySet()` | entry들의 iterable collection |
| `keySet()` | key들의 iterable collection |
| `values()` | value iterator |

## List-based Map

Unsorted doubly linked list로 map을 만들 수 있다.

| 연산 | 시간 | 이유 |
|---|---:|---|
| `put` | `O(1)` 가능 | 앞/뒤에 바로 삽입 |
| `get` | `O(n)` | key를 찾기 위해 순회 |
| `remove` | `O(n)` | 삭제할 key를 먼저 찾아야 함 |

작은 map이거나 삽입이 압도적으로 많고 검색/삭제가 드문 경우에만 적합하다.

## Hashing의 아이디어

Hash function `h`는 key `k`를 hash table의 index `h(k)`로 바꾼다.

```text
h(k) -> 0 ... N-1
```

단순히 `h(k) = k`로 두면 key range가 너무 클 수 있다. 따라서 다양한 key를 제한된 배열 크기에 고르게 흩뿌리는 함수가 필요하다.

## Hash function의 두 단계

```text
h(x) = h2(h1(x))
```

| 단계 | 역할 |
|---|---|
| Hash code `h1` | key를 integer로 변환 |
| Compression function `h2` | integer를 table index 범위로 압축 |

목표는 key를 겉보기에 random하게 분산시키는 것이다.

## Hash code 방법

| 방법 | 설명 | 적합한 key |
|---|---|---|
| Memory address | 객체 주소를 integer로 해석 | 일반 객체 |
| Integer cast | bit를 integer로 재해석 | 짧은 numeric key |
| Component sum | key bit를 일정 길이 조각으로 나누어 합산 | 긴 numeric key |
| Polynomial accumulation | component를 다항식처럼 누적 | string |

문자열에는 polynomial accumulation이 특히 적합하다. Horner's rule을 쓰면 `O(n)`에 계산할 수 있다.

```text
p(z) = a0 + a1 z + a2 z^2 + ... + a(n-1) z^(n-1)
```

## Compression function

| 방법 | 식 | 주의 |
|---|---|---|
| Division | `h2(y) = y mod N` | `N`은 보통 prime 선택 |
| MAD | `h2(y) = (ay + b) mod N` | `a mod N != 0`이어야 함 |

`N`을 잘못 고르면 특정 패턴 key가 같은 bucket으로 몰릴 수 있다.

## Hash table

Hash table은 key를 hash function으로 배열 index에 매핑해 `(key, value)` entry를 저장한다.

구성 요소:

- hash function
- 크기 `N`의 array
- bucket 또는 slot
- entry

## Collision

Collision은 서로 다른 key가 같은 index로 mapping되는 상황이다. 충돌은 피할 수 없으므로 해결 전략이 필요하다.

## Separate Chaining

각 index마다 linked list를 두고, 충돌한 key들을 같은 chain에 저장한다.

특징:

- array size `M`, entry 수 `N`
- load factor 또는 average chain length `alpha = N / M`
- insert는 해당 chain 앞에 넣을 수 있다.
- search는 해당 chain만 탐색한다.
- 최악은 모든 key가 같은 chain에 몰려 `O(n)`이다.

## Linear Probing

충돌하면 다음 빈 slot을 순서대로 찾는다.

```text
i, i+1, i+2, ...
```

특징:

- array size는 보통 entry 수보다 충분히 크게 둔다.
- cluster가 길어지면 성능이 나빠진다.
- 평균 cluster 길이는 load factor의 영향을 크게 받는다.

## 개선된 충돌 처리

| 방법 | 아이디어 |
|---|---|
| Two-probe hashing | 두 위치 중 짧은 chain 선택 |
| Double hashing | 두 번째 hash로 probing 간격 결정 |

Double hashing은 cluster를 줄이며, table size가 prime이면 모든 cell 탐색 가능성이 좋아진다.

## 성능

| 경우 | Search/Insert/Remove |
|---|---:|
| 최악 | `O(n)` |
| 기대 | `O(1)` |

실제로는 load factor가 100%에 가까워지지 않도록 관리해야 한다. Linear probing은 높은 load factor에서 probe 수가 급격히 증가하고, double hashing은 clustering을 더 잘 완화한다.

## Hashing vs Balanced Tree

| 기준 | Hashing | Balanced tree |
|---|---|---|
| 평균 속도 | 매우 빠름 | `O(log n)` |
| 최악 보장 | 약함 | 강함 |
| key 순서 연산 | 부적합 | 적합 |
| 구현 난점 | hash/equals 설계 | compare 설계 |

순서가 필요 없는 검색에는 hashing이 강하고, 정렬 순서나 범위 질의가 필요하면 balanced tree가 유리하다.

## 함께 보면 좋은 노트

- [균형 트리](19-balanced-trees.md)
- [성능 분석](09-performance-analysis.md)



---

이전: [20. 힙과 우선순위 큐](20-heaps-and-priority-queues.md) · 다음: [22. 그래프](22-graphs.md)
