---
layout: page
title: "04. Actor-Critic과 PPO"
permalink: /studies/ai/reinforcement-learning/04-actor-critic-ppo/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/04%20Actor-Critic%EA%B3%BC%20PPO.md)

{% raw %}
## 핵심 요약

Actor-critic은 policy를 업데이트하는 actor와 value를 추정하는 critic을 함께 학습한다. policy gradient의 높은 variance를 줄이기 위해 reward-to-go, baseline, advantage, GAE를 사용한다. PPO는 policy가 한 번에 너무 크게 바뀌지 않도록 clipped objective를 사용하는 안정적인 on-policy deep RL 알고리즘이다.

## Reward-to-go

기본 REINFORCE는 모든 action log probability에 전체 trajectory return을 곱한다. 그러나 시간 `t`의 action은 과거 reward에 영향을 줄 수 없다. 따라서 `t` 이후 reward만 사용하는 reward-to-go가 더 적절하다.

```text
Gt = Σt'=t,T γ(t'-t) rt'
∇θJ(θ) ≈ E[Σt ∇θ log πθ(at|st) Gt]
```

이는 causality를 반영하고 variance를 낮춘다.

## Baseline

정책 그래디언트에서 state에만 의존하는 baseline `b(st)`를 빼도 gradient의 기댓값은 바뀌지 않는다.

```text
E[∇θ log πθ(at|st)b(st)] = 0
```

가장 대표적인 baseline은 value function이다.

```text
Vπ(s) = E[Gt | st=s]
```

## Advantage

advantage는 어떤 action이 그 state의 평균적 행동보다 얼마나 좋은지 나타낸다.

```text
Aπ(s,a) = Qπ(s,a) - Vπ(s)
```

정책 업데이트는 return 대신 advantage를 사용한다.

```text
∇θJ(θ) ≈ E[Σt ∇θ log πθ(at|st) Aπ(st,at)]
```

## Critic과 TD error

critic은 `Vφ(s)` 또는 `Qφ(s,a)`를 근사한다. TD error는 advantage의 근사로 자주 쓰인다.

```text
δt = rt + γVφ(st+1) - Vφ(st)
```

critic을 학습하면 pure Monte Carlo보다 variance가 낮아지지만, bootstrapping 때문에 bias가 생길 수 있다.

## GAE

Generalized Advantage Estimation은 여러 step TD error를 지수적으로 섞어 bias-variance trade-off를 조절한다.

```text
At^GAE = Σl=0,∞ (γλ)^l δt+l
```

`λ`가 0에 가까우면 bias는 크고 variance는 작다. `λ`가 1에 가까우면 Monte Carlo에 가까워져 bias는 작고 variance는 커진다.

## Actor-Critic 구조

Actor-critic의 반복 구조는 다음과 같다.

1. 현재 policy로 rollout을 수집한다.
2. critic으로 value 또는 advantage를 추정한다.
3. actor를 advantage-weighted policy gradient로 업데이트한다.
4. critic을 return 또는 TD target에 맞게 업데이트한다.
5. 새 policy로 다시 data를 수집한다.

## PPO의 동기

Policy gradient는 큰 step을 밟으면 policy distribution이 급격히 변해 학습이 망가질 수 있다. PPO는 새 policy와 old policy의 확률비를 사용한다.

```text
rt(θ) = πθ(at|st) / πθ_old(at|st)
```

그리고 clipped objective를 최적화한다.

```text
LCLIP(θ) = E[min(rt(θ)At, clip(rt(θ), 1-ε, 1+ε)At)]
```

이 식은 advantage가 양수일 때 action 확률을 과도하게 키우지 못하게 하고, advantage가 음수일 때 과도하게 줄이지 못하게 한다.

## PPO의 특징

- 구현이 비교적 쉽다.
- 다양한 continuous control benchmark에서 강력하다.
- on-policy라 sample efficiency는 off-policy보다 낮을 수 있다.
- clipping, advantage normalization, value loss, entropy bonus 등 세부 구현이 성능에 중요하다.

## 실전 체크

- rollout batch가 충분히 큰가?
- advantage를 normalize했는가?
- policy update epoch 수가 너무 많아 old policy와 멀어지지 않는가?
- entropy bonus가 exploration을 유지하는가?
- value loss와 policy loss scale이 균형적인가?

## 연결 노트

- [정책 그래디언트 기초](03-policy-gradient-basics.md)
- [Q-learning](05-q-learning.md)
- [Off-policy RL](06-off-policy-rl.md)
- [Humanoid RL](16-humanoid-rl.md)

{% endraw %}

---

이전: [03. 정책 그래디언트 기초](03-policy-gradient-basics.md) · 다음: [05. Q-learning](05-q-learning.md)
