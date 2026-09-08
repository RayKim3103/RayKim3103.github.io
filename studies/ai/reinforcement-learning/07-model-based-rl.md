---
layout: page
title: "07. Model-Based RL (기초 · 심화)"
permalink: /studies/ai/reinforcement-learning/07-model-based-rl/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `09(기초)` + `10(고급)` 통합·보강
- 표준 자료(MBPO · Dyna · Dreamer · TD-MPC 문헌) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**dynamics/reward model**을 학습(또는 알고)하고, 그 model로 **planning** 또는 policy improvement. model-free가 실제 sample에 크게 의존한다면, model-based는 **imaginary rollout**으로 sample efficiency를 높인다.

## 1. Model이란

$$
p(s_{t+1}\mid s_t, a_t),\qquad r(s_t, a_t)
$$
action을 취했을 때 다음 state·reward가 어떻게 생기는지. (로봇이 바나나를 던지는 예: state = pose, action = joint control, model = 물리 법칙, reward = 바구니에 들어갔는가.)

## 2. Planning

미래 action sequence를 평가해 가장 좋은 **첫 action**을 고름:
$$
a^*_{0:H} = \arg\max_{a_{0:H}} \sum_{t=0}^{H} r(s_t, a_t)
$$
첫 action만 실행 → 다음 state에서 재계획 = **receding horizon / MPC**.

### Random shooting → CEM
random action sequence를 sample → model로 rollout → return 큰 것 선택.
**Cross-Entropy Method**: elite set(상위)을 뽑아 그 분포(평균·분산)를 갱신 → 반복.

## 3. Model learning

$$
\min_\phi \sum \lVert f_\phi(s_t, a_t) - s_{t+1} \rVert^2
$$
reward도 모르면 reward model 함께. **model error는 horizon이 길수록 누적** → 긴 full trajectory보다 **짧은 rollout을 자주**.

## 4. 심화 — Model 결합 기법

### MBPO (Model-Based Policy Optimization)
실제 data $D_{\text{env}}$ 로 model 학습 → $D_{\text{env}}$ 의 여러 state에서 **짧은 imaginary rollout** → $D_{\text{model}}$ 을 policy/critic update에 추가 사용.

### Dyna
model-free update + model-based planning update를 섞는 고전 구조:
1. 실제 env step → transition
2. Q-learning update
3. model update
4. model에서 simulated transition 여러 개 → 추가 Q update

### Dreamer
pixel을 직접 예측하지 않고 **latent state dynamics** 학습 → **latent imagination** 안에서 actor-critic.
- representation model: obs → latent
- dynamics model: latent transition
- reward/value model: latent → reward, value
- actor: imagined trajectory return 최대화

### TD-MPC
latent dynamics + TD learning + MPC 결합. representation은 **control-relevant** 정보를 담도록, model은 pixel reconstruction보다 value·reward 예측에 유용하게. MPC로 state마다 action sequence 재최적화 → robustness↑.

## 5. 공통 위험

- **model exploitation**: policy가 model의 오류를 이용해 실제로는 나쁜 행동 선택
- **uncertainty estimation**: model이 모르는 영역을 구분해야
- long-horizon: 상상 rollout이 길수록 불확실성↑
- representation: 예측에 필요한 정보 ≠ 제어에 필요한 정보

## 복습 질문

- planning(MPC)과 CEM 절차, 그리고 왜 짧은 rollout을 자주 쓰는가?
- MBPO/Dyna가 실제 경험과 상상 경험을 어떻게 결합하는가?
- Dreamer/TD-MPC가 latent representation을 쓰는 이유와, "생성 품질 ≠ 제어 품질"의 의미는?
- model-based RL에서 model error/exploitation이 왜 위험한가?
{% endraw %}

---

이전: [06. Offline RL](06-offline-rl.md) · 다음: [08. AlphaGo와 MCTS](08-alphago-and-mcts.md)
