---
layout: page
title: "13. Skill Discovery"
permalink: /studies/ai/reinforcement-learning/13-skill-discovery/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/13%20Skill%20Discovery.md)

{% raw %}
## 핵심 요약

Skill discovery는 외부 reward나 명시적 task supervision 없이 유용하고 다양한 행동 skill을 학습하는 문제이다. agent가 어떤 task를 해야 할지 모르는 상황에서도 나중에 재사용 가능한 temporally extended behavior를 발견하려는 접근이다. 강의는 entropy, KL divergence, mutual information 같은 정보이론 개념을 바탕으로 skill discovery를 설명한다.

## 왜 필요한가

기존 RL은 보통 잘 정의된 reward와 task가 있다. 하지만 실제 환경에서는 다음 문제가 있다.

- 다양한 task reward를 사람이 모두 설계하기 어렵다.
- long-horizon task는 primitive action만으로 탐색하기 어렵다.
- 사전 훈련된 skill이 있으면 새 task를 더 빠르게 배울 수 있다.
- zero supervision 상황에서도 agent가 환경을 탐색하고 능력을 축적해야 한다.

## skill의 의미

skill은 여러 time step에 걸쳐 지속되는 행동 패턴이다. 예를 들어 걷기, 점프, 물체 잡기, 문 열기 같은 행동이 skill이 될 수 있다.

정책은 skill latent `z`를 조건으로 받을 수 있다.

```text
π(a|s,z)
```

목표는 서로 다른 `z`가 서로 다른 관찰 가능한 행동을 만들도록 학습하는 것이다.

## Entropy

entropy는 분포가 얼마나 넓게 퍼져 있는지 나타낸다.

```text
H(p(x)) = -E[p(x)] log p(x)
```

항상 같은 값만 나오면 entropy는 낮고, 여러 결과가 고르게 나오면 entropy가 높다. RL에서는 entropy를 높이면 exploration이 증가한다.

## KL divergence

KL divergence는 두 분포가 얼마나 다른지 측정한다.

```text
DKL(p||q) = E[p(x)] [log p(x) - log q(x)]
```

forward KL은 mode covering 성향을, reverse KL은 mode seeking 성향을 가질 수 있다. 이 차이는 policy learning과 representation learning에서 중요하다.

## Mutual Information

mutual information은 두 random variable이 얼마나 의존적인지 측정한다.

```text
I(x;y) = DKL(p(x,y) || p(x)p(y))
```

skill discovery에서는 skill latent `z`와 visited state `s` 사이의 MI를 크게 만들려 한다.

```text
maximize I(z; s)
```

서로 다른 skill이 서로 다른 state distribution을 만들면 `s`를 보고 어떤 `z`가 실행됐는지 잘 맞힐 수 있다. 이것이 diverse skill을 만든다.

## MI 기반 skill discovery

대표 구조:

1. skill latent `z`를 sample한다.
2. policy `π(a|s,z)`로 rollout한다.
3. discriminator가 최종 state 또는 trajectory에서 `z`를 예측한다.
4. 예측이 잘 되도록 intrinsic reward를 준다.
5. policy는 서로 구분되는 행동을 학습한다.

직관적으로 agent는 “내가 어떤 skill을 실행했는지 관찰 결과만 보고 맞힐 수 있게” 행동한다.

## Maximum Entropy RL과의 연결

Maximum entropy RL은 reward와 entropy를 함께 최대화한다. Skill discovery도 환경 reward 대신 diversity와 controllability를 reward처럼 사용한다. 즉 외부 task reward가 없어도 intrinsic objective로 행동 레퍼토리를 넓힌다.

## 한계

- 다양하지만 쓸모없는 skill이 나올 수 있다.
- state coverage만 넓히면 task-relevant behavior와 어긋날 수 있다.
- learned skill을 새 task에 어떻게 조합할지 별도 문제가 남는다.
- observation space가 크면 discriminator와 representation 학습이 어렵다.

## 연결 노트

- [Reward Learning](12-reward-learning.md)
- [Hierarchical RL](14-hierarchical-rl.md)
- [생성모델과 표현학습 기반 RL](17-generative-models-and-representation-learning-rl.md)

{% endraw %}

---

이전: [12. Reward Learning](12-reward-learning.md) · 다음: [14. Hierarchical RL](14-hierarchical-rl.md)
