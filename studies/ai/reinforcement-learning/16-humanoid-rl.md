---
layout: page
title: "16. Humanoid RL"
permalink: /studies/ai/reinforcement-learning/16-humanoid-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/16%20Humanoid%20RL.md)

{% raw %}
## 핵심 요약

Humanoid RL은 사람형 로봇의 locomotion, manipulation, whole-body control을 강화학습으로 해결하는 분야이다. PPO 같은 on-policy 알고리즘이 locomotion에서 널리 쓰이며, motion tracking과 human motion prior를 결합하면 더 복잡한 행동을 학습할 수 있다. 하지만 탐색 비효율, task reward와 imitation reward의 충돌, sim2real gap이 주요 문제이다.

## Humanoid 제어의 범위

강의는 humanoid AI를 다음 영역으로 나누어 본다.

- locomotion: 걷기, 달리기, parkour
- manipulation: 손과 팔을 이용한 물체 조작
- whole-body control: 이동과 조작을 동시에 수행
- human-object interaction: 사람 동작과 물체 상호작용 모방

## PPO 기반 locomotion

humanoid locomotion에서는 PPO가 강력한 baseline이다. reward는 보통 다음 항을 조합한다.

- command following
- smooth motion
- alive bonus
- energy penalty
- safety constraint
- 불필요한 움직임 제한

이런 reward shaping은 안정적인 보행에는 좋지만, 매우 다양한 동작으로 확장하기 어렵다.

## 다양한 skill 훈련

parkour나 rough terrain locomotion처럼 복잡한 동작은 다양한 skill을 같은 recipe로 학습해야 한다. state-based teacher를 먼저 학습하고, vision-based student로 distillation하는 구조도 등장한다.

```text
state-based teacher -> distill -> vision-based student
```

teacher는 privileged state를 사용해 쉽게 학습하고, student는 실제 배포 가능한 sensor observation으로 행동한다.

## Human Motion Prior

AMASS 같은 motion capture dataset은 사람 움직임의 prior를 제공한다. RL policy는 reference motion과 비슷하게 움직이도록 reward를 받으면서 task도 수행한다.

```text
reward = task reward + motion similarity - energy/safety penalty
```

이 접근은 자연스러운 동작과 효율적 탐색에 도움이 된다.

## Motion Tracking RL

reference motion이 있을 때 policy는 현재 pose와 target pose의 차이를 줄이도록 학습한다. 이후 새 task에서는 human prior를 유지하면서 task reward를 최적화한다.

장점:

- raw action exploration보다 효율적이다.
- 사람 같은 움직임을 만들기 쉽다.
- 복잡한 초기 행동 repertoire를 제공한다.

단점:

- reference motion이 task와 충돌할 수 있다.
- imitation에 너무 묶이면 task 최적화가 제한된다.
- motion dataset coverage 밖 동작은 어렵다.

## Whole-body manipulation

humanoid manipulation은 locomotion보다 더 어렵다. balance, hand dexterity, contact dynamics, perception을 동시에 해결해야 한다. 물체를 잡고 옮기는 task에서는 손, 팔, 몸통, 다리의 coordination이 필요하다.

## Sim2Real 관점

humanoid policy는 simulation에서 잘 되어도 real robot에서 실패하기 쉽다.

- 관절 마찰과 actuator dynamics 차이
- 발 접촉과 slip
- sensor delay
- model mass distribution 오차
- 안전 constraint

따라서 domain randomization, teacher-student distillation, online adaptation이 중요하다.

## 연결 노트

- [Actor-Critic과 PPO](04-actor-critic-ppo.md)
- [Hierarchical RL](14-hierarchical-rl.md)
- [Sim2Real Transfer](15-sim2real-transfer.md)
- [생성모델과 표현학습 기반 RL](17-generative-models-and-representation-learning-rl.md)

{% endraw %}

---

이전: [15. Sim2Real Transfer](15-sim2real-transfer.md) · 다음: [17. 생성모델과 표현학습 기반 RL](17-generative-models-and-representation-learning-rl.md)
