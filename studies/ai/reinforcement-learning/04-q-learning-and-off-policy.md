---
layout: page
title: "04. Q-learning과 Off-policy RL"
permalink: /studies/ai/reinforcement-learning/04-q-learning-and-off-policy/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `05(Q-learning)` + `06(Off-policy RL)` 통합·보강
- 표준 자료(DQN · DDPG · TD3 · SAC 논문) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

policy를 직접 미분하지 않고 **value $$Q(s,a)$$** 를 학습 → greedy action 선택. Q-learning 계열은 본질적으로 **off-policy**(behavior policy ≠ target policy) → replay buffer로 data 재사용. 연속 action에서는 DDPG/TD3/SAC.

## 1. Value function & Bellman

$$
V^\pi(s) = \mathbb{E}_\pi\Big[\sum_t \gamma^t r_t \,\Big|\, s_0{=}s\Big],\qquad
Q^\pi(s,a) = \mathbb{E}_\pi\Big[\sum_t \gamma^t r_t \,\Big|\, s_0{=}s, a_0{=}a\Big]
$$
$$
Q^*(s,a) = r(s,a) + \gamma\,\mathbb{E}_{s'}\big[\max_{a'} Q^*(s',a')\big]
\qquad
\pi^*(s) = \arg\max_a Q^*(s,a)
$$
value iteration = Bellman optimality backup 반복.

## 2. Q-learning update

$$
Q(s,a) \leftarrow Q(s,a) + \alpha\big[\, \underbrace{r + \gamma \max_{a'} Q(s',a') - Q(s,a)}_{\text{TD error}} \,\big]
$$

### Fitted Q-Iteration (함수근사)
$$
y_i = r_i + \gamma \max_{a'} Q_{\phi_{\text{old}}}(s'_i, a'),\qquad
\min_\phi \sum_i \big(Q_\phi(s_i,a_i) - y_i\big)^2
$$
→ Q-learning을 deep learning과 연결. target policy는 greedy로 **암묵적** 정의 → data를 어떤 policy가 모았는지와 무관하게 학습 가능(= off-policy).

## 3. DQN

고차원(image) 입력에서 $$Q_\phi$$ 를 신경망으로. 핵심 trick:
- **replay buffer**: 과거 transition 저장 → mini-batch 샘플링 (상관 제거, data 재사용)
- **target network**: target 계산용 network를 느리게 갱신 (안정화)
- **$$\epsilon$$-greedy**: greedy + random 섞음

1. Q net으로 action 선택 → env step → $$(s,a,r,s')$$ → buffer
2. mini-batch 샘플 → target net으로 $$y = r + \gamma(1-d)\max_{a'} Q_{\text{target}}(s',a')$$
3. Q net을 TD loss로 갱신 (Huber loss + gradient clipping으로 안정화)
4. target net 주기적 hard copy ($$\phi'\leftarrow\phi$$ every $$K$$) 또는 Polyak soft update ($$\phi'\leftarrow\phi' + \tau(\phi-\phi')$$, $$\tau\approx0.005$$)

**Deadly triad**: function approximation + bootstrapping + off-policy 셋이 겹치면 Q가 자기 target을 좇는 양의 피드백으로 **발산**할 수 있다 (learning rate가 클수록 심함).

### Double Q-learning (overestimation)
$$\max_a Q(s,a)$$ 는 noisy estimate에서 과대평가. action **선택**과 **평가** 분리:
$$
a^* = \arg\max_a Q_{\text{online}}(s',a),\qquad
y = r + \gamma\, Q_{\text{target}}(s', a^*)
$$

**장단점**: off-policy(재사용), discrete action에서 강력, replay로 sample 효율↑ / continuous action의 $$\arg\max_a Q$$ 어려움, bootstrapping + function approx 결합 시 불안정, 단순 exploration은 sparse reward에서 실패.

## 4. Continuous action — DDPG / TD3 / SAC

### DDPG
deterministic actor $$\mu_\theta(s) \approx \arg\max_a Q_\phi(s,a)$$ 를 학습:
$$
\nabla_\theta J \approx \mathbb{E}_{s\sim D}\big[\nabla_a Q_\phi(s,a)\big|_{a=\mu_\theta(s)}\, \nabla_\theta \mu_\theta(s)\big]
$$
DQN의 replay buffer + target network를 continuous control에. exploration은 action에 noise 추가, target actor/critic soft update. hyperparameter 민감, Q overestimation.

### TD3
- **twin critics**: 두 Q 중 **작은 값**을 target에 → overestimation↓
- **delayed policy update**: critic을 더 자주, actor는 천천히
- **target policy smoothing**: target action에 noise → critic의 sharp peak 악용 방지

### SAC — Maximum Entropy RL
reward + policy entropy를 함께 최대화 (온도 $$\alpha$$ 또는 $$\beta$$):
$$
J(\pi) = \mathbb{E}\Big[\sum_t \gamma^t\big(r(s_t,a_t) + \alpha\, \mathcal{H}(\pi(\cdot\mid s_t))\big)\Big],\qquad
\mathcal{H}(\pi(\cdot\mid s)) \approx -\log\pi(\hat a\mid s),\ \hat a\sim\pi
$$
$$
y = r + \gamma\, \mathbb{E}_{a'\sim\pi}\big[Q_{\text{target}}(s',a') - \alpha \log \pi(a'\mid s')\big]
$$
actor는 $$\min_\pi \mathbb{E}[\alpha \log \pi(a\mid s) - Q(s,a)]$$ — Q가 큰 action을 고르되 entropy 유지 → exploration 장려, 여러 좋은 action 유지. actor gradient는 **reparametrization**($$a = \mu_\theta(s) + \sigma_\theta(s)\epsilon$$)으로 저분산 pathwise 추정. overestimation 대응은 **clipped double-Q**: $$y = r + \gamma\min(Q_{\phi'_A}, Q_{\phi'_B})$$.

## 5. Off-policy 실전

- rollout data 재사용 → sample 효율↑, 과거 실험/여러 behavior policy data 활용.
- **주의**: replay data와 현재 policy 분포가 너무 멀면 불안정 / Q target의 bootstrapping 오차 누적 / **완전 static dataset**만 쓰면(=offline RL) OOD action 문제가 커짐 → 별도 처리 필요.

## 관련 과제

- [과제 3 — Q-Learning](hw3-q-learning.md): DQN·Double DQN 구현. 실측 — CartPole-v1 DQN이 300K step에 return ≈ 500 수렴. **lr=0.05에서 deadly triad 발산**(Q 과대평가·critic loss ~300 스파이크·return 붕괴). Double DQN은 BankHeist에서 소폭 우위(환경 단순·1M step 한계·seed 분산).
- [과제 4 — Soft Actor-Critic](hw4-soft-actor-critic.md): SAC 구현. 실측 — Pendulum critic-only에서 $$Q = r/(1-\gamma) = -1000$$ 예측 대비 실측 −700대. Clipped Double-Q가 Hopper ~700으로 single-Q·double-Q보다 우위(Q 과대평가 최소).

## 복습 질문

- $$Q^*$$ 의 Bellman optimality equation과, Q-learning이 off-policy인 이유는?
- DQN의 replay buffer·target network·$$\epsilon$$-greedy 각각의 역할은?
- Double Q-learning이 overestimation을 줄이는 방식은? (clipped double-Q와의 차이도)
- deadly triad 세 요소와, learning rate가 크면 발산하는 이유는?
- TD3의 세 가지 개선과, SAC의 max-entropy objective가 주는 이점은?
{% endraw %}

---

이전: [03. 정책 그래디언트와 Actor-Critic](03-policy-gradient-and-actor-critic.md) · 다음: [05. RL 벤치마크](05-rl-benchmarks.md)
