---
layout: page
title: "05. Q-learning"
permalink: /studies/ai/reinforcement-learning/05-q-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/05%20Q-learning.md)

{% raw %}
## 핵심 요약

Q-learning은 policy를 직접 미분하지 않고 state-action value `Q(s,a)`를 학습한 뒤, 가장 큰 Q값을 주는 action을 선택하는 value-based RL 방법이다. 핵심은 Bellman optimality equation을 target으로 삼아 Q함수를 반복적으로 맞추는 것이다. Deep Q-Network는 neural network로 Q함수를 근사하고 replay buffer와 target network로 학습을 안정화한다.

## Value function

State value와 action value는 다음처럼 정의한다.

```text
Vπ(s) = Eπ[Σt γt rt | s0=s]
Qπ(s,a) = Eπ[Σt γt rt | s0=s, a0=a]
```

optimal Q function은 특정 state-action에서 시작해 이후 최적 정책을 따른 return이다.

```text
Q*(s,a) = r(s,a) + γ Es' [maxa' Q*(s',a')]
```

## Policy iteration과 value iteration

정책이 주어지면 그 정책의 value를 평가할 수 있다. Q값이 주어지면 greedy policy를 만들 수 있다.

```text
π(s) = arg maxa Q(s,a)
```

Value iteration은 Bellman optimality backup을 반복해 optimal value에 접근한다.

## Q-learning update

tabular Q-learning의 기본 update는 다음이다.

```text
Q(s,a) ← Q(s,a) + α [r + γ maxa' Q(s',a') - Q(s,a)]
```

괄호 안은 TD error이다. 현재 추정값과 bootstrapped target 사이의 차이를 줄인다.

## Fitted Q-Iteration

함수근사를 사용할 때는 dataset의 transition에 대해 target을 만든 뒤 supervised regression처럼 Q함수를 fit한다.

```text
yi = ri + γ maxa' Qφ_old(s'i,a')
minφ Σi (Qφ(si,ai) - yi)^2
```

이 구조는 Q-learning을 deep learning과 연결한다.

## DQN

DQN은 image observation 같은 고차원 입력에서 Q함수를 neural network로 근사한다.

핵심 trick은 다음과 같다.

- replay buffer: 과거 transition을 저장해 mini-batch로 샘플링
- target network: target 계산용 network를 느리게 업데이트
- ε-greedy exploration: greedy action과 random action을 섞음

알고리즘 흐름:

1. 현재 Q network로 action을 선택한다.
2. environment step으로 `(s,a,r,s')`를 얻는다.
3. replay buffer에 저장한다.
4. buffer에서 mini-batch를 샘플링한다.
5. target network로 `r + γ max Qtarget(s',a')`를 계산한다.
6. Q network를 TD loss로 업데이트한다.
7. target network를 주기적으로 복사 또는 soft update한다.

## Overestimation과 Double Q-learning

`maxa Q(s,a)`는 noisy Q estimate에서 과대평가를 만들 수 있다. Double Q-learning은 action selection과 action evaluation을 분리해 overestimation을 줄인다.

```text
a* = arg maxa Qonline(s',a)
y = r + γ Qtarget(s',a*)
```

## Q-learning의 장단점

장점:

- off-policy로 data 재사용이 가능하다.
- discrete action에서 강력하다.
- replay buffer 덕분에 sample efficiency가 좋다.

단점:

- continuous action에서는 `arg maxa Q(s,a)`가 어렵다.
- bootstrapping과 function approximation이 결합되어 불안정할 수 있다.
- exploration이 단순하면 sparse reward에서 실패할 수 있다.

## 연결 노트

- [정책 그래디언트 기초](03-policy-gradient-basics.md)
- [Actor-Critic과 PPO](04-actor-critic-ppo.md)
- [Off-policy RL](06-off-policy-rl.md)
- [Offline RL](08-offline-rl.md)

{% endraw %}

---

이전: [04. Actor-Critic과 PPO](04-actor-critic-ppo.md) · 다음: [06. Off-policy RL](06-off-policy-rl.md)
