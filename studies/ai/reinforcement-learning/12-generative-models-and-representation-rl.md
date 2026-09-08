---
layout: page
title: "12. 생성모델·표현학습 기반 RL"
permalink: /studies/ai/reinforcement-learning/12-generative-models-and-representation-rl/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `17` 정리·보강
- 표준 자료(Decision Transformer · world model 문헌) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

RL을 더 **supervised learning처럼 안정적**으로 만들거나, 큰 offline data·foundation model의 힘을 활용하려는 흐름. Decision Transformer, return-conditioned policy, video/world model 기반 planning, representation learning.

## 1. 동기

RL이 의존하는 trick: noisy policy gradient, bootstrapping, distribution shift, value overestimation, exploration, importance sampling, credit assignment. **Rainbow DQN**(DQN + Double Q + prioritized replay + dueling + multi-step + distributional + noisy net)이 강력했던 것도 이 문제들을 각각 보완했기 때문.

생성모델(Stable Diffusion, Sora, ChatGPT)의 성공이 던지는 질문: RL을 sequence modeling으로 바꿀 수 있는가? reward·value bootstrapping 없이 좋은 policy를 만들 수 있는가?

## 2. BC → Filtered BC → Advantage-Weighted Regression

- **BC**: demonstration 모방, reward 미활용.
- **filtered BC**: return이 높은 trajectory만 골라 모방.
- **Advantage-Weighted Regression**: 높은 advantage action을 더 많이 모방 → reward 정보 일부 반영. offline RL과 BC의 중간.

## 3. Return-conditioned policy

$$
\pi(a\mid s, R_{\text{target}})
$$
trajectory를 sequence로 보고 reward-to-go·state·action을 함께 모델링:
$$
(R_0, s_0, a_0, R_1, s_1, a_1, \dots)
$$
실행 시 목표 return을 지정하면 policy가 그 수준의 행동을 생성.

## 4. Decision Transformer

RL을 **sequence modeling**으로. Transformer가 과거 return-to-go·state·action sequence를 보고 다음 action 예측.
- **장점**: supervised objective, offline dataset과 잘 맞음, long-context.
- **한계**: dataset에 없는 높은 return 행동을 새로 발견 어려움 / target return 지정에 민감 / env interaction을 통한 improvement 제한.

## 5. Video / World Model

video generation model = 미래 observation을 예측하는 world model. UniPi·UniSim류는 action-conditioned video prediction/simulated experience로 planning·policy learning.
**핵심**: "미래를 그럴듯하게 생성"과 "control에 필요한 정확한 dynamics 예측"은 다를 수 있다.

## 6. Representation Learning

좋은 representation은 raw obs에서 task-relevant factor 추출: pixel → compact latent, irrelevant visual variation 제거, dynamics 예측에 필요한 정보 유지 → downstream RL sample efficiency↑. model-based RL의 latent dynamics와 강하게 연결.

## 7. 실전 주의

- generative likelihood가 높다고 좋은 control model은 아니다.
- offline sequence model은 dataset support 밖 action을 만들기 어렵다.
- return conditioning은 reward scale·dataset quality에 민감.
- representation이 reward-relevant 정보를 버리면 policy가 실패.

## 복습 질문

- Decision Transformer가 RL을 어떻게 sequence modeling으로 바꾸며, 그 한계는?
- return-conditioned policy의 아이디어와 민감한 요인은?
- "생성 품질 ≠ 제어 품질"이 world model에서 왜 문제인가?
- RL에서 좋은 representation의 조건은?
{% endraw %}

---

이전: [11. Sim2Real · Humanoid RL](11-sim2real-and-humanoid-rl.md) · 다음: [13. 강화학습 리뷰와 열린 문제](13-review-and-open-problems.md)
