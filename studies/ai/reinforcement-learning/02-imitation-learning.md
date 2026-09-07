---
layout: page
title: "02. 모방학습"
permalink: /studies/ai/reinforcement-learning/02-imitation-learning/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/02%20%EB%AA%A8%EB%B0%A9%ED%95%99%EC%8A%B5.md)

{% raw %}
## 핵심 요약

모방학습은 expert demonstration을 이용해 policy를 학습하는 방법이다. 강화학습처럼 reward를 직접 설계하고 탐색하는 대신, expert가 어떤 state에서 어떤 action을 선택했는지 보고 supervised learning 형태로 policy를 훈련한다. 그러나 단순 behavioral cloning은 distribution shift 때문에 rollout 중 작은 실수가 누적될 수 있다.

## 문제 정의

trajectory는 다음처럼 표현된다.

```text
τ = (s0, a0, s1, a1, ..., sT)
```

policy는 state 또는 observation을 받아 action을 출력한다.

```text
π(a|s)
```

모방학습에서는 expert dataset

```text
D = {(si, ai)}
```

를 사용해 `πθ(ai|si)`가 expert action에 가까워지도록 학습한다.

## Behavioral Cloning

Behavioral cloning은 expert action을 label로 보는 supervised learning이다.

```text
minθ E(s,a)~D [L(πθ(s), a)]
```

연속 action이면 MSE를, 이산 action이면 cross entropy를 자주 쓴다.

장점은 단순하고 안정적이며 reward 설계가 필요 없다는 것이다. 단점은 expert data 분포 밖의 state를 만나면 policy가 어떻게 해야 할지 모른다는 것이다.

## Distribution Shift

BC는 expert가 방문한 state 분포에서만 학습한다. 학습된 policy가 실행 중 작은 실수를 하면 expert dataset에는 거의 없던 state로 들어간다. 그 state에서 또 잘못 행동하면 더 낯선 state로 이동한다. 이 오류가 시간에 따라 누적된다.

```text
expert data distribution ≠ learned policy rollout distribution
```

이 문제가 모방학습의 핵심 어려움이다.

## DAgger 관점

DAgger는 learned policy가 실제로 방문하는 state에서 expert label을 추가로 수집해 dataset을 확장한다. 핵심은 expert 분포가 아니라 learner가 유도하는 분포 위에서 supervised learning을 하게 만드는 것이다.

절차는 다음과 같다.

1. expert demonstration으로 초기 policy를 학습한다.
2. 현재 policy를 rollout하여 state를 수집한다.
3. 그 state들에 대해 expert action을 query한다.
4. dataset에 추가하고 policy를 재학습한다.
5. 반복한다.

## RL과의 비교

| 항목 | Imitation Learning | Reinforcement Learning |
| --- | --- | --- |
| supervision | expert action | reward |
| 탐색 | 거의 불필요 | 필수 |
| 장점 | sample-efficient, 안정적 | expert보다 나은 정책 가능 |
| 단점 | expert 품질과 coverage에 의존 | reward 설계와 탐색이 어려움 |

모방학습은 expert가 충분히 좋고 data가 넓게 수집되어 있을 때 강력하다. 반대로 expert data가 부족하거나 policy가 expert를 넘어야 한다면 RL 또는 reward learning이 필요하다.

## 로봇 조작 응용

강의는 low-cost bimanual manipulation, fine-grained manipulation, RSS 논문 사례 등을 통해 imitation learning이 로봇 제어에서 실용적으로 중요하다는 점을 보여준다. 복잡한 로봇 manipulation에서는 reward shaping보다 demonstration 수집이 더 직관적인 경우가 많다.

## 실전 체크

- expert demonstration이 충분히 다양한 초기 상태와 실패 근처 상황을 포함하는가?
- observation에 task에 필요한 정보가 모두 들어 있는가?
- action space가 사람이 제공한 action과 policy output 사이에서 일관적인가?
- rollout error가 누적되는 horizon이 긴가?
- DAgger처럼 learner distribution을 보정할 방법이 있는가?

## 연결 노트

- [강화학습 개요](01-rl-overview.md)
- [Offline RL](08-offline-rl.md)
- [Reward Learning](12-reward-learning.md)
- [Hierarchical RL](14-hierarchical-rl.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. 모방학습**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **02. 모방학습**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. 강화학습 개요](01-rl-overview.md) · 다음: [03. 정책 그래디언트 기초](03-policy-gradient-basics.md)
