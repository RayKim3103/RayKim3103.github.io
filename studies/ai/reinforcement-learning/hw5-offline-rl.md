---
layout: page
title: "과제 5 — Offline RL (Conservative Q-Learning)"
permalink: /studies/ai/reinforcement-learning/hw5-offline-rl/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW5](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW5) (`scripts/run_cql.py`, `critics/cql_critic.py`)
- CAS4160 · Homework 5 (CS285 offline RL / CQL 기반)
- 관련 강의 노트: [06. 오프라인 강화학습](06-offline-rl.md)

{% raw %}
## 개요

고정된 데이터셋 $$D = \{(s_i, a_i, r_i, s'_i)\}$$ 만으로 학습하는 **offline RL**. 데이터에 없는 행동(**unseen action**)의 Q 값이 과대평가되어 policy가 그쪽으로 쏠리는 **distributional shift**가 핵심 문제. **Conservative Q-Learning(CQL)**으로 이를 억제.

- 환경: **PointmassMedium-v0** — grid world 내비게이션, 관측 $$(y,x)$$, action 5개(정지/상하좌우), goal 도달 시 reward 1·종료, 그 외 0 (sparse). eval return ≈ 성공률.
- 데이터셋: (1) **random** policy 100000 step, (2) **expert** policy 20000 step.

### 알고리즘 — CQL (discrete, DQN 위에 정규화항)
$$
L_Q(\theta) = \underbrace{\frac{1}{N}\sum_i \Big(Q(s_i,a_i) - \big[r_i + \gamma\max_{a'} Q(s'_i,a')\big]\Big)^2}_{\text{표준 TD 오차}}
\;+\; \alpha\,\underbrace{\frac{1}{N}\sum_i\Big[\log\sum_a \exp Q(s_i,a) \;-\; Q(s_i,a_i)\Big]}_{\text{CQL regularizer}}
$$
regularizer는 모든 행동의 Q를 **낮추고**(logsumexp) 데이터에 있는 행동의 Q만 **올린다** → OOD 행동의 Q 과대평가 억제. **$$\alpha = 0$$ 이면 정규화항이 사라져 정확히 vanilla DQN**.

---

## 결과 (student report, PointmassMedium-v0, 50000 iter)

### Expert 데이터셋 — α 비교
| α | Eval Mean Return | overestimation $$\frac1N\sum[\log\sum_a e^{Q} - Q(s,a)]$$ |
|---|---:|---|
| **0.0** (= vanilla DQN) | **0** (goal 도달 실패, policy 불안정) | 1.4 ~ 1.6로 계속 증가 |
| **0.1** | **1** (≈ 0.9+, 목표 달성) | 초반 급감 후 0.2 ~ 0.3 안정 |

### Random 데이터셋 — α 비교
| 데이터 · α | Eval Mean Return |
|---|---:|
| Random, α = 0.0 | 1 |
| Random, α = 0.1 | 1 |
| Expert, α = 0.0 | 0 |
| Expert, α = 0.1 | 1 |

### 왜 α=0이 random에서는 되고 expert에서는 실패하나
- **Expert 데이터**는 near-optimal 행동만 좁게 덮음 → 방문 안 한 행동이 많고, 그들의 Q가 크게 overestimate → α=0(DQN)이면 policy가 그 환상 행동으로 발산.
- **Random 데이터**는 상태·행동 공간을 넓게 덮음 → unseen 행동이 적어 overestimation이 덜 심각 → α=0로도 학습 가능. 따라서 **정규화의 필요성은 데이터 품질(=coverage)에 달려 있다**.

## 핵심 정리

- Offline RL의 적: OOD 행동 Q 과대평가로 인한 distributional shift. exploration 불가라 self-correction이 없다.
- CQL = TD 오차 + $$\alpha(\log\sum_a e^{Q} - Q(s,a))$$. logsumexp이 "모든 Q push down, 데이터 행동만 push up".
- α=0 ⇔ vanilla DQN. Expert 데이터 + α=0 → return 0(실패); α=0.1 → 0.9+.
- 좁고 좋은 데이터일수록 conservatism이 더 중요; 넓은(random) 데이터는 덜 필요.
- overestimation 곡선(1.5 → 0.25)이 정규화가 실제로 Q를 눌렀음을 직접 보여줌.

## 복습 질문

- CQL objective를 쓰고, regularizer의 두 항(logsumexp, $$-Q(s,a)$$)이 각각 무엇을 하는지 설명하라.
- "$$\alpha = 0$$ 은 어떤 알고리즘인가"에 답하고, expert 데이터에서 왜 실패하는지 OOD 행동으로 설명하라.
- 같은 α=0인데 random 데이터에서는 성공하는 이유를 데이터 coverage로 설명하라.
- online RL이라면 이 overestimation이 자연히 교정되는데, offline에서는 왜 안 되나?
{% endraw %}

---

이전 과제: [과제 4 — Soft Actor-Critic](hw4-soft-actor-critic.md) · 다음 과제: [과제 6 — Model-Based RL](hw6-model-based-rl.md)
