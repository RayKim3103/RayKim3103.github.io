---
layout: page
title: "12. Reward Learning"
permalink: /studies/ai/reinforcement-learning/12-reward-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/12%20Reward%20Learning.md)

{% raw %}
## 핵심 요약

Reward learning은 사람이 직접 설계하기 어려운 reward function을 data, demonstration, preference, human feedback으로부터 학습하는 방법이다. 시뮬레이션에서는 reward를 코드로 만들 수 있지만, 현실 task에서는 무엇을 reward로 줄지 명확하지 않거나 reward shaping이 부작용을 낳을 수 있다. RLHF와 preference learning은 이 문제를 해결하는 대표 접근이다.

## reward는 어디서 오는가

시뮬레이션 환경에서는 reward가 코드에 들어 있다. 예를 들어 HalfCheetah는 앞으로 이동한 거리와 action penalty를 조합하고, LunarLander는 위치, 각도, 다리 접촉, 연료 사용, 성공/실패를 shaping한다.

하지만 현실 task에서는 reward 설계가 어렵다.

- 목표가 모호하다.
- 여러 기준이 충돌한다.
- sparse success reward만 주면 학습이 느리다.
- reward hacking이 발생할 수 있다.
- 사람이 원하는 행동과 proxy reward가 다를 수 있다.

## Demonstration 기반 reward

expert data가 있으면 expert 행동을 선호하는 reward를 학습할 수 있다. inverse reinforcement learning은 expert가 최적화했을 reward를 추정하려는 접근이다.

직관:

```text
expert trajectory가 높은 reward를 받아야 한다.
non-expert trajectory는 낮은 reward를 받아야 한다.
```

다만 reward가 유일하게 식별되지 않을 수 있고, reward를 학습한 뒤 다시 RL을 돌려야 하는 부담이 있다.

## Preference Learning

preference learning은 사람에게 두 trajectory 또는 두 response 중 어느 쪽이 더 좋은지 묻고, 그 비교 data로 reward model을 학습한다.

```text
τA > τB
```

reward model `rφ`가 trajectory return을 계산하고, Bradley-Terry 형태의 확률 모델을 쓸 수 있다.

```text
P(τA preferred) =
exp(Rφ(τA)) / [exp(Rφ(τA)) + exp(Rφ(τB))]
```

loss는 사람이 고른 쪽의 확률을 높이는 cross entropy 형태이다.

## RLHF

RLHF는 human feedback으로 reward model을 학습하고, 그 reward model을 사용해 policy를 RL로 최적화하는 절차이다.

일반 흐름:

1. supervised fine-tuning으로 초기 policy를 만든다.
2. 여러 output을 생성하고 사람이 preference label을 준다.
3. preference data로 reward model을 학습한다.
4. policy를 reward model 기준으로 PPO 등으로 최적화한다.
5. base policy에서 너무 멀어지지 않도록 KL penalty를 둔다.

언어모델에서는 사람이 선호하는 답변 스타일과 안전성을 반영하는 데 쓰인다.

## DPO

Direct Preference Optimization은 reward model을 명시적으로 따로 학습하고 PPO를 돌리는 대신, preference data에서 policy를 직접 업데이트하는 방법이다.

핵심 관점은 optimal policy와 reward 사이의 관계를 이용해 preference loss를 policy likelihood ratio로 바꾸는 것이다. 실전에서는 RLHF보다 구현이 단순하고 안정적인 경우가 많다.

## reward learning의 위험

- preference data가 편향될 수 있다.
- reward model이 학습 data 밖에서 잘못된 점수를 줄 수 있다.
- policy가 reward model의 허점을 찾아 reward hacking을 할 수 있다.
- 사람 feedback은 비싸고 noisy하다.
- 여러 사람의 선호가 일관되지 않을 수 있다.

## 실전 체크

- reward model을 별도 validation preference로 평가했는가?
- policy optimization 중 reward model score만 오르고 실제 품질은 떨어지지 않는가?
- KL constraint나 early stopping으로 policy drift를 막는가?
- preference pair가 충분히 어려운 비교를 포함하는가?
- human feedback 기준이 명확한가?

## 연결 노트

- [모방학습](02-imitation-learning.md)
- [Offline RL](08-offline-rl.md)
- [Skill Discovery](13-skill-discovery.md)
- [강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)

{% endraw %}

---

이전: [11. AlphaGo와 MCTS](11-alphago-mcts.md) · 다음: [13. Skill Discovery](13-skill-discovery.md)
