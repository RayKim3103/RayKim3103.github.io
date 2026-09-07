---
layout: page
title: "08. Offline RL"
permalink: /studies/ai/reinforcement-learning/08-offline-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/08%20Offline%20RL.md)

{% raw %}
## 핵심 요약

Offline RL은 environment와 새로 상호작용하지 않고, 이미 주어진 static dataset만으로 policy를 학습하는 문제이다. 목표는 online RL과 같이 높은 return을 얻는 것이지만, 학습 중 새로운 action을 시도해 결과를 확인할 수 없다. 핵심 어려움은 dataset 밖 action에 대한 Q값이 부정확해지고 policy가 그 OOD action을 악용하는 것이다.

## Offline RL 설정

Offline dataset은 behavior policy가 수집한 transition들의 집합이다.

```text
D = {(s, a, r, s')}
```

behavior policy는 하나일 수도 있고 여러 policy의 mixture일 수도 있으며, 보통 정확히 모른다. 학습자는 dataset만 보고 target policy를 만든다.

## Behavioral Cloning과의 차이

BC는 dataset 안 expert action을 그대로 따라 한다. Offline RL은 reward와 transition을 사용해 dataset 조각들을 이어 붙이는 stitching을 할 수 있다.

예를 들어 data에 `A -> B`와 `B -> C` trajectory가 따로 있으면, offline RL은 reward와 value를 통해 `A -> C` 전략을 구성할 수 있다. BC는 단순히 관측된 action을 모방하므로 이런 조합 능력이 제한된다.

## 왜 어려운가

Online RL은 policy가 새로운 action을 해보고 reward를 확인할 수 있다. Offline RL은 그럴 수 없다. 따라서 policy가 dataset에 거의 없는 action을 선택하면 그 action이 좋은지 나쁜지 알 방법이 없다.

Q-learning을 그대로 쓰면 target에 `maxa Q(s',a)`가 들어간다. 학습되지 않은 OOD action이 우연히 높은 Q를 받으면 policy가 그 action을 선택하고, 학습이 망가진다.

```text
OOD action -> overestimated Q -> bad policy
```

## 일반 off-policy RL과 차이

Off-policy RL도 replay buffer data를 사용하지만, 필요하면 새 data를 계속 수집한다. Offline RL은 dataset이 고정되어 있으므로 distribution shift를 직접 보정해야 한다.

## 보수적 가치 추정

Offline RL의 많은 방법은 pessimism을 사용한다. dataset에 없는 action의 value를 낮게 보거나, policy가 dataset support 안에 머물도록 제약한다.

대표 접근:

- behavior regularization: learned policy가 behavior policy에서 너무 멀어지지 않게 함
- conservative value learning: OOD action의 Q값을 낮춤
- uncertainty penalty: 불확실한 action에 penalty 부여
- advantage-weighted BC: reward 정보를 쓰되 BC 형태로 안정화

## CQL

Conservative Q-Learning은 dataset 밖 action의 Q값을 낮추고 dataset action의 Q값을 상대적으로 높이는 방향으로 학습한다.

직관적으로는

```text
minimize high Q on unseen actions
fit Bellman target on dataset actions
```

을 동시에 수행한다. 이를 통해 policy가 OOD action의 과대평가를 악용하지 못하게 한다.

## Offline RL이 유용한 경우

- 실제 환경 상호작용이 비싸거나 느림
- 로봇이나 자율주행처럼 실패가 위험함
- 의료처럼 탐색 자체가 윤리적으로 어렵거나 불가능함
- 과거 실험 data를 재사용하고 싶음
- 여러 기관이나 시스템이 수집한 dataset을 활용하고 싶음

## 실전 체크

- dataset coverage가 목표 task에 충분한가?
- dataset이 expert data인지, medium policy인지, random data가 섞였는지 확인했는가?
- policy가 behavior distribution 밖으로 나가지 않도록 제약했는가?
- OOD action에 대한 Q overestimation을 막는가?
- offline evaluation이 신뢰 가능한가?

## 연결 노트

- [모방학습](02-imitation-learning.md)
- [Q-learning](05-q-learning.md)
- [Off-policy RL](06-off-policy-rl.md)
- [Reward Learning](12-reward-learning.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. Offline RL**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
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
- **08. Offline RL**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. RL 벤치마크](07-rl-benchmarks.md) · 다음: [09. Model-Based RL](09-model-based-rl.md)
