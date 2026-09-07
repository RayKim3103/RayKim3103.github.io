---
layout: page
title: "03. 벡터 미적분과 장 이론"
permalink: /studies/math/electromagnetics/03-vector-calculus-and-field-theory/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electromagnetics/lecture_notes/03%20%EB%B2%A1%ED%84%B0%20%EB%AF%B8%EC%A0%81%EB%B6%84%EA%B3%BC%20%EC%9E%A5%20%EC%9D%B4%EB%A1%A0.md)

{% raw %}
## 핵심 요약

벡터 미적분은 장의 공간적 변화를 정량화하는 도구이다. 이 강의는 스칼라장과 벡터장을 정의하고, 선적분ㆍ면적분ㆍ체적적분, gradient, divergence, curl, Laplacian, Gauss 정리, Stokes 정리, Helmholtz 정리를 전자기학의 언어로 연결한다.

## 스칼라장과 벡터장

스칼라장은 각 점에 하나의 숫자를 부여한다. 온도장 `T(x, y, z)`나 전위 `V(x, y, z)`가 예이다. 벡터장은 각 점에 방향과 크기를 가진 벡터를 부여한다. 열 흐름, 전기장, 자기장, 전류밀도가 예이다.

벡터장의 크기는 단위 면적을 통과하는 흐름의 세기처럼 해석할 수 있다. 면이 흐름 방향에 수직이면 기여가 최대이고, 흐름과 평행이면 플럭스가 0이 된다. 이 때문에 면적분에는 항상 `A · dS` 형태의 내적이 등장한다.

## 벡터 적분

| 적분 | 형태 | 의미 |
| --- | --- | --- |
| 선적분 | `∫C A · dl` | 경로를 따라 장의 접선 성분 누적 |
| 면적분 | `∫S A · dS` | 면을 통과하는 플럭스 |
| 체적적분 | `∫V f dV` | 영역 전체의 스칼라량 합 |

전기장 선적분은 전위차 또는 유도기전력으로 연결되고, 자기장 선적분은 앙페르 법칙으로 연결된다. 전기 플럭스는 가우스 법칙, 자기 플럭스는 패러데이 법칙과 연결된다.

## Gradient

스칼라장 `T`의 gradient는 가장 빠르게 증가하는 방향과 그 증가율을 나타낸다.

```text
dT = ∇T · dl
```

열 흐름은 온도가 가장 빨리 낮아지는 방향으로 흐르므로 `h = -k∇T`와 같이 음의 gradient가 등장한다. 전자기학에서는 정전계에서

```text
E = -∇V
```

가 핵심 관계이다. 전기장은 전위가 가장 빨리 감소하는 방향을 향한다.

## Divergence

Divergence는 아주 작은 부피에서 벡터장이 밖으로 얼마나 빠져나가는지 나타내는 스칼라량이다.

```text
∇ · A
```

물리적으로 source 또는 sink의 밀도를 의미한다. `∇ · A = 0`이면 그 벡터장은 solenoidal field이고, 닫힌 선처럼 source 없이 이어진다.

전자기학의 대표 예시는 다음과 같다.

```text
∇ · D = ρ
∇ · B = 0
```

첫 식은 전하가 전기 플럭스의 source임을 말하고, 둘째 식은 자기 단극자가 없다는 뜻이다.

## Curl

Curl은 벡터장이 한 점 주변에서 얼마나 회전하려는지 나타내는 벡터량이다.

```text
∇ × A
```

물레방아를 벡터장 안에 놓았을 때 회전하려는 경향으로 이해할 수 있다. Curl-free field는 irrotational field이고, 보존장으로 해석된다.

정전계에서는

```text
∇ × E = 0
```

이므로 전위 `V`를 정의할 수 있다. 정자계에서는

```text
∇ × H = J
```

이므로 전류가 자기장의 순환을 만든다.

## Gauss 정리와 Stokes 정리

Gauss 정리는 닫힌 면 플럭스와 부피 내부 발산을 연결한다.

```text
∮S A · dS = ∫V ∇ · A dV
```

Stokes 정리는 닫힌 경로의 순환과 그 경로가 둘러싼 면의 curl 플럭스를 연결한다.

```text
∮C A · dl = ∫S (∇ × A) · dS
```

이 두 정리는 적분형 맥스웰 방정식과 미분형 맥스웰 방정식을 서로 바꾸는 다리이다.

## Laplacian과 포텐셜 방정식

라플라시안은 gradient의 divergence이다.

```text
∇²V = ∇ · (∇V)
```

정전계에서 `E = -∇V`와 `∇ · D = ρ`를 결합하면 포아송 방정식과 라플라스 방정식이 나온다.

```text
∇²V = -ρ / ε
∇²V = 0    (전하가 없는 영역)
```

## Helmholtz 정리

Helmholtz 정리는 벡터장의 발산과 회전이 공간 전체에서 주어지면, 경계조건을 제외하고 그 벡터장이 결정된다는 내용을 담는다. 전자기학에서 중요한 이유는 맥스웰 방정식이 `E`, `B`, `D`, `H`의 divergence와 curl을 지정하기 때문이다.

## 연결 노트

- [벡터 대수와 직교 좌표계](01-vector-algebra-and-coordinates.md)
- [정전계 I - 쿨롱 법칙과 가우스 법칙](04-electrostatics-1-coulomb-and-gauss.md)
- [정자계 I - 비오-사바르와 앙페르 법칙](07-magnetostatics-1-biot-savart-and-ampere.md)
- [시간 변화장과 맥스웰 방정식](09-time-varying-fields-and-maxwell.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. 벡터 미적분과 장 이론**를 다루며, 벡터해석을 바탕으로 전기장, 자기장, 물질 경계조건, Maxwell 방정식을 연결해 전자기 현상을 설명한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 선형대수 주제에서는 계산 결과를 column space, null space, rank, projection의 기하학으로 다시 해석한다.
- 행렬 곱의 차원과 선형변환의 domain/codomain을 먼저 쓰면 수식 실수를 줄일 수 있다.
- 전자기학 문제는 대칭성과 좌표계를 먼저 정하면 적분 경로와 면을 자연스럽게 고를 수 있다.
- 경계조건은 normal/tangential 성분을 나누고 free source 존재 여부를 따져 적용한다.
- 전자기학은 field의 미분형/적분형 표현을 상황에 맞게 바꾸는 능력이 핵심이다.

### 문제 풀이 또는 구현 루틴

- 좌표계를 먼저 고르고 대칭성으로 field 방향과 변수 의존성을 줄인다.
- 미분형과 적분형 중 어떤 형태가 더 쉬운지 판단한 뒤 법칙을 적용한다.
- 경계에서는 normal/tangential component를 나누고 free charge/current 존재 여부를 표시한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 벡터 방향을 scalar 크기처럼 계산하면 부호와 성분이 쉽게 틀린다.
- 전위와 전기장은 음의 gradient 관계이므로 증가 방향을 혼동하지 않는다.
- 시간 변화장이 있으면 electrostatics/magnetostatics에서 쓰던 보존장 가정이 깨질 수 있다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 문제의 대칭성은 어떤 좌표계에서 가장 단순한가?
- source가 charge인지 current인지, free인지 bound인지 구분했는가?
- field, flux density, potential 중 무엇을 먼저 구하면 나머지가 쉬운가?
- **03. 벡터 미적분과 장 이론**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. 벡터 대수와 직교 좌표계 중복본](02-vector-algebra-and-coordinates-dup.md) · 다음: [04. 정전계 I - 쿨롱 법칙과 가우스 법칙](04-electrostatics-1-coulomb-and-gauss.md)
