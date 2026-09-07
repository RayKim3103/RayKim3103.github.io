---
layout: page
title: "11. AlphaGo와 MCTS"
permalink: /studies/ai/reinforcement-learning/11-alphago-mcts/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/11%20AlphaGo%EC%99%80%20MCTS.md)

{% raw %}
## 핵심 요약

AlphaGo는 깊은 neural network와 Monte Carlo Tree Search를 결합해 바둑의 거대한 탐색공간을 다룬 사례이다. 바둑은 state space가 약 `10^170`으로 매우 크고 action space도 19x19이며, reward는 승패 중심이라 sparse하다. AlphaGo는 policy network로 탐색 폭을 줄이고 value network로 rollout 평가를 개선했다.

## 바둑의 어려움

- 가능한 position 수가 매우 크다.
- action 후보가 많다.
- episode가 길다.
- 중간 reward가 거의 없다.
- 한 수를 제한 시간 안에 골라야 한다.
- 누가 이기고 있는지 평가하기 어렵다.

brute-force tree search는 모든 수순을 평가해야 하므로 불가능하다.

## Pure MCTS

MCTS는 모든 game tree를 탐색하지 않고 sampling으로 좋은 action을 추정한다.

기본 아이디어:

1. 현재 state에서 tree를 시작한다.
2. 선택 규칙으로 child node를 따라간다.
3. leaf에서 rollout 또는 value estimate를 수행한다.
4. 결과를 tree 위로 backpropagation한다.
5. 충분히 반복한 뒤 visit count가 큰 action을 선택한다.

## MCTS의 네 단계

| 단계 | 설명 |
| --- | --- |
| Selection | 현재 tree 안에서 promising action을 따라 내려감 |
| Expansion | 아직 방문하지 않은 node를 추가 |
| Simulation | 끝까지 rollout하거나 value로 평가 |
| Backup | win rate 또는 value를 부모 node로 전파 |

탐색은 exploitation과 exploration을 균형 있게 해야 한다.

## AlphaGo의 핵심 구성

AlphaGo는 세 가지 학습 요소를 결합한다.

- supervised learning policy network: human expert move 예측
- reinforcement learning policy network: self-play로 policy 개선
- value network: position에서 승률 예측

MCTS는 policy network로 유망한 action을 우선 탐색하고, value network로 leaf 평가를 빠르게 수행한다.

## Policy network의 역할

policy network는 가능한 수 중 강한 후보를 좁힌다.

```text
p(a|s)
```

모든 action을 같은 비중으로 탐색하는 대신, policy prior가 높은 action에 더 많은 탐색 budget을 준다. 이는 큰 action space를 다룰 때 중요하다.

## Value network의 역할

value network는 완전한 random rollout 없이 현재 position의 승률을 예측한다.

```text
V(s) ≈ P(win | s)
```

이 덕분에 rollout이 느리거나 noisy한 문제를 줄인다.

## AlphaGo Zero 관점

AlphaGo Zero 계열은 human expert data 없이 self-play만으로 학습한다. neural network는 policy와 value를 동시에 출력하고, MCTS가 만든 개선된 policy target과 game outcome value target으로 학습한다.

반복 구조:

1. 현재 network와 MCTS로 self-play game을 생성한다.
2. 각 state에서 MCTS visit distribution을 policy target으로 저장한다.
3. 최종 승패를 value target으로 저장한다.
4. network를 supervised loss 형태로 업데이트한다.
5. 업데이트된 network로 더 강한 self-play를 반복한다.

## 일반화된 교훈

AlphaGo는 model이 완벽하게 알려진 환경에서 planning과 learning을 결합한 성공 사례이다. 바둑 규칙이 정확한 simulator 역할을 하므로 MCTS가 강력하게 작동한다. 로봇이나 현실 세계에서는 model이 완벽하지 않기 때문에 같은 구조를 그대로 쓰기 어렵고 model uncertainty와 sim2real 문제가 생긴다.

## 연결 노트

- [Model-Based RL](09-model-based-rl.md)
- [고급 Model-Based RL](10-advanced-model-based-rl.md)
- [생성모델과 표현학습 기반 RL](17-generative-models-and-representation-learning-rl.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. AlphaGo와 MCTS**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
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
- **11. AlphaGo와 MCTS**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. 고급 Model-Based RL](10-advanced-model-based-rl.md) · 다음: [12. Reward Learning](12-reward-learning.md)
