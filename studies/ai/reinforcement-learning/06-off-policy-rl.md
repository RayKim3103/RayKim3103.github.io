---
layout: page
title: "06. Off-policy RL"
permalink: /studies/ai/reinforcement-learning/06-off-policy-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/06%20Off-policy%20RL.md)

{% raw %}
## 핵심 요약

Off-policy RL은 현재 학습하려는 target policy와 다른 behavior policy가 수집한 data를 사용해 학습할 수 있는 방법이다. Q-learning 계열은 본질적으로 off-policy이고, replay buffer를 통해 data reuse가 가능하다. 연속 action에서는 DDPG, TD3, SAC 같은 actor-critic 기반 off-policy 알고리즘이 중요하다.

## Off-policy의 의미

On-policy 방법은 현재 policy로 수집한 rollout을 주로 사용한다. 반면 off-policy 방법은 다른 policy가 수집한 transition도 사용할 수 있다.

```text
(s, a, r, s') ~ D
```

여기서 `D`는 replay buffer이며, data를 만든 policy는 현재 policy와 다를 수 있다.

## Fitted Q-Iteration

Q-learning target은 다음과 같다.

```text
y = r + γ maxa' Q(s',a')
```

target policy는 greedy policy로 암묵적으로 정의된다.

```text
π(s) = arg maxa Q(s,a)
```

따라서 data를 어떤 policy가 수집했는지와 별개로 greedy target을 학습할 수 있다.

## Continuous action의 어려움

연속 action space에서는 모든 action에 대해 `Q(s,a)`를 평가해 argmax를 찾기 어렵다. DDPG는 deterministic actor `μθ(s)`를 학습해 greedy action을 근사한다.

```text
μθ(s) ≈ arg maxa Qφ(s,a)
```

actor는 critic의 action gradient를 따라 업데이트된다.

```text
∇θ J ≈ Es~D [∇a Qφ(s,a)|a=μθ(s) ∇θ μθ(s)]
```

## DDPG

DDPG는 DQN의 replay buffer와 target network 아이디어를 continuous control에 적용한다.

- critic: `Qφ(s,a)`를 TD target으로 학습
- actor: `Qφ(s, μθ(s))`를 크게 만드는 방향으로 학습
- exploration: deterministic policy에 noise를 추가
- target actor와 target critic을 soft update

단점은 hyperparameter에 민감하고 Q overestimation과 exploration 문제가 있을 수 있다는 것이다.

## TD3의 관점

TD3는 DDPG의 불안정성을 줄이기 위해 다음 아이디어를 사용한다.

- twin critics: 두 Q값 중 작은 값을 target에 사용해 overestimation 감소
- delayed policy update: critic을 더 자주 업데이트하고 actor는 천천히 업데이트
- target policy smoothing: target action에 noise를 더해 critic의 sharp peak 악용을 줄임

## Maximum Entropy RL

SAC는 reward뿐 아니라 policy entropy도 최대화한다.

```text
J(π) = E[Σt γt (r(st,at) + α H(π(·|st)))]
```

entropy term은 exploration을 장려하고 여러 좋은 action을 유지한다.

## Soft Actor-Critic

SAC는 stochastic actor와 soft Q function을 사용한다.

Soft value target은 entropy를 포함한다.

```text
y = r + γ Ea'~π [Qtarget(s',a') - α log π(a'|s')]
```

actor는 다음 목적을 최적화한다.

```text
minπ E[α log π(a|s) - Q(s,a)]
```

즉 Q가 큰 action을 선택하면서도 entropy를 유지한다.

## Off-policy의 실전 장점

- rollout data를 여러 번 재사용해 sample efficiency가 높다.
- 과거 실험 data나 여러 behavior policy의 data를 활용할 수 있다.
- continuous control에서 SAC, TD3가 강력한 baseline이다.

## 주의점

- replay buffer data와 현재 policy distribution이 너무 멀어지면 학습이 불안정할 수 있다.
- Q target이 bootstrapping을 사용하므로 오차가 누적될 수 있다.
- offline RL처럼 완전히 static dataset만 쓰는 경우에는 일반 off-policy 알고리즘을 그대로 쓰면 OOD action 문제가 커진다.

## 연결 노트

- [Q-learning](05-q-learning.md)
- [RL 벤치마크](07-rl-benchmarks.md)
- [Offline RL](08-offline-rl.md)
- [Humanoid RL](16-humanoid-rl.md)

{% endraw %}

---

이전: [05. Q-learning](05-q-learning.md) · 다음: [07. RL 벤치마크](07-rl-benchmarks.md)
