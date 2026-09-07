---
layout: page
title: "09. Model-Based RL"
permalink: /studies/ai/reinforcement-learning/09-model-based-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/09%20Model-Based%20RL.md)

{% raw %}
## 핵심 요약

Model-based RL은 environment dynamics나 reward model을 학습하거나 알고 있다고 가정하고, 그 model을 이용해 planning 또는 policy improvement를 수행한다. model-free RL이 실제 environment sample에 크게 의존한다면, model-based RL은 imaginary rollout을 통해 sample efficiency를 높이려 한다.

## model의 의미

강화학습에서 model은 보통 다음을 의미한다.

```text
p(st+1 | st, at)
r(st, at)
```

즉 action을 취했을 때 다음 state와 reward가 어떻게 생기는지 예측하는 transition dynamics와 reward function이다.

로봇이 바나나를 던지는 예에서는 state가 로봇과 바나나 pose, action이 joint control, model이 실제 물리 법칙, reward가 바구니에 들어갔는지 여부에 해당한다.

## model을 알면 무엇이 가능한가

model을 알고 있으면 실제 환경에서 위험하게 시도하지 않고도 여러 action sequence를 상상해 볼 수 있다.

```text
s_t -> a_t -> s_t+1 -> a_t+1 -> ... -> s_t+H
```

이 imaginary rollout을 통해 누적 reward가 큰 action sequence를 선택하거나, model-generated data를 policy 학습에 사용할 수 있다.

## Planning

planning은 현재 state에서 가능한 미래 action sequence를 평가해 가장 좋은 첫 action을 고르는 방식이다.

```text
a*0:H = arg maxa0:H Σt=0,H r(st,at)
```

실제 실행은 첫 action만 하고 다음 state에서 다시 planning한다. 이를 receding horizon control 또는 model predictive control 관점으로 볼 수 있다.

## Random shooting과 CEM

가장 단순한 planning은 여러 action sequence를 random sample하고, model로 rollout하여 return이 큰 sequence를 고르는 것이다.

Cross-Entropy Method는 좋은 sequence들을 elite set으로 뽑고, 그 분포를 갱신해 더 좋은 sequence를 반복적으로 sample한다.

절차:

1. action sequence 분포를 초기화한다.
2. 여러 sequence를 sample한다.
3. model로 return을 평가한다.
4. 상위 elite sequence를 고른다.
5. elite의 평균과 분산으로 분포를 갱신한다.
6. 반복 후 가장 좋은 sequence의 첫 action을 실행한다.

## model learning

model이 주어지지 않으면 dataset으로 neural network dynamics model을 학습한다.

```text
minφ Σ ||fφ(st,at) - st+1||^2
```

reward도 모르면 reward model을 함께 학습한다.

model error는 horizon이 길어질수록 누적된다. 그래서 긴 full trajectory보다 짧은 rollout을 자주 사용하는 전략이 흔하다.

## Model-based RL의 장점과 위험

장점:

- 실제 sample을 덜 쓰므로 sample-efficient할 수 있다.
- planning을 통해 현재 state에 맞는 action을 바로 계산할 수 있다.
- model을 분석하면 task 구조를 이해하기 쉽다.

위험:

- model bias가 policy를 잘못된 방향으로 이끌 수 있다.
- model rollout이 길수록 prediction error가 누적된다.
- 학습된 model이 dataset 밖 state-action에서 부정확할 수 있다.
- planning 비용이 커질 수 있다.

## 연결 노트

- [고급 Model-Based RL](10-advanced-model-based-rl.md)
- [AlphaGo와 MCTS](11-alphago-mcts.md)
- [Sim2Real Transfer](15-sim2real-transfer.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Model-Based RL**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- RL 알고리즘은 update target, rollout policy, bootstrap 여부, exploration 방법을 표로 비교하면 구조가 잡힌다.
- return curve는 seed별 분산과 environment stochasticity를 함께 확인해야 한다.
- value-based, policy-gradient, actor-critic 방법은 Bellman 관점과 trajectory likelihood 관점의 조합이다.
- on-policy와 off-policy의 차이는 sample을 모으는 policy와 학습하려는 policy가 같은지에서 출발한다.
- reward 설계, exploration, distribution shift가 성능과 안정성을 좌우하므로 실험 해석이 조심스럽다.

### 문제 풀이 또는 구현 루틴

- state, action, transition, reward, horizon, discount로 문제를 먼저 쪼갠다.
- 업데이트 식에서는 target, bootstrap 여부, importance sampling 여부를 표시한다.
- benchmark 결과는 평균 return뿐 아니라 variance, sample efficiency, seed sensitivity를 함께 본다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 높은 training return이 robust policy를 의미하지 않을 수 있다.
- discount factor는 장기 보상과 variance를 조절하는 설계 선택이다.
- offline RL에서는 dataset 밖 action을 과신하면 extrapolation error가 커진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 알고리즘은 어디에서 bias를 넣고 어디에서 variance를 줄이는가?
- 환경과 reward가 조금 바뀌면 policy가 어떻게 무너질 수 있는가?
- 탐험을 늘리는 선택이 sample efficiency와 안정성에 어떤 비용을 만드는가?
- **09. Model-Based RL**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Offline RL](08-offline-rl.md) · 다음: [10. 고급 Model-Based RL](10-advanced-model-based-rl.md)
