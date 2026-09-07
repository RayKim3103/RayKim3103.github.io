---
layout: page
title: "03. 정책 그래디언트 기초"
permalink: /studies/ai/reinforcement-learning/03-policy-gradient-basics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/03%20%EC%A0%95%EC%B1%85%20%EA%B7%B8%EB%9E%98%EB%94%94%EC%96%B8%ED%8A%B8%20%EA%B8%B0%EC%B4%88.md)

{% raw %}
## 핵심 요약

정책 그래디언트는 policy `πθ(a|s)`를 직접 최적화하는 방법이다. RL 문제를 MDP로 정식화하고, trajectory return의 기댓값을 최대화하도록 parameter `θ`를 업데이트한다. 핵심 수식은 log-derivative trick을 사용해 reward를 직접 미분하지 않고 sampling된 trajectory로 gradient를 추정하는 것이다.

## MDP

강화학습 환경은 Markov Decision Process로 표현한다.

```text
M = <S, A, P, R, γ>
```

| 기호 | 의미 |
| --- | --- |
| `S` | state space |
| `A` | action space |
| `P(s'|s,a)` | transition dynamics |
| `R(s,a)` | reward function |
| `γ` | discount factor |

Markov property는 현재 state가 미래 예측에 필요한 모든 정보를 담는다는 뜻이다.

```text
P(st+1 | s0,a0,...,st,at) = P(st+1 | st,at)
```

## 목적함수

policy의 목표는 누적 reward의 기댓값을 최대화하는 것이다.

```text
J(θ) = Eτ~πθ [Σt γt rt]
```

trajectory 확률은 policy와 environment dynamics의 곱으로 표현된다.

```text
pθ(τ) = p(s0) Πt πθ(at|st)P(st+1|st,at)
```

환경 dynamics는 보통 미분할 수 없거나 모르지만, policy는 우리가 parameterize했으므로 `θ`에 대해 미분 가능하다.

## Log-derivative trick

정책 그래디언트의 핵심은 다음 항등식이다.

```text
∇θ pθ(τ) = pθ(τ) ∇θ log pθ(τ)
```

이를 목적함수에 적용하면

```text
∇θJ(θ) = Eτ~πθ [∇θ log pθ(τ) R(τ)]
```

이고 dynamics는 `θ`에 의존하지 않으므로

```text
∇θ log pθ(τ) = Σt ∇θ log πθ(at|st)
```

가 된다.

## REINFORCE 추정량

Monte Carlo sample trajectory를 사용하면

```text
∇θJ(θ) ≈ (1/N)Σi Σt ∇θ log πθ(ai,t|si,t) R(τi)
```

로 gradient를 추정한다. 이 방식은 단순하고 policy를 직접 개선하지만 variance가 크다.

## Discount factor

discount factor `γ`는 미래 reward의 현재 가치를 줄이는 역할을 한다.

```text
Gt = rt + γrt+1 + γ2rt+2 + ...
```

`γ`가 1에 가까우면 먼 미래 reward까지 고려하고, 작으면 단기 reward를 더 중시한다.

## 정책 그래디언트의 장단점

장점:

- 연속 action space에 자연스럽다.
- stochastic policy를 직접 학습할 수 있다.
- policy 자체를 최적화하므로 argmax over action이 필요 없다.

단점:

- sample efficiency가 낮다.
- gradient variance가 크다.
- local optimum에 빠질 수 있다.
- on-policy 성격이 강해 새 rollout이 자주 필요하다.

## variance 문제

정책 그래디언트는 sampling 기반 추정량이라 noisy하다. 이후 actor-critic에서는 reward-to-go, baseline, advantage, value function을 사용해 variance를 줄인다.

## 연결 노트

- [강화학습 개요](01-rl-overview.md)
- [Actor-Critic과 PPO](04-actor-critic-ppo.md)
- [Q-learning](05-q-learning.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. 정책 그래디언트 기초**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- RL 알고리즘은 update target, rollout policy, bootstrap 여부, exploration 방법을 표로 비교하면 구조가 잡힌다.
- return curve는 seed별 분산과 environment stochasticity를 함께 확인해야 한다.
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
- **03. 정책 그래디언트 기초**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [02. 모방학습](02-imitation-learning.md) · 다음: [04. Actor-Critic과 PPO](04-actor-critic-ppo.md)
