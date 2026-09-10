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

**정책 그래디언트**: $$\pi_\theta(a\mid s)$$ 를 직접 최적화. log-derivative trick으로 reward를 미분하지 않고 sampling된 trajectory로 gradient 추정.
**Actor-Critic**: variance를 줄이려 reward-to-go·baseline·advantage·GAE 도입.
**PPO**: policy가 한 번에 크게 바뀌지 않게 clipped objective.

## 1. MDP와 목적함수

$$
\mathcal{M} = \langle S, A, P, R, \gamma \rangle,\qquad
P(s'\mid s,a),\quad R(s,a),\quad \gamma \in [0,1)
$$
Markov property: $$P(s_{t+1}\mid s_0,a_0,\dots,s_t,a_t) = P(s_{t+1}\mid s_t,a_t)$$.

$$
J(\theta) = \mathbb{E}_{\tau\sim\pi_\theta}\Big[\sum_t \gamma^t r_t\Big],\qquad
p_\theta(\tau) = p(s_0)\prod_t \pi_\theta(a_t\mid s_t)\,P(s_{t+1}\mid s_t,a_t)
$$
dynamics는 모르거나 미분 불가하지만 **policy는 $$\theta$$에 대해 미분 가능**.

## 2. Log-derivative trick → REINFORCE

$$
\nabla_\theta p_\theta(\tau) = p_\theta(\tau)\,\nabla_\theta \log p_\theta(\tau)
$$
$$
\nabla_\theta J(\theta) = \mathbb{E}_{\tau}\big[\nabla_\theta \log p_\theta(\tau)\, R(\tau)\big]
= \mathbb{E}_{\tau}\Big[\Big(\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\Big) R(\tau)\Big]
$$
(dynamics는 $$\theta$$에 무관 → 사라짐.)
$$
\nabla_\theta J(\theta) \approx \frac{1}{N}\sum_i \sum_t \nabla_\theta \log \pi_\theta(a_{i,t}\mid s_{i,t})\, R(\tau_i)
\quad\text{(REINFORCE, high variance)}
$$

**장점**: 연속 action 자연스러움, stochastic policy 직접 학습, argmax 불필요.
**단점**: sample 비효율, gradient variance 큼, local optimum, on-policy(새 rollout 자주 필요).

## 3. Variance 줄이기

### Reward-to-go (causality)
시간 $$t$$의 action은 과거 reward에 영향 없음 → $$t$$ 이후만:
$$
G_t = \sum_{t'=t}^{T} \gamma^{t'-t} r_{t'},\qquad
\nabla_\theta J \approx \mathbb{E}\Big[\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\, G_t\Big]
$$

### Baseline
state에만 의존하는 $$b(s_t)$$ 를 빼도 기댓값 불변 ($$\mathbb{E}[\nabla_\theta \log \pi_\theta(a_t\mid s_t)\, b(s_t)] = 0$$). 대표 baseline = value function $$V^\pi(s) = \mathbb{E}[G_t\mid s_t{=}s]$$.

### Advantage
$$
A^\pi(s,a) = Q^\pi(s,a) - V^\pi(s),\qquad
\nabla_\theta J \approx \mathbb{E}\Big[\sum_t \nabla_\theta \log \pi_\theta(a_t\mid s_t)\, A^\pi(s_t,a_t)\Big]
$$

### Critic + TD error
critic $$V_\phi(s)$$ 학습. advantage 근사:
$$
\delta_t = r_t + \gamma V_\phi(s_{t+1}) - V_\phi(s_t)
$$
Monte Carlo보다 variance↓, bootstrapping으로 bias↑.

### n-step advantage
$$
\hat A_t^{(n)} = \sum_{t'=t}^{t+n} \gamma^{t'-t} r_{t'} + \gamma^{n+1} V_\phi(s_{t+n+1}) - V_\phi(s_t)
$$
$$n$$↑ → Monte Carlo에 가까워 bias↓ variance↑; $$n=0$$ → $$\delta_t$$(bias↑ variance↓).

### GAE (Generalized Advantage Estimation)
$$n$$-step 추정들의 지수가중 평균:
$$
\hat A_t^{\text{GAE}} = \sum_{l=0}^{\infty} (\gamma\lambda)^l\, \delta_{t+l}
\;=\; \delta_t + \gamma\lambda\, \hat A_{t+1}^{\text{GAE}}
\quad(\text{뒤에서 앞으로 재귀})
$$
$$\lambda\to 0$$: $$\hat A_t = \delta_t$$ (bias↑ variance↓). $$\lambda\to 1$$: Monte Carlo advantage (bias↓ variance↑). $$\lambda$$ 가 bias–variance 손잡이.

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
- clip 덕분에 같은 rollout으로 **여러 epoch·minibatch** 업데이트 안전 (surrogate 없이 다중 업데이트하면 ratio가 1에서 크게 벗어나 policy collapse).
- 세부(advantage normalization, value loss coefficient, entropy bonus $$+\beta\mathcal H(\pi_\theta)$$, update epoch 수)가 성능에 중요.

## 관련 과제

- [과제 2 — Policy Gradients](hw2-policy-gradients.md): REINFORCE·reward-to-go·NN baseline·GAE·PPO 구현. 실측 — CartPole는 RTG + advantage normalization 조합이 최속 수렴, gradient 분산 $$O(1/B)$$ 로 큰 batch가 안정. HalfCheetah baseline로 return > 300. HumanoidStandup **GAE λ=0.95가 최적(≈12000)**, λ=0(≈4500)·λ=1(≈11000)은 열세. Reacher **PPO −10 vs no-PPO −70**, surrogate 제거 + 4-epoch vanilla 업데이트 → **−1.6e7 collapse**.
- [과제 4 — Soft Actor-Critic](hw4-soft-actor-critic.md): actor gradient를 REINFORCE vs reparametrize로 비교 (HalfCheetah reparam ~5000 ≫ REINFORCE-1 ~0).

## 복습 질문

- log-derivative trick으로 $$\nabla_\theta J$$ 를 유도하고, dynamics가 사라지는 이유는?
- reward-to-go / baseline / advantage / GAE 각각이 variance를 줄이는 방식은?
- $$n$$-step advantage에서 $$n$$, GAE에서 $$\lambda$$ 가 bias–variance에 주는 영향은? ($$\hat A_t = \delta_t + \gamma\lambda\hat A_{t+1}$$)
- PPO의 clipped objective가 하는 일과, surrogate 없이 다중 업데이트하면 왜 collapse하는가?
{% endraw %}

---

이전: [02. 모방학습](02-imitation-learning.md) · 다음: [04. Q-learning과 Off-policy RL](04-q-learning-and-off-policy.md)
