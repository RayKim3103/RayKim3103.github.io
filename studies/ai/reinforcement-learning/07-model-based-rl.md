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

무한·open-loop 최적화는 불가능(model 오차 누적) → 유한 horizon $$H$$, 유한 후보 $$K$$개 action sequence 위에서:
$$
A^* = \arg\max_{A^{(0)},\dots,A^{(K-1)}} \sum_{t'=t}^{t+H-1} r(\hat s_{t'}, a_{t'}),\qquad \hat s_{t'+1} = F_\phi(\hat s_{t'}, a_{t'})
$$
첫 action만 실행 → 다음 state에서 재계획 = **receding horizon / MPC** (누적 오차 완화).

### Random shooting → CEM
- **Random shooting**: $$K$$개 무작위 action sequence sample → model rollout → return 큰 것 선택.
- **Cross-Entropy Method**: $$K$$개 중 상위 $$J$$개 **elite**를 뽑아 그 평균·분산으로 diagonal Gaussian을 refit → 다음 iteration 샘플 분포. $$M$$회 반복 후 최종 평균을 action으로. (반복적 분포 최적화라 random shooting보다 훨씬 강력.)

## 3. Model learning

정규화된 **state 차분**을 예측 (다음 state 직접 예측보다 스케일·수치안정에 유리):
$$
\min_\phi\; \mathbb{E}_{(s_t,a_t,s_{t+1})\sim D}\big\lVert \text{Normalize}(s_{t+1}-s_t) - f_\phi(s_t, a_t) \big\rVert^2,\qquad
F_\phi(s,a) = s + \text{Unnormalize}(f_\phi(s,a))
$$
reward도 모르면 reward model 함께. **model error는 horizon이 길수록 누적** → 긴 full trajectory보다 **짧은 rollout을 자주**. **Ensemble**($$N$$개 독립 network)로 후보마다 $$N$$개 rollout 평균 → model 편향 완화.

## 4. 심화 — Model 결합 기법

### MBPO (Model-Based Policy Optimization)
실제 data $$D_{\text{env}}$$ 로 model 학습 → $$D_{\text{env}}$$ 의 여러 state에서 **짧은 imaginary rollout** → $$D_{\text{model}}$$ 을 policy/critic update에 추가 사용.

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

## 관련 과제

[과제 6 — Model-Based RL](hw6-model-based-rl.md): dynamics model + MPC(random shooting / CEM) 구현. 실측(HalfCheetah) — model loss < 0.2로 하강, **CEM 2500.27 ± 50.19 vs random shooting 847.05 ± 54.77** (약 3배). CEM 하이퍼파라미터: **H=1 → −15250 붕괴**(미래 못 봄 → 오차 누적), K=50 → 1302(후보 부족). horizon $$H$$·후보 수 $$K$$ 둘 다 결정적.

## 복습 질문

- 왜 다음 state 대신 (정규화된) state 차분을 예측하나? $$F_\phi$$ 정의를 쓰라.
- planning(MPC)과 CEM 절차(elite refit), 그리고 왜 짧은 rollout을 자주 쓰는가?
- MBPO/Dyna가 실제 경험과 상상 경험을 어떻게 결합하는가?
- Dreamer/TD-MPC가 latent representation을 쓰는 이유와, "생성 품질 ≠ 제어 품질"의 의미는?
- model-based RL에서 model error/exploitation이 왜 위험한가?
{% endraw %}

---

이전: [06. Offline RL](06-offline-rl.md) · 다음: [08. AlphaGo와 MCTS](08-alphago-and-mcts.md)
