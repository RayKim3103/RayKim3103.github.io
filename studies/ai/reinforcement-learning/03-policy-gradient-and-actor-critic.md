---
layout: page
title: "03. 정책 그래디언트와 Actor-Critic (PPO)"
permalink: /studies/ai/reinforcement-learning/03-policy-gradient-and-actor-critic/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `03(PG 기초)` + `04(Actor-Critic·PPO)` 통합·보강
- 표준 자료(CS285 · PPO 논문) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**정책 그래디언트**: $\pi_\theta(a\mid s)$ 를 직접 최적화. log-derivative trick으로 reward를 미분하지 않고 sampling된 trajectory로 gradient 추정.
**Actor-Critic**: variance를 줄이려 reward-to-go·baseline·advantage·GAE 도입.
**PPO**: policy가 한 번에 크게 바뀌지 않게 clipped objective.

## 1. MDP와 목적함수

$$
\mathcal{M} = \langle S, A, P, R, \gamma \rangle,\qquad
P(s'\mid s,a),\quad R(s,a),\quad \gamma \in [0,1)
$$
Markov property: $P(s_{t+1}\mid s_0,a_0,\dots,s_t,a_t) = P(s_{t+1}\mid s_t,a_t)$.

$$
J(\theta) = \mathbb{E}_{\tau\sim\pi_\theta}\Big[\sum_t \gamma^t r_t\Big],\qquad
p_\theta(\tau) = p(s_0)\prod_t \pi_\theta(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t)
$$
dynamics는 모르거나 미분 불가하지만 **policy는 $\theta$에 대해 미분 가능**.

## 2. Log-derivative trick → REINFORCE

$$
\nabla_\theta p_\theta(\tau) = p_\theta(\tau)\,\nabla_\theta \log p_\theta(\tau)
$$
$$
\nabla_\theta J(\theta) = \mathbb{E}_{\tau}\big[\nabla_\theta \log p_\theta(\tau)\, R(\tau)\big]
= \mathbb{E}_{\tau}\Big[\Big(\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\Big) R(\tau)\Big]
$$
(dynamics는 $\theta$에 무관 → 사라짐.)
$$
\nabla_\theta J(\theta) \approx \frac{1}{N}\sum_i \sum_t \nabla_\theta \log \pi_\theta(a_{i,t}\mid s_{i,t})\, R(\tau_i)
\quad\text{(REINFORCE, high variance)}
$$

**장점**: 연속 action 자연스러움, stochastic policy 직접 학습, argmax 불필요.
**단점**: sample 비효율, gradient variance 큼, local optimum, on-policy(새 rollout 자주 필요).

## 3. Variance 줄이기

### Reward-to-go (causality)
시간 $t$의 action은 과거 reward에 영향 없음 → $t$ 이후만:
$$
G_t = \sum_{t'=t}^{T} \gamma^{t'-t} r_{t'},\qquad
\nabla_\theta J \approx \mathbb{E}\Big[\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\, G_t\Big]
$$

### Baseline
state에만 의존하는 $b(s_t)$ 를 빼도 기댓값 불변 ($\mathbb{E}[\nabla_\theta \log \pi_\theta(a_t\mid s_t)\, b(s_t)] = 0$). 대표 baseline = value function $V^\pi(s) = \mathbb{E}[G_t\mid s_t{=}s]$.

### Advantage
$$
A^\pi(s,a) = Q^\pi(s,a) - V^\pi(s),\qquad
\nabla_\theta J \approx \mathbb{E}\Big[\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\, A^\pi(s_t,a_t)\Big]
$$

### Critic + TD error
critic $V_\phi(s)$ 학습. advantage 근사:
$$
\delta_t = r_t + \gamma V_\phi(s_{t+1}) - V_\phi(s_t)
$$
Monte Carlo보다 variance↓, bootstrapping으로 bias↑.

### GAE (Generalized Advantage Estimation)
$$
\hat A_t^{\text{GAE}} = \sum_{l=0}^{\infty} (\gamma\lambda)^l\, \delta_{t+l}
$$
$\lambda\to 0$: bias↑ variance↓. $\lambda\to 1$: Monte Carlo(bias↓ variance↑).

## 4. Actor-Critic 반복

1. 현재 policy로 rollout 수집
2. critic으로 value/advantage 추정
3. actor를 advantage-weighted PG로 업데이트
4. critic을 return/TD target으로 업데이트
5. 새 policy로 재수집

## 5. PPO

큰 step → policy 분포 급변 → 학습 붕괴. old policy와의 확률비:
$$
r_t(\theta) = \frac{\pi_\theta(a_t\mid s_t)}{\pi_{\theta_{\text{old}}}(a_t\mid s_t)}
$$
$$
L^{\text{CLIP}}(\theta) = \mathbb{E}\Big[\min\big(r_t(\theta)\hat A_t,\; \operatorname{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon)\hat A_t\big)\Big]
$$
advantage 양수 시 확률을 과도하게 키우지 못하게, 음수 시 과도하게 줄이지 못하게.

- 구현 쉬움, continuous control에서 강력. on-policy라 sample 효율은 off-policy보다 낮음.
- 세부(advantage normalization, value loss, entropy bonus, update epoch 수)가 성능에 중요.

## 복습 질문

- log-derivative trick으로 $\nabla_\theta J$ 를 유도하고, dynamics가 사라지는 이유는?
- reward-to-go / baseline / advantage / GAE 각각이 variance를 줄이는 방식은?
- TD error가 advantage를 근사할 때 bias–variance는 어떻게 되는가?
- PPO의 clipped objective가 하는 일과, on-policy의 sample 효율 한계는?
{% endraw %}

---

이전: [02. 모방학습](02-imitation-learning.md) · 다음: [04. Q-learning과 Off-policy RL](04-q-learning-and-off-policy.md)
