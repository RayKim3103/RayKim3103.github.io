---
layout: page
title: "17. 생성모델과 표현학습 기반 RL"
permalink: /studies/ai/reinforcement-learning/17-generative-models-and-representation-learning-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/17%20%EC%83%9D%EC%84%B1%EB%AA%A8%EB%8D%B8%EA%B3%BC%20%ED%91%9C%ED%98%84%ED%95%99%EC%8A%B5%20%EA%B8%B0%EB%B0%98%20RL.md)

## 핵심 요약

생성모델과 표현학습 기반 RL은 RL을 더 supervised learning처럼 안정적으로 만들거나, 큰 offline data와 foundation model의 힘을 활용하려는 흐름이다. Decision Transformer, return-conditioned policy, video generation 기반 planning, world model, representation learning이 핵심 주제이다.

## 왜 필요한가

강의는 RL이 실제로 많은 trick에 의존한다고 강조한다.

- noisy policy gradient
- bootstrapping
- distribution shift
- value overestimation
- exploration
- importance sampling
- credit assignment

Rainbow DQN처럼 DQN, Double Q-learning, prioritized replay, dueling network, multi-step learning, distributional RL, noisy network를 모두 합친 알고리즘이 강력했던 이유도 이런 문제를 각각 보완하기 위해서이다.

## 생성모델의 성공과 RL

Stable Diffusion, Sora, Copilot, ChatGPT 같은 생성모델의 성공은 RL에도 질문을 던진다.

- RL을 더 supervised learning처럼 만들 수 있는가?
- 큰 trajectory dataset을 sequence modeling으로 학습할 수 있는가?
- reward와 value bootstrapping 없이도 좋은 policy를 만들 수 있는가?
- video/world model을 사용해 planning할 수 있는가?

## Behavioral Cloning의 한계

BC는 demonstration을 모방하지만 reward 정보를 활용하지 않는다. filtered BC는 return이 높은 trajectory만 골라 모방하지만, 여전히 reward 구조를 적극적으로 학습하지 않는다.

Advantage-weighted regression은 높은 advantage action을 더 많이 모방해 reward 정보를 일부 반영한다. Offline RL과 BC 사이의 중간적인 접근이다.

## Return-conditioned policy

Return-conditioned policy는 원하는 return을 condition으로 넣고 action을 예측한다.

```text
π(a|s, Rtarget)
```

trajectory를 sequence로 보고 reward-to-go, state, action을 함께 모델링한다.

```text
(R0, s0, a0, R1, s1, a1, ...)
```

실행 시에는 목표 return을 지정하고 policy가 그 수준의 행동을 생성하도록 한다.

## Decision Transformer

Decision Transformer는 RL을 sequence modeling 문제로 바꾼다. Transformer는 과거 return-to-go, state, action sequence를 보고 다음 action을 예측한다.

장점:

- supervised learning objective로 학습 가능
- offline dataset과 잘 맞음
- long-context sequence modeling을 활용 가능

한계:

- dataset에 없는 높은 return 행동을 새로 발견하기 어렵다.
- target return 지정이 성능에 민감하다.
- environment interaction을 통한 improvement가 제한될 수 있다.

## Video와 World Model

video generation model은 미래 observation을 예측하는 world model로 볼 수 있다. UniPi, UniSim 같은 접근은 action-conditioned video prediction 또는 simulated experience를 사용해 planning과 policy learning을 시도한다.

핵심은 “미래를 그럴듯하게 생성하는 것”과 “control에 필요한 정확한 dynamics를 예측하는 것”이 다를 수 있다는 점이다.

## Representation Learning

좋은 representation은 raw observation에서 task-relevant factor를 뽑아낸다.

- pixel observation을 compact latent로 바꿈
- irrelevant visual variation 제거
- dynamics prediction에 필요한 정보 유지
- downstream RL sample efficiency 향상

model-based RL의 latent dynamics와도 강하게 연결된다.

## 실전 주의점

- generative likelihood가 높다고 좋은 control model은 아니다.
- offline sequence model은 dataset support 밖 action을 만들기 어렵다.
- return conditioning은 reward scale과 dataset quality에 민감하다.
- representation이 reward-relevant 정보를 버리면 policy가 실패한다.

## 연결 노트

- [Offline RL](08-offline-rl.md)
- [고급 Model-Based RL](10-advanced-model-based-rl.md)
- [AlphaGo와 MCTS](11-alphago-mcts.md)
- [Humanoid RL](16-humanoid-rl.md)
- [강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)


---

이전: [16. Humanoid RL](16-humanoid-rl.md) · 다음: [18. 강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)
