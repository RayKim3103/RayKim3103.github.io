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

{% endraw %}

---

이전: [10. 고급 Model-Based RL](10-advanced-model-based-rl.md) · 다음: [12. Reward Learning](12-reward-learning.md)
