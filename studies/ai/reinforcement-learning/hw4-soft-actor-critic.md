---
layout: page
title: "과제 4 — Soft Actor-Critic (Entropy · REINFORCE vs Reparametrize · Clipped Double-Q)"
permalink: /studies/ai/reinforcement-learning/hw4-soft-actor-critic/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW4](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW4) (`run_hw4.py`, `agents/sac_agent.py`, `networks/state_action_value_critic.py`, `env_configs/sac_config.py`)
- CAS4160 · Homework 4 (CS285 HW3-part2 / SAC 기반)
- 관련 강의 노트: [03. 정책 그래디언트와 Actor-Critic](03-policy-gradient-and-actor-critic.md) · [04. Q-러닝과 Off-policy](04-q-learning-and-off-policy.md)

{% raw %}
## 개요

연속 행동공간에서 $$\max_a Q(s,a)$$ 가 비선형 최적화 문제가 되므로, **명시적 actor** $$\pi_\theta$$ 를 학습해 $$\mathbb{E}_{a\sim\pi}[Q(s,a)]$$ 를 최대화하는 **Soft Actor-Critic**을 구현. off-policy(replay buffer + target network)라 HW3 DQN과 학습 루프가 거의 같다.

### 구현 대상 (TODO)
`run_hw4.py` 학습 루프 → `sac_agent.update_critic`(bootstrapped target) → soft/hard target update → `entropy`(1-sample 추정) → soft Q target → `actor_loss_reinforce` / `actor_loss_reparametrize` → `q_backup_strategy`(double-Q / clipped double-Q).

### 알고리즘

**Bootstrapped critic** (target net $$Q_{\phi'}$$, $$a_{t+1}\sim\pi(\cdot\mid s_{t+1})$$):
$$
y = r_t + \gamma(1-d_t)\,Q_{\phi'}(s_{t+1}, a_{t+1}),\qquad \min_\phi (Q_\phi(s_t,a_t) - y)^2
$$

**Soft update** (Polyak, $$\tau \approx 0.005$$): $$\phi' \leftarrow \phi' + \tau(\phi - \phi')$$.

**Entropy bonus** (온도 $$\beta$$), soft Q target:
$$
y = r_t + \gamma(1-d_t)\big[Q_{\phi'}(s_{t+1},a_{t+1}) + \beta\,\mathcal{H}(\pi(\cdot\mid s_{t+1}))\big],\qquad
\mathcal{H}(\pi(\cdot\mid s)) \approx -\log\pi_\theta(\hat a\mid s),\ \hat a\sim\pi_\theta
$$

**Actor gradient — 두 추정기**

| | 식 | 특징 |
|---|---|---|
| REINFORCE | $$\mathbb{E}_{s\sim D,\,a\sim\pi_\theta}[\nabla_\theta\log\pi_\theta(a\mid s)\,Q_\phi(s,a)]$$ | score-function, 고분산. action은 $$\pi_\theta$$에서 샘플 → `num_actor_samples`로 분산↓ |
| REPARAMETRIZE | $$a = \mu_\theta(s) + \sigma_\theta(s)\epsilon,\ \epsilon\sim\mathcal N(0,I)$$; $$\nabla_\theta \mathbb{E}[Q_\phi(s,a)] = \mathbb{E}_\epsilon[\nabla_\theta Q_\phi(s, \mu_\theta + \sigma_\theta\epsilon)]$$ | pathwise, 저분산. 1-sample로 충분 (`rsample()`) |

**Overestimation 완화**: Double-Q(교차 target: $$y_A = r + \gamma Q_{\phi'_B}(s',a')$$, $$y_B = r + \gamma Q_{\phi'_A}$$) → **Clipped Double-Q**($$y = r + \gamma\min(Q_{\phi'_A}, Q_{\phi'_B})$$).

---

## 결과 (student report)

### Sanity — Pendulum-v1 (critic-only, γ=0.99)
"do-nothing" 보상 $$\approx -10$$/step, 종료 없음 → $$Q = r/(1-\gamma) = -10/0.01 = \mathbf{-1000}$$ (이론값). 실측 Q는 **−700 ~ −750**에서 안정. 차이 원인: 실제 보상이 상수 −10이 아님, 미학습 actor가 non-zero action 샘플, soft target update·초기 상태 분포.

두 번째 sanity(entropy만 최대화): 1D action → 균등분포일 때 최대 $$\mathcal H(U[-1,1]) = \log 2 \approx \mathbf{0.69}$$. 실측 entropy가 ~0.69로 수렴.

### REINFORCE actor
| 환경 | 결과 |
|---|---|
| InvertedPendulum-v4 | 최대 return **1000** 자주 도달, REINFORCE 고분산으로 간헐적 급락. Q 값은 꾸준히 상승 |
| HalfCheetah **REINFORCE-1** (`num_actor_samples=1`) | 매우 noisy, ~**500K step** 후에야 양의 보상 |
| HalfCheetah **REINFORCE-10** (`=10`) | 편향 없이 분산만 감소 → 훨씬 빠름. **250K~300K step에 return > 500**, 후반 1000+ |

### REPARAMETRIZE actor
| 환경 | 결과 |
|---|---|
| InvertedPendulum-v4 | return 1000으로 **빠르게** 수렴 |
| HalfCheetah | 세 추정기 중 최고·최안정, 1-sample로 **~5000** 도달 (REINFORCE-1 ≈ 0, REINFORCE-10 ≈ 1000) |

→ 고차원 연속제어에서 reparam trick이 sample efficiency·안정성 모두 우위. score-function $$\nabla\log\pi\cdot Q$$ 대신 noise를 통한 pathwise gradient $$\nabla_\theta Q(s,\mu_\theta+\sigma_\theta\epsilon)$$.

### Clipped Double-Q — Hopper-v4 (seed 48)
| 방식 | 최종 Eval Return | Q 값 |
|---|---:|---|
| Single-Q | ~400, 불안정 | 가장 높음 (overestimation) |
| Double-Q | ~350, 불안정 | 중간 |
| **Clipped Double-Q** | **~700**, 가장 안정 | 가장 낮고 정확 |

Humanoid-v4: Clipped Double-Q로 500K step에 return **800~900**, Q 값도 유계로 부드럽게 상승 (default mean/single critic 대비 우위).

## 핵심 정리

- SAC = off-policy actor-critic + entropy bonus. soft Q target에 $$\beta\mathcal H$$ 포함 → 불확실할 때 더 탐색.
- Pendulum sanity: $$Q = r/(1-\gamma)$$ 로 정답 예측(−1000), 실측 −700대 → 구현 검증 도구.
- REINFORCE-10 > REINFORCE-1 (분산↓, 편향 없음); REPARAMETRIZE > 둘 다 (pathwise, 1-sample로 HalfCheetah ~5000).
- overestimation: single-Q가 Q 과대평가 → Clipped Double-Q($$\min$$)가 Hopper ~700으로 최선.

## 복습 질문

- Pendulum "do-nothing"에서 $$Q = r/(1-\gamma)$$ 유도, 이론값 −1000과 실측 −700의 차이를 설명하라.
- REINFORCE와 reparametrize gradient의 식 차이와, HalfCheetah에서 후자가 5000 vs 전자 ~0인 이유는?
- `num_actor_samples`를 늘리면 REINFORCE가 개선되는 이유(편향/분산)와 그 한계는?
- Double-Q와 Clipped Double-Q의 target 식 차이, 그리고 Hopper Q 값 그래프가 overestimation을 어떻게 보여주나?
- soft Q target에 entropy 항을 넣지 않고 actor loss에만 넣으면 무엇이 잘못되나?
{% endraw %}

---

이전 과제: [과제 3 — Q-Learning](hw3-q-learning.md) · 다음 과제: [과제 5 — Offline RL](hw5-offline-rl.md)
