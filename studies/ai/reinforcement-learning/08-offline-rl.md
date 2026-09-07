---
layout: page
title: "08. Offline RL"
permalink: /studies/ai/reinforcement-learning/08-offline-rl/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/08%20Offline%20RL.md)

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


---

이전: [07. RL 벤치마크](07-rl-benchmarks.md) · 다음: [09. Model-Based RL](09-model-based-rl.md)
