---
layout: page
title: "과제 3 — Q-Learning (DQN · Double DQN)"
permalink: /studies/ai/reinforcement-learning/hw3-q-learning/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW3](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW3) (`run_hw3.py`, `agents/dqn_agent.py`, `env_configs/dqn_basic_config.py`, `env_configs/dqn_atari_config.py`, `infrastructure/replay_buffer.py`, `infrastructure/atari_wrappers.py`)
- CAS4160 · Homework 3 (CS285 HW3 기반)
- 관련 강의 노트: [04. Q-러닝과 Off-policy](04-q-learning-and-off-policy.md)

{% raw %}
## 개요

**Deep Q-Network(DQN)**와 **Double DQN**을 구현하고, state 기반 환경(**CartPole-v1**)과 pixel 기반 Atari(**BankHeist-v5**, grayscale·frame-skip 4·frame-stack 4)에서 평가. replay buffer·target network가 안정성에 주는 영향과 **deadly triad**(function approximation + bootstrapping + off-policy)를 관찰.

### 구현 대상 (TODO)
`DQNCritic.update_critic`(Bellman target·Huber loss·gradient clipping) → `update`(critic 갱신 + 필요 시 target network 하드 카피) → `get_action`($$\epsilon$$-greedy) → `run_hw3.py` 학습 루프 → `utils.sample_trajectory`(terminated vs truncated 구분 — truncation 시 replay buffer의 done flag는 False).

### 알고리즘

**DQN target** (target network $$Q_{\phi'}$$):
$$
y_j = r_j + \gamma\,(1-d_j)\max_{a'} Q_{\phi'}(s'_j, a'),\qquad
L(\phi) = \frac{1}{B}\sum_j \big(Q_\phi(s_j,a_j) - y_j\big)^2
$$

**Double DQN** — max 연산의 **overestimation bias** 완화. action 선택은 online net $$Q_\phi$$, 평가는 target net $$Q_{\phi'}$$:
$$
a' = \arg\max_{a'} Q_\phi(s'_j, a'),\qquad y_j = r_j + \gamma\,(1-d_j)\,Q_{\phi'}(s'_j, a')
$$

핵심 장치: (1) **replay buffer** — 시간 상관 제거, 데이터 재사용 (2) **target network** — moving-target 문제 완화 (3) $$\epsilon$$ / lr **scheduling**, **gradient clipping**.

---

## 결과 (student report)

### DQN — CartPole-v1 (`cartpole.yaml`, seed 1, 300K steps)
초반엔 exploration 우세로 낮음 → **100K~200K step부터 급상승** → **300K 부근에서 return ≈ 500 안정 수렴** (과제 목표 달성). target network + replay buffer가 Q-learning 불안정을 완화함을 확인.

### Learning rate 민감도 — CartPole-v1 (lr ∈ {1e-4, 1e-3, 1e-2, 5e-2})
| lr | Eval Return | 예측 Q | critic loss |
|---|---|---|---|
| 1e-4 | 느리다가 ~250K 후 500 도달 (수렴 느림) | — | 낮음 |
| **1e-3 (기본)** | **100K 후 빠르게 500, 안정** | 90~95로 매끄럽게 수렴 | ≈ 0 안정 |
| 1e-2 | 중간 성능, 크게 진동 | — | 진동 |
| **5e-2** | **~100 수준에서 진동, 500 도달 실패** | 100~110+로 과대·변동 | 150K 후 ~300까지 **폭발적 스파이크** |

→ lr↑ → gradient step size↑ → moving-target 악화 + (근사 + 부트스트랩 + off-policy)로 **deadly triad** 발산. lr=0.05는 "큰 step size → 불안정"의 교과서적 사례.

### Double DQN vs DQN — BankHeist-v5 (3 seeds each, 1M steps)
- 두 알고리즘 모두 return이 상승; **600K step까지 거의 차이 없음**, 이후 Double DQN(red)이 vanilla DQN(blue)보다 **소폭** 우위.
- 이론상 Double DQN이 overestimation을 줄여 더 안정적이어야 하나, 이 실험에선 큰 차이 없음. 이유: (1) BankHeist는 비교적 단순한 Atari라 overestimation 영향이 작음 (2) 1M step은 Double DQN 이점이 드러나기엔 짧음 (3) seed·exploration schedule 무작위성이 큼. (과제도 "결과보다 구현 정확성으로 평가"라고 명시.)

## 핵심 정리

- DQN = Q-learning + (replay buffer, target network, Huber loss, gradient clipping). 두 장치가 없으면 발산.
- Double DQN: $$\max$$ 를 "online으로 argmax → target으로 evaluate"로 분리 → overestimation 완화 (실측에선 BankHeist에서 소폭).
- deadly triad: 근사 + 부트스트랩 + off-policy. **lr=0.05에서 Q 과대평가·critic loss 폭발·return 붕괴**로 직접 관찰됨.
- lr은 U자형이 아니라 "작으면 느림 / 크면 발산" — CartPole 최적 ≈ 1e-3.
- truncation(최대 길이 도달)과 termination(실패)은 다르다 — truncation 시 TD done flag는 False.

## 복습 질문

- DQN의 Bellman target 식과, replay buffer·target network가 각각 해결하는 문제는?
- Double DQN이 overestimation bias를 줄이는 원리(어떤 네트워크가 argmax, 어떤 네트워크가 evaluate)를 식으로.
- deadly triad의 세 요소와, lr=0.05에서 Q·critic loss·return이 각각 어떻게 됐고 왜 그런지 설명하라.
- $$\epsilon$$-greedy schedule을 학습 초반 크게, 후반 작게 두는 이유는?
- 이론상 우월한 Double DQN이 BankHeist 실험에서 큰 차이를 못 보인 이유 3가지는?
{% endraw %}

---

이전 과제: [과제 2 — Policy Gradients](hw2-policy-gradients.md) · 다음 과제: [과제 4 — Soft Actor-Critic](hw4-soft-actor-critic.md)
