---
layout: page
title: "10. 고급 Model-Based RL"
permalink: /studies/ai/reinforcement-learning/10-advanced-model-based-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/10%20%EA%B3%A0%EA%B8%89%20Model-Based%20RL.md)

{% raw %}
## 핵심 요약

고급 model-based RL은 학습된 model을 policy optimization, value learning, latent dynamics learning과 결합한다. 핵심은 실제 환경 data와 model-generated imaginary rollout을 섞어 sample efficiency를 높이되, model error가 누적되지 않도록 rollout 길이와 latent 표현을 조절하는 것이다.

## Model-Based Policy Optimization

기본 아이디어는 실제 data에 model로 만든 rollout을 추가하는 것이다.

```text
Denv -> train model
Dmodel <- imaginary rollouts from learned model
policy/critic update using Denv and Dmodel
```

절차:

1. 현재 policy로 환경에서 data를 수집한다.
2. `Denv`에 transition을 저장한다.
3. `Denv`로 dynamics model을 학습한다.
4. `Denv`의 여러 state에서 짧은 imaginary rollout을 생성한다.
5. `Dmodel`을 policy 또는 critic update에 사용한다.

## rollout 길이 선택

긴 rollout은 더 많은 synthetic data를 주지만 model error가 누적된다. 짧은 rollout은 안정적이지만 먼 미래 효과를 충분히 반영하지 못할 수 있다. 그래서 실제 state에서 시작하는 짧은 partial rollout을 많이 만드는 전략이 자주 쓰인다.

## Dyna 관점

Dyna는 model-free update와 model-based planning update를 섞는 고전적 구조이다.

1. 실제 environment step으로 transition을 얻는다.
2. Q-learning update를 수행한다.
3. model을 업데이트한다.
4. model에서 simulated transition을 여러 개 만들어 추가 Q update를 한다.

즉 실제 경험과 상상 경험을 모두 학습에 사용한다.

## Dreamer

Dreamer 계열은 pixel observation을 직접 예측하기보다 latent state dynamics를 학습하고, latent imagination 안에서 actor-critic을 학습한다.

중요 구성:

- representation model: observation을 latent state로 인코딩
- dynamics model: latent transition 예측
- reward/value model: latent에서 reward와 value 예측
- actor: imagined trajectory에서 return을 크게 만드는 action 선택

장점은 고차원 observation에서도 planning과 policy learning을 효율적으로 할 수 있다는 것이다.

## TD-MPC

TD-MPC는 latent dynamics, temporal difference learning, model predictive control을 결합한다. representation은 control에 필요한 정보를 담도록 학습되고, planning은 latent model 안에서 수행된다.

핵심 관점:

- raw observation보다 control-relevant latent가 중요하다.
- model은 pixel reconstruction보다 value와 reward 예측에 유용해야 한다.
- MPC는 현재 state마다 action sequence를 다시 최적화해 robustness를 높인다.

## Advanced MBRL의 공통 문제

- model exploitation: policy가 model의 오류를 이용해 실제로는 나쁜 행동을 선택
- uncertainty estimation: model이 모르는 영역을 구분해야 함
- long-horizon credit: 상상 rollout이 길어질수록 불확실성 증가
- representation learning: 예측에 필요한 정보와 제어에 필요한 정보가 다를 수 있음

## 연결 노트

- [Model-Based RL](09-model-based-rl.md)
- [AlphaGo와 MCTS](11-alphago-mcts.md)
- [생성모델과 표현학습 기반 RL](17-generative-models-and-representation-learning-rl.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. 고급 Model-Based RL**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
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
- **10. 고급 Model-Based RL**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. Model-Based RL](09-model-based-rl.md) · 다음: [11. AlphaGo와 MCTS](11-alphago-mcts.md)
