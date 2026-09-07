---
layout: page
title: "01. 강화학습 개요"
permalink: /studies/ai/reinforcement-learning/01-rl-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/01%20%EA%B0%95%ED%99%94%ED%95%99%EC%8A%B5%20%EA%B0%9C%EC%9A%94.md)

## 핵심 요약

강화학습은 agent가 environment와 상호작용하면서 누적 reward를 최대화하는 행동 정책을 배우는 문제이다. 지도학습처럼 정답 label을 직접 받는 것이 아니라, 행동의 결과로 돌아오는 reward와 다음 state를 통해 무엇이 좋은 행동인지 간접적으로 배운다. 이 과목은 imitation learning, model-free RL, model-based RL, offline RL, reward learning, skill discovery, hierarchical RL, sim2real, humanoid control, generative model 기반 RL을 폭넓게 다룬다.

## 강화학습 문제

기본 구성요소는 다음과 같다.

| 구성요소 | 의미 |
| --- | --- |
| agent | 행동을 선택하는 학습 주체 |
| environment | 행동의 결과를 만들어내는 외부 세계 |
| state `s` | 현재 상황 정보 |
| action `a` | agent가 선택하는 제어 입력 |
| reward `r` | 행동 결과에 대한 피드백 |
| policy `π(a|s)` | state에서 action을 선택하는 규칙 |

강화학습은 순차 의사결정 문제이다.

```text
s0, a0, r0, s1, a1, r1, ...
```

한 번의 행동이 다음 state와 이후 가능한 보상에 영향을 주기 때문에, 단기 보상만 보고 행동하면 전체 성능이 나빠질 수 있다.

## 이 과목의 범위

강의는 모든 RL 세부 분야를 다루기보다 핵심 개념과 deep RL 구현, robotics와 control 응용, 고급 주제를 연결하는 데 초점을 둔다.

- imitation learning
- policy gradient
- actor-critic과 PPO
- Q-learning과 off-policy RL
- RL benchmark와 평가 방법
- offline RL
- model-based RL과 AlphaGo
- reward learning과 RLHF
- skill discovery와 hierarchical RL
- sim2real transfer와 humanoid RL
- generative model 및 representation learning for RL

## 지도학습과의 차이

지도학습은 정답 `y`가 주어지고 `x -> y` mapping을 학습한다. 강화학습은 특정 state에서 어떤 action이 정답인지 바로 알기 어렵다. 행동을 수행한 뒤 reward를 보고, 그 reward도 즉시 나오지 않을 수 있다.

핵심 차이는 다음과 같다.

- data가 i.i.d.가 아니다.
- agent의 policy가 수집되는 data distribution을 바꾼다.
- reward가 sparse하거나 delayed일 수 있다.
- exploration이 필요하다.
- 현재 행동의 책임을 미래 reward에 어떻게 배분할지 credit assignment가 필요하다.

## 왜 어려운가

강화학습은 재미있지만 구현과 실험이 어렵다. 강의는 특히 deep RL이 많은 trick에 의존한다는 점을 강조한다.

- noisy policy gradient
- bootstrapping으로 인한 불안정성
- distribution shift
- value overestimation
- exploration 실패
- importance sampling variance
- long-horizon credit assignment

## 응용 영역

강화학습은 게임, 로보틱스, 언어모델 정렬, 교육, chip design 등 다양한 영역에 쓰인다. 특히 로봇 제어에서는 simulation에서 많은 trial을 수행하고, real world에서는 안전하고 sample-efficient하게 배운 정책을 transfer하는 것이 중요하다.

## 과목을 읽는 순서

```mermaid
flowchart TD
  A["RL 문제 정의"] --> B["Imitation Learning"]
  A --> C["Policy Gradient"]
  C --> D["Actor-Critic / PPO"]
  D --> E["Q-learning / Off-policy"]
  E --> F["Offline RL"]
  E --> G["Model-Based RL"]
  G --> H["AlphaGo / MCTS"]
  A --> I["Reward Learning / RLHF"]
  A --> J["Skill Discovery / Hierarchical RL"]
  J --> K["Sim2Real / Humanoid RL"]
  K --> L["Generative Models for RL"]
```

## 연결 노트

- [모방학습](02-imitation-learning.md)
- [정책 그래디언트 기초](03-policy-gradient-basics.md)
- [Q-learning](05-q-learning.md)
- [강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)


---

다음: [02. 모방학습](02-imitation-learning.md)
