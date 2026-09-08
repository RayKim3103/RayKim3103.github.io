---
layout: page
title: "08. AlphaGo와 MCTS"
permalink: /studies/ai/reinforcement-learning/08-alphago-and-mcts/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `11` 정리·보강
- 표준 자료(AlphaGo · AlphaGo Zero 논문) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

바둑은 state space $\sim 10^{170}$, action space $19\times19$, reward는 승패 중심(sparse). **MCTS**로 탐색 폭·깊이를 줄이고, **policy network**로 탐색 폭을, **value network**로 rollout 평가를 개선한다.

## 1. 바둑의 어려움

가능한 position 수 방대 / action 후보 많음 / episode 김 / 중간 reward 거의 없음 / 제한 시간 / 승패 평가 어려움 → brute-force tree search 불가.

## 2. MCTS 네 단계

| 단계 | 설명 |
|---|---|
| **Selection** | tree 안에서 promising action을 따라 내려감 (exploitation ↔ exploration 균형, 보통 UCT/PUCT) |
| **Expansion** | 미방문 node 추가 |
| **Simulation** | 끝까지 rollout 또는 value로 평가 |
| **Backup** | win rate/value를 부모 node로 전파 |

충분히 반복 후 **visit count가 큰 action** 선택.

## 3. AlphaGo 구성

- **SL policy network**: human expert move 예측 $p(a\mid s)$
- **RL policy network**: self-play로 policy 개선
- **value network**: $V(s) \approx P(\text{win}\mid s)$

MCTS는 policy prior가 높은 action에 탐색 budget을 더 주고(큰 action space 대응), value network로 leaf를 빠르게 평가(느리고 noisy한 random rollout 완화).

## 4. AlphaGo Zero

human expert data 없이 **self-play만**. 하나의 network가 policy·value를 동시에 출력.
1. 현재 network + MCTS로 self-play game 생성
2. 각 state의 MCTS **visit distribution**을 policy target으로 저장
3. 최종 승패를 value target으로 저장
4. network를 supervised loss로 갱신
5. 더 강한 self-play 반복

MCTS가 만든 개선된 policy를 network가 따라잡는 **policy improvement 연산자**로 작동.

## 5. 일반화된 교훈

AlphaGo는 **model(규칙)이 완벽히 알려진** 환경에서 planning + learning을 결합한 성공 사례. 바둑 규칙이 정확한 simulator → MCTS가 강력. 현실 세계·로봇은 model이 불완전 → 같은 구조를 그대로 못 쓰고 **model uncertainty, sim2real** 문제가 생긴다.

## 복습 질문

- MCTS 네 단계와, 각 단계에서 무엇을 하는가?
- policy network와 value network가 MCTS의 어느 문제(폭 / 평가)를 각각 완화하는가?
- AlphaGo Zero의 self-play 학습 루프에서 policy target·value target은 무엇인가?
- AlphaGo 구조가 현실 로봇에 그대로 적용되기 어려운 이유는?
{% endraw %}

---

이전: [07. Model-Based RL](07-model-based-rl.md) · 다음: [09. Reward Learning · RLHF](09-reward-learning-rlhf.md)
