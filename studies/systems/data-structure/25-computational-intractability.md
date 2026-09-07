---
layout: page
title: "25. 계산 난해성"
permalink: /studies/systems/data-structure/25-computational-intractability/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/25%20%EA%B3%84%EC%82%B0%20%EB%82%9C%ED%95%B4%EC%84%B1.md)

## 실용적인 알고리즘의 기준

강의는 “어떤 알고리즘이 실제로 유용한가?”라는 질문에서 시작한다. 보통 모든 입력에 대해 polynomial time에 실행되는 알고리즘을 실용적 기준으로 삼는다.

```text
Polynomial time = O(n^a)
```

반대로 `O(n!)` 같은 비다항 시간은 입력이 조금만 커져도 계산이 불가능해진다. 강의 예시는 `1000!`이 우주의 전자 수, 초당 명령 수, 우주 나이를 모두 곱한 규모보다도 훨씬 커질 수 있음을 보여준다.

## 풀 수 있는 문제와 알고리즘

문제에 대해 알고 싶은 것은 두 가지다.

- 실제로 polynomial-time algorithm이 있는가?
- 없다면 단순히 아직 못 찾은 것인가, 근본적으로 어려운 것인가?

예시:

| 문제 | 설명 | 알려진 polynomial algorithm |
|---|---|---|
| LSOLVE | 선형 방정식 시스템의 해 찾기 | Gaussian elimination `O(n^3)` |
| LP | 선형 부등식 시스템의 해 찾기 | Ellipsoid algorithm 등 |
| ILP | 선형 부등식 시스템의 binary solution 찾기 | 알려진 다항 알고리즘 없음 |

## Search Problem

Search problem은 instance `I`가 주어졌을 때 solution `S`를 찾는 문제다.

중요 조건:

```text
제안된 S가 정말 I의 해인지 polynomial time에 검사할 수 있어야 한다.
```

예:

- LSOLVE: 해를 방정식에 대입해 확인한다.
- LP: 해를 부등식에 대입해 확인한다.
- ILP: binary 조건과 부등식을 확인한다.
- FACTOR: 제안된 factor로 나누어 확인한다.

## NP

NP는 solution을 polynomial time에 검증할 수 있는 문제들의 집합이다.

동치적 설명:

- deterministic Turing machine으로 polynomial time에 검증 가능
- nondeterministic Turing machine으로 polynomial time에 해결 가능

비결정성은 원하는 답을 “맞게 추측”할 수 있는 계산 모델로 이해할 수 있다.

## P

P는 deterministic computation으로 polynomial time에 풀 수 있는 문제들의 집합이다.

관계:

```text
P subset NP
```

P 문제는 답을 찾을 수 있으므로 당연히 검증도 할 수 있다.

## P vs NP

핵심 질문:

```text
P = NP ?
```

즉, polynomial time에 검증할 수 있는 모든 해를 polynomial time에 찾을 수도 있는가?

이는 아직 해결되지 않은 대표적인 이론 컴퓨터과학 문제다.

## Intractable

강의에서는 P에 속하지 않는 search problem을 intractable하다고 부른다. 직관적으로는 모든 입력에 대해 효율적으로 풀 수 있는 알고리즘이 없는 문제다.

## Reduction

Reduction은 문제의 어려움을 비교하는 도구다.

```text
X reduces to Y
```

의미:

- Y를 푸는 subroutine이 있으면 X도 polynomial overhead로 풀 수 있다.
- Y는 적어도 X만큼 어렵다.
- Y에 polynomial algorithm이 있으면 X에도 있다.
- X에 polynomial algorithm이 없으면 Y에도 없다.

## Reduction 예시: LSOLVE to LP

등식 `Ax = b`는 두 부등식으로 바꿀 수 있다.

```text
Ax <= b
Ax >= b
```

따라서 선형 방정식 문제를 선형 부등식 문제로 변환할 수 있고, LSOLVE는 LP로 reduce된다.

## 3-SAT

3-SAT 문제:

```text
k개의 clause와 n개의 boolean variable로 된 CNF formula F가 주어졌을 때,
F를 참으로 만드는 truth assignment를 찾는다.
```

CNF는 clause들의 AND이고, 각 clause는 literal들의 OR이다. 3-SAT에서는 clause마다 3개의 literal을 가진다.

## Cook-Levin 정리

Cook-Levin theorem:

```text
모든 NP 문제는 3-SAT으로 reduce된다.
```

즉 3-SAT은 NP 안에서 가장 어려운 문제 계열의 대표다.

## NP-Complete

NP-complete 문제는 다음을 만족한다.

1. 그 문제는 NP에 속한다.
2. NP의 모든 문제가 그 문제로 reduce된다.

3-SAT은 NP-complete의 대표 예다. 3-SAT을 polynomial time에 풀면 NP의 모든 문제를 polynomial time에 풀 수 있다.

Karp의 21개 NP-complete 문제에는 3-COLOR, VERTEX COVER, EXACT COVER, SUBSET-SUM, PARTITION, KNAPSACK, BIN-PACKING, CLIQUE, HAM-CYCLE, TSP 등이 포함된다.

## NP-Hard

NP-hard는 NP의 가장 어려운 문제들만큼 어렵다는 뜻이다.

중요한 차이:

- NP-complete는 NP에 속해야 한다.
- NP-hard는 반드시 NP에 속할 필요가 없다.

즉 NP-hard 문제는 solution 검증이 polynomial time이라는 조건을 만족하지 않을 수도 있다.

## 문제 분류 지도

```mermaid
graph TD
  P[P: polynomial time에 해결 가능] --> NP[NP: polynomial time에 검증 가능]
  NPC[NP-Complete: NP 안의 가장 어려운 문제] --> NP
  NPH[NP-Hard: NP-Complete 이상으로 어려움] --> NPC
```

## 함께 보면 좋은 노트

- [성능 분석](09-performance-analysis.md)
- [그래프](22-graphs.md)
- [최단 경로](24-shortest-paths.md)



---

이전: [24. 최단 경로](24-shortest-paths.md)
