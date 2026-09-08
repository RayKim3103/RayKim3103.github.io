---
layout: page
title: "02. 모방학습 (Imitation Learning)"
permalink: /studies/ai/reinforcement-learning/02-imitation-learning/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `02` 정리·보강
- 표준 자료(CS285) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**expert demonstration**으로 policy를 학습. reward 설계·탐색 없이 "이 state에서 expert는 이 action" 을 supervised learning으로 훈련. 단순 behavioral cloning은 **distribution shift**로 오류가 누적된다.

## 1. 문제 정의

$$
\tau = (s_0, a_0, s_1, a_1, \dots, s_T),\qquad
D = \{(s_i, a_i)\}_{\text{expert}}
$$
$\pi_\theta(a\mid s)$ 가 expert action에 가까워지도록.

## 2. Behavioral Cloning (BC)

$$
\min_\theta\; \mathbb{E}_{(s,a)\sim D}\big[ L(\pi_\theta(s), a) \big]
$$
연속 action → MSE, 이산 → cross-entropy.

- **장점**: 단순·안정, reward 설계 불필요.
- **단점**: expert data 분포 **밖** state에서 어떻게 해야 할지 모름.

## 3. Distribution Shift

BC는 expert가 방문한 state 분포에서만 학습. 실행 중 작은 실수 → expert data에 거의 없던 state → 또 실수 → 더 낯선 state … **오류 누적**.
$$
p_{\text{data}}(s) \ne p_{\pi_\theta}(s)
\quad(\text{모방학습의 핵심 어려움})
$$
horizon $T$가 길수록 나쁘고, 이론적으로 error가 $O(T^2)$까지 누적될 수 있다.

## 4. DAgger (Dataset Aggregation)

learner가 **실제로 방문하는** state에서 expert label을 추가 수집 → learner 분포 위에서 supervised learning.

1. expert demo로 초기 policy 학습
2. 현재 policy rollout → state 수집
3. 그 state들에 expert action query
4. dataset에 추가 후 재학습
5. 반복

## 5. IL vs RL

| | Imitation Learning | Reinforcement Learning |
|---|---|---|
| supervision | expert action | reward |
| 탐색 | 거의 불필요 | 필수 |
| 장점 | sample-efficient, 안정 | expert보다 나은 정책 가능 |
| 단점 | expert 품질·coverage 의존 | reward 설계·탐색 어려움 |

복잡한 로봇 manipulation에서는 reward shaping보다 demonstration 수집이 직관적인 경우가 많다(low-cost bimanual manipulation 등).

## 6. 실전 체크

- demo가 다양한 초기 상태·실패 근처 상황을 포함하는가?
- observation에 task에 필요한 정보가 다 들어 있는가?
- 사람 action과 policy output의 action space가 일관적인가?
- rollout error가 누적되는 horizon이 긴가? DAgger처럼 learner 분포를 보정할 방법이 있는가?

## 복습 질문

- behavioral cloning의 objective와, distribution shift가 왜·어떻게 오류를 누적시키는가?
- DAgger가 이를 해결하는 핵심 아이디어(누구의 분포 위에서 학습하는가)는?
- IL과 RL을 supervision·탐색·장단점으로 비교하라.
{% endraw %}

---

이전: [01. 강화학습 개요](01-overview.md) · 다음: [03. 정책 그래디언트와 Actor-Critic](03-policy-gradient-and-actor-critic.md)
