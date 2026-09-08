---
layout: page
title: "05. RL 벤치마크와 평가"
permalink: /studies/ai/reinforcement-learning/05-rl-benchmarks/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `07` 정리·보강
- 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

RL 알고리즘은 **최종 성능만으로 비교 불가**. 같은 final return이라도 필요한 environment step이 다르면 sample efficiency가 다르다. seed variance도 크다.

## 1. 두 축

| 지표 | 의미 |
|---|---|
| **asymptotic performance** | 충분히 학습했을 때 도달하는 최종 성능 |
| **sample efficiency** | 적은 step으로 얼마나 빨리 높은 성능에 |

learning curve: x축 = training steps(또는 env interactions), y축 = evaluation return. 어떤 알고리즘은 최종 성능은 높지만 sample이 많이 필요, 다른 건 초반은 빠르지만 최종은 낮음.

## 2. Benchmark 종류

**Continuous control** (로봇 제어·motor control): DeepMind Control Suite, Gym/Gymnasium MuJoCo, BSuite, robosuite, RLBench, Meta-World, LIBERO, FrankaKitchen, FurnitureBench, HumanoidBench.

**Discrete control** (value-based·exploration): Atari(pixel + discrete), MiniGrid(sparse reward·exploration), Procgen(generalization), Crafter, Minecraft 계열.

각 benchmark가 보는 능력: 기본 locomotion / 고차원 robotic 제어 / long-horizon 계획·sparse reward / unseen 환경 generalization / open-ended embodied learning.

## 3. 사용 시 주의 (실험 신뢰성)

- **여러 random seed** 평균 + 신뢰구간을 보는가? (하나의 curve만 보면 위험)
- evaluation 시 **exploration noise를 껐는가**?
- **environment step 수 vs gradient update 수**를 구분했는가?
- observation setting(state vs pixel)이 명확한가?
- reward normalization, action repeat, frame stack 같은 세부가 동일한가?

## 4. "A가 B보다 좋다" 를 말하려면

적어도 무엇이 좋은지 분명히: 더 높은 final return / 더 빠른 학습 / 더 안정적인 seed 성능 / 더 적은 hyperparameter tuning / 더 넓은 task에서 일관 / sim2real·unseen transfer가 더 좋음.

## 복습 질문

- asymptotic performance와 sample efficiency의 차이, learning curve에서 각각 어떻게 보이는가?
- continuous vs discrete benchmark가 각각 평가하는 능력은?
- RL 실험 결과를 읽을 때 확인해야 할 신뢰성 항목 다섯 가지는?
{% endraw %}

---

이전: [04. Q-learning과 Off-policy RL](04-q-learning-and-off-policy.md) · 다음: [06. Offline RL](06-offline-rl.md)
