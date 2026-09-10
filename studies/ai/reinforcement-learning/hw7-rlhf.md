---
layout: page
title: "과제 7 — RLHF (선호 학습 · Bradley-Terry 보상 모델 · PPO)"
permalink: /studies/ai/reinforcement-learning/hw7-rlhf/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW7](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW7) (`networks/reward_predictor.py`, `agents/pg_agent.py`, `scripts/run_hw7.py`, Flask 피드백 GUI)
- CAS4160 · Homework 7 (CS285 RLHF / Christiano et al. 2017 기반)
- 관련 강의 노트: [09. 보상 학습과 RLHF](09-reward-learning-rlhf.md)

{% raw %}
## 개요

환경 보상 대신 **사람의 선호 비교**로부터 보상 모델 $$\hat r$$ 을 학습하고, 그 학습된 보상으로 **PPO**(과제 2)를 돌린다. RLHF의 원조는 LLM이 아니라 **로봇 제어**(Christiano et al. 2017).

### 구현 대상 (TODO)
`reward_predictor.py`(Bradley-Terry 선호 확률 + cross-entropy loss) → `pg_agent.py` PPO가 학습된 보상을 쓰도록 **한 줄** 수정 → `run_hw7.py`(사람 라벨 triplet을 replay buffer에 저장, 보상 모델 학습, PPO에 보상 신호 제공).

### 알고리즘 — 선호로부터 보상 학습

trajectory segment $$\tau = ((s_0,a_0),\dots,(s_{k-1},a_{k-1}))$$. 비교 결과를 triplet $$(\tau_1, \tau_2, \mu)$$ 로 저장 ($$\mu$$ 는 $$\{1,2\}$$ 위 분포: 한쪽 선호면 그쪽에 질량 1, 무승부면 $$(0.5, 0.5)$$).

**Bradley-Terry** 선호 확률:
$$
\hat P[\tau_1 \succ \tau_2] = \frac{\exp\big(\sum_t \hat r(s^1_t, a^1_t)\big)}{\exp\big(\sum_t \hat r(s^1_t, a^1_t)\big) + \exp\big(\sum_t \hat r(s^2_t, a^2_t)\big)}
$$

**Cross-entropy loss** (예측 선호 vs 사람 라벨 $$\mu$$):
$$
L(\hat r) = -\sum_{(\tau_1,\tau_2,\mu)\in D}\Big[\mu(1)\log\hat P[\tau_1\succ\tau_2] + \mu(2)\log\hat P[\tau_2\succ\tau_1]\Big]
$$

목표: 사람이 선호할 궤적을 생성하되 **질의 수를 최소화**. 정량 평가는 (참 보상 $$r$$ 이 알려진 경우) $$\sum r(s^1_t,a^1_t) > \sum r(s^2_t,a^2_t)$$ 로, 정성 평가는 행동 영상이 자연어 목표에 얼마나 부합하는지로.

---

## 결과 (student report)

### PointMaze — 합성 선호(synthetic) vs 사람 선호(human)
| 선호 소스 | 결과 |
|---|---|
| **Synthetic** (환경 보상으로 선호 자동 생성) | 초록 공이 목표 방향으로 이동하나 **완전히 도달 못 함**, 좌상단 corridor 근처에서 정체. 합성 보상이 거의 goal까지의 유클리드 거리라, U자 미로에서 "기하적으로 가까움 ≠ 도달 가능 경로"라서 국소 최소에 빠짐 |
| **Human** (Flask GUI로 직접 라벨) | 초록 공이 valid corridor를 따라 **red target에 도달 성공** (~10분 학습). "올바른 통로로 진행하는가 / 벽에 안 걸리는가 / 같으면 더 빠른 쪽"을 기준으로 일관되게 선호 |

### Hopper Backflip (Hopper-v5)
100 optimization step, **5 step마다 human feedback** (약 1시간 20분). "뒤로 회전 / 각운동량 충분 / 몸이 뒤집힘 / 한 바퀴 완주" 기준으로 선호 라벨. 결과: Hopper가 몸을 뒤로 굽혀 지면 반동으로 회전 → **한 바퀴 백플립** 성공. 학습된 보상 기준 `Eval_AverageReturn`이 −11 → +15로 꾸준히 상승 (환경 보상이 아니라 **선호 모델 보상** 기준값이라는 점에 주의).

## 핵심 정리

- RLHF: 사람의 **쌍 비교** → Bradley-Terry로 보상 모델 $$\hat r$$ 학습(cross-entropy on $$\mu$$) → 그 보상으로 PPO.
- segment 보상 합의 softmax = 선호 확률. $$\mu = (0.5, 0.5)$$ 로 무승부 표현.
- 합성 선호(≈ 거리 기반)는 미로에서 국소 정체; 사람 선호는 "경로가 올바른가"라는 고수준 판단을 줘 도달 성공.
- RLHF가 유용한 조건: 손수 만든 보상이 어렵거나 모호하거나 의도치 않은 행동을 부를 때 — sparse reward, long-horizon, 장애물, 정성적 목표, "보면 알지만 수식화 어려운" 행동(예: 백플립).
- `Eval_AverageReturn`을 학습된 보상으로 계산하면 그 값은 "선호 모델과의 정합도"이지 실제 성능이 아님.

## 복습 질문

- Bradley-Terry 선호 확률 식과 cross-entropy loss를 쓰고, $$\mu$$ 가 무엇을 인코딩하는지 설명하라.
- PointMaze에서 synthetic 선호는 정체하고 human 선호는 성공한 이유를 미로 구조·보상 국소성으로 설명하라.
- Hopper 백플립처럼 hand-designed 보상이 어려운 이유와, 선호 비교가 이를 어떻게 우회하나?
- RLHF에서 "질의 수 최소화"가 목표인 이유, 그리고 어떤 조건에서 RLHF가 특히 필요한가?
- 학습된 보상으로 잰 `Eval_AverageReturn` 상승을 "성능 향상"이라 단정하면 안 되는 이유는?
{% endraw %}

---

이전 과제: [과제 6 — Model-Based RL](hw6-model-based-rl.md)
