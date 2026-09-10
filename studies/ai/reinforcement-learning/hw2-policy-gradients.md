---
layout: page
title: "과제 2 — Policy Gradients (REINFORCE · Baseline · GAE · PPO)"
permalink: /studies/ai/reinforcement-learning/hw2-policy-gradients/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW2](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW2) (`run_hw2.py`, `pg_agent.py`, `networks/policies.py`, `networks/critics.py`)
- CAS4160 · Homework 2 (CS285 HW2 기반)
- 관련 강의 노트: [03. 정책 그래디언트와 Actor-Critic](03-policy-gradient-and-actor-critic.md)

{% raw %}
## 개요

정책 그래디언트의 **분산 감소** 기법(reward-to-go, 할인, 신경망 baseline, GAE)과 **PPO**를 구현.

### 정책 그래디언트 유도
$$
\nabla_\theta J(\theta) = \mathbb{E}_{\tau\sim\pi_\theta}\!\left[\nabla_\theta \log \pi_\theta(\tau)\, r(\tau)\right]
\approx \frac{1}{N}\sum_{i=1}^N \left(\sum_{t=0}^{T-1}\nabla_\theta \log\pi_\theta(a_{it}\mid s_{it})\right)\!\left(\sum_{t=0}^{T-1} r(s_{it}, a_{it})\right)
$$

### 분산 감소

| 기법 | 식 |
|---|---|
| **Reward-to-go** (causality) | $$\sum_t \nabla\log\pi_\theta(a_t\mid s_t)\sum_{t'=t}^{T-1}\gamma^{t'-t}r_{t'}$$ |
| **Baseline** (불편) | $$\sum_t \nabla\log\pi_\theta(a_t\mid s_t)\,[G_t - V^\pi_\phi(s_t)]$$; $$\;\mathbb{E}[\nabla\log\pi\cdot b(s)] = 0$$ |
| **1-step TD (n=0)** | $$\delta_t = r_t + \gamma V_\phi(s_{t+1}) - V_\phi(s_t)$$ — bias↑ variance↓ |
| **n-step** | $$A_n = \sum_{t'=t}^{t+n}\gamma^{t'-t}r_{t'} + \gamma^{n}V_\phi(s_{t+n+1}) - V_\phi(s_t)$$ |
| **GAE(λ)** | $$A^{GAE}_t = \sum_{t'=t}^{T-1}(\gamma\lambda)^{t'-t}\delta_{t'}$$; 재귀 $$A^{GAE}_t = \delta_t + \gamma\lambda A^{GAE}_{t+1}$$ |

$$\lambda = 0$$ → 1-step TD($$\delta_t$$, bias 큼/variance 작음). $$\lambda = 1$$ → Monte-Carlo(bias 없음/variance 큼).

### PPO-Clip
$$
r_t(\theta) = \frac{\pi_\theta(a_t\mid s_t)}{\pi_{\theta_k}(a_t\mid s_t)},\qquad
L = \mathbb{E}\big[\min(r_t A_t,\; \operatorname{clip}(r_t, 1-\epsilon, 1+\epsilon)A_t)\big]
$$
clip이 policy가 old에서 너무 멀어지지 않게 → 같은 rollout으로 **여러 epoch·minibatch** 재사용 가능.

---

## 결과 (student report)

### Exp 1 — CartPole-v0 (100 iter, batch 1000 / 4000)
소형 batch(1000): **RTG + advantage normalization 조합이 가장 빠르게 200 수렴**. 대형 batch(4000): 전반적으로 더 안정·smooth (gradient 분산 $$O(1/B)$$). 질문 답: "normalization 없이는 REINFORCE보다 **reward-to-go**가 낫다".

### Exp 2 — HalfCheetah-v4 (n=100, b=5000, γ=0.95, lr=0.01)
| | baseline loss | Eval Return |
|---|---|---|
| No baseline | — | 학습 느리고 불안정 |
| **With baseline** (`-blr 0.01 -bgs 5`) | 1000 → ~100 수렴 | **> 300** 도달, 안정적 상승 |
| bgs=1, blr=0.001 (약화) | 높고 불안정하게 수렴 | 낮고 variance 큼 |

→ 좋은 value function(baseline)이 advantage 추정 정확도 → policy 안정성·성능을 좌우.

### Exp 3 — HumanoidStandup-v5 (GAE λ 탐색, n=50, l=3, s=128, b=2000)
| λ | 결과 |
|---|---|
| 0 | 불안정, 최종 ~4500 (bias 큼) |
| **0.95** | 가장 빠르고 **최고 ~12000** (bias–variance 최적 균형) |
| 1 | ~11000, variance 약간 증가 |

목표 성능 ≈ 1.15e4. sparse·long-horizon 환경에서 $$\lambda = 0$$(과보수) / $$\lambda = 1$$(불안정) 모두 $$\lambda = 0.95$$보다 열세.

### Exp 4 — Reacher-v4 (PPO vs baseline PG+GAE, n=100, b=5000, λ=0.97, `--n_ppo_epochs 4 --n_ppo_minibatches 4`)
| | 최종 Eval Return |
|---|---|
| **PPO** | −10 ~ −11 (빠르고 안정 수렴) |
| No PPO (PG + GAE) | −60 ~ −80 (불안정, 큰 variance) |
| No surrogate + 4 epoch vanilla update | **−1.6e7** (policy collapse) |

→ surrogate objective(clipping) 없이 같은 데이터를 여러 번 업데이트하면 ratio $$r_t$$가 1에서 크게 벗어나 **policy collapse**. PPO의 clip이 다중 epoch 재사용을 안전하게 만드는 **핵심 메커니즘**.

## 핵심 정리

- PG 분산 감소: reward-to-go(causality) → baseline(불편, $$V_\phi$$) → GAE($$\delta_t + \gamma\lambda A_{t+1}$$ 재귀, λ가 bias–variance).
- gradient 분산은 batch size에 $$O(1/B)$$ → 큰 batch가 더 안정.
- HumanoidStandup: λ=0.95가 최적(≈12000). Reacher: PPO −10 vs no-PPO −70.
- PPO surrogate 제거 + 다중 업데이트 → collapse(−1.6e7). clip이 다중 epoch 재사용의 안전장치.

## 복습 질문

- log-derivative trick으로 $$\nabla_\theta J$$를 유도하고, reward-to-go·baseline·GAE 각각이 분산을 줄이는 방식은?
- GAE에서 $$\lambda = 0$$과 $$\lambda = 1$$이 각각 무엇에 해당하며, HumanoidStandup 결과와 어떻게 연결되나?
- PPO ratio $$r_t(\theta)$$와 clip objective, 그리고 surrogate 없이 다중 업데이트하면 왜 collapse하나?
- batch size가 CartPole 결과(안정성)에 준 영향과 그 이유($$O(1/B)$$)는?
{% endraw %}

---

이전 과제: [과제 1 — Imitation Learning](hw1-imitation-learning.md) · 다음 과제: [과제 3 — Q-Learning](hw3-q-learning.md)
