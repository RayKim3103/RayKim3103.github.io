---
layout: page
title: "14. Hierarchical RL"
permalink: /studies/ai/reinforcement-learning/14-hierarchical-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/14%20Hierarchical%20RL.md)

{% raw %}
## 핵심 요약

Hierarchical RL은 긴 horizon task를 낮은 수준의 primitive action만으로 풀지 않고, 더 높은 수준의 skill 또는 option을 조합해 해결하려는 접근이다. 사람은 파스타를 만들 때 “채소 씻기”, “면 삶기”, “볶기” 같은 추상 행동으로 계획한다. HRL은 AI agent도 이런 abstraction을 사용하게 만드는 것이 목표이다.

## long-horizon task의 어려움

긴 task에서는 다음 문제가 커진다.

- sparse reward에서 성공까지 우연히 도달하기 어렵다.
- primitive action sequence가 너무 길다.
- credit assignment가 어렵다.
- exploration space가 기하급수적으로 커진다.
- 중간 목표와 하위 행동을 분리하지 않으면 학습이 불안정하다.

## skill과 option

skill은 일정 시간 동안 실행되는 하위 policy이다.

```text
πlow(a|s,z)
```

상위 policy는 primitive action 대신 skill을 선택한다.

```text
πhigh(z|s)
```

이 구조는 action space를 더 추상적인 decision space로 바꾸어 planning horizon을 줄인다.

## skill을 얻는 방법

강의는 여러 경로를 제시한다.

- expert data에서 skill learning
- unsupervised skill discovery
- 사람이 직접 설계한 skill
- AI 또는 사람이 만든 subtask reward
- world model 또는 skill prior를 활용

각 방법은 coverage, task relevance, 비용에서 trade-off가 있다.

## Skill Chaining

복잡한 task는 여러 skill을 순서대로 연결해야 한다.

```text
initial state -> skill 1 -> intermediate state -> skill 2 -> goal
```

좋은 skill은 종료 state가 다음 skill의 시작 조건과 잘 맞아야 한다. skill 간 transition feasibility가 중요하다.

## Goal-conditioned policy

하위 policy가 목표 `g`를 조건으로 받으면 여러 하위 task를 하나의 policy로 표현할 수 있다.

```text
π(a|s,g)
```

상위 policy는 다음 목표를 선택하고, 하위 policy는 그 목표까지 도달한다.

## Hierarchy의 장점

- exploration을 더 구조화할 수 있다.
- 긴 task를 짧은 subtask로 나눌 수 있다.
- 이전 task에서 배운 skill을 재사용할 수 있다.
- 사람의 instruction이나 계획과 연결하기 쉽다.

## 어려움

- 어떤 skill abstraction이 좋은지 정의하기 어렵다.
- 상위 policy와 하위 policy를 함께 학습하면 nonstationarity가 생긴다.
- skill duration과 termination 조건이 성능에 큰 영향을 준다.
- 잘못된 skill set은 오히려 policy를 제한한다.

## 로봇 task 관점

로봇 조작에서는 잡기, 이동, 놓기, 밀기 같은 skill을 조합해야 한다. 긴 조작 task는 reward가 sparse하고 실패 모드가 많기 때문에 HRL이 자연스럽다. 그러나 실제 시스템에서는 perception error, dynamics uncertainty, contact-rich interaction 때문에 skill transfer가 쉽지 않다.

## 연결 노트

- [Skill Discovery](13-skill-discovery.md)
- [Sim2Real Transfer](15-sim2real-transfer.md)
- [Humanoid RL](16-humanoid-rl.md)
- [강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **14. Hierarchical RL**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- value-based, policy-gradient, actor-critic 방법은 Bellman 관점과 trajectory likelihood 관점의 조합이다.
- on-policy와 off-policy의 차이는 sample을 모으는 policy와 학습하려는 policy가 같은지에서 출발한다.
- reward 설계, exploration, distribution shift가 성능과 안정성을 좌우하므로 실험 해석이 조심스럽다.

### 문제 풀이 또는 구현 루틴

- state, action, transition, reward, horizon, discount로 문제를 먼저 쪼갠다.
- 업데이트 식에서는 target, bootstrap 여부, importance sampling 여부를 표시한다.
- benchmark 결과는 평균 return뿐 아니라 variance, sample efficiency, seed sensitivity를 함께 본다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 높은 training return이 robust policy를 의미하지 않을 수 있다.
- discount factor는 장기 보상과 variance를 조절하는 설계 선택이다.
- offline RL에서는 dataset 밖 action을 과신하면 extrapolation error가 커진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 알고리즘은 어디에서 bias를 넣고 어디에서 variance를 줄이는가?
- 환경과 reward가 조금 바뀌면 policy가 어떻게 무너질 수 있는가?
- 탐험을 늘리는 선택이 sample efficiency와 안정성에 어떤 비용을 만드는가?
- **14. Hierarchical RL**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [13. Skill Discovery](13-skill-discovery.md) · 다음: [15. Sim2Real Transfer](15-sim2real-transfer.md)
