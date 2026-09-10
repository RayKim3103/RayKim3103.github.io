---
layout: page
title: Reinforcement Learning
description: >
  강화학습(CAS4160) 강의 노트와 프로그래밍 과제. 강의 내용과 과제 내용을 분리해 정리했습니다.
hide_description: false
sitemap: false
permalink: /studies/ai/reinforcement-learning/
---

원본: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning)

> **강의 노트**와 **프로그래밍 과제**(HW1–HW7, Berkeley CS285 기반)를 별도 파일로 분리했습니다. 강의 노트는 원본 18개 강의 중 "기초 → 심화"로 이어지는 연속 강의(정책 그래디언트+Actor-Critic, Q-learning+Off-policy, Model-Based 기초+심화, Skill Discovery+Hierarchical, Sim2Real+Humanoid)를 주제 단위로 합치고, pseudo-code를 정식 표기로 다듬어 표준 자료(Sutton & Barto · CS285 · 관련 논문) 수준으로 보강했습니다. 과제 노트는 과제 스펙·알고리즘 유도·코드 구조와 **학생 리포트의 실제 측정 결과**(return·Q값·분산 등)를 그대로 정리했습니다.

## 강의 노트

1. [강화학습 개요](01-overview.md)
2. [모방학습 (Imitation Learning)](02-imitation-learning.md)
3. [정책 그래디언트와 Actor-Critic (PPO)](03-policy-gradient-and-actor-critic.md)
4. [Q-learning과 Off-policy RL](04-q-learning-and-off-policy.md)
5. [RL 벤치마크와 평가](05-rl-benchmarks.md)
6. [Offline RL](06-offline-rl.md)
7. [Model-Based RL (기초 · 심화)](07-model-based-rl.md)
8. [AlphaGo와 MCTS](08-alphago-and-mcts.md)
9. [Reward Learning · RLHF](09-reward-learning-rlhf.md)
10. [Skill Discovery · Hierarchical RL](10-skill-discovery-and-hierarchical-rl.md)
11. [Sim2Real · Humanoid RL](11-sim2real-and-humanoid-rl.md)
12. [생성모델·표현학습 기반 RL](12-generative-models-and-representation-rl.md)
13. [강화학습 리뷰와 열린 문제](13-review-and-open-problems.md)

## 과제 — 프로그래밍 프로젝트

1. [과제 1 — Imitation Learning (BC · DAgger)](hw1-imitation-learning.md) — MuJoCo 4개 환경, BC vs DAgger. Walker2d BC 11% → DAgger 96.5%
2. [과제 2 — Policy Gradients (REINFORCE · Baseline · GAE · PPO)](hw2-policy-gradients.md) — 분산 감소 4종 + PPO-Clip. HumanoidStandup λ=0.95 ≈ 12000, Reacher PPO −10 vs no-surrogate −1.6e7
3. [과제 3 — Q-Learning (DQN · Double DQN)](hw3-q-learning.md) — CartPole·BankHeist. lr=0.05에서 deadly triad 발산 관찰
4. [과제 4 — Soft Actor-Critic](hw4-soft-actor-critic.md) — entropy bonus, REINFORCE vs reparametrize(HalfCheetah ~5000), Clipped Double-Q
5. [과제 5 — Offline RL (CQL)](hw5-offline-rl.md) — Pointmass. Expert 데이터 α=0 실패 / α=0.1 성공, overestimation 억제
6. [과제 6 — Model-Based RL (MPC · CEM)](hw6-model-based-rl.md) — dynamics model + planning. CEM 2500 vs random shooting 847, H=1 붕괴
7. [과제 7 — RLHF (선호 학습)](hw7-rlhf.md) — Bradley-Terry 보상 모델 + PPO. PointMaze 사람 선호로 해결, Hopper 백플립
