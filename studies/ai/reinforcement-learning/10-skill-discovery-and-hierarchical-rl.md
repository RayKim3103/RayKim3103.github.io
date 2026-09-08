---
layout: page
title: "10. Skill Discovery · Hierarchical RL"
permalink: /studies/ai/reinforcement-learning/10-skill-discovery-and-hierarchical-rl/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `13(Skill Discovery)` + `14(Hierarchical RL)` 통합·보강
- 표준 자료(DIAYN · options framework 문헌) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**Skill discovery**: 외부 reward·task supervision 없이 유용하고 다양한 행동 skill을 학습(정보이론 기반).
**Hierarchical RL**: 긴 horizon task를 primitive action이 아니라 skill/option을 조합해 해결(사람이 "채소 씻기 → 면 삶기 → 볶기"로 계획하듯).

## 1. Skill의 의미

skill = 여러 time step에 걸친 행동 패턴(걷기, 점프, 물체 잡기). policy가 skill latent $z$ 를 조건으로:
$$
\pi(a\mid s, z)
$$
목표: 서로 다른 $z$ 가 서로 다른 **관찰 가능한** 행동을 만들도록.

## 2. 정보이론 도구

$$
H(p) = -\mathbb{E}_p[\log p(x)] \quad(\text{퍼짐; entropy↑ → exploration↑})
$$
$$
D_{\text{KL}}(p \Vert q) = \mathbb{E}_p[\log p(x) - \log q(x)]
\quad(\text{forward: mode covering / reverse: mode seeking})
$$
$$
I(x; y) = D_{\text{KL}}\big(p(x,y) \,\Vert\, p(x)p(y)\big) \quad(\text{두 변수의 의존성})
$$

## 3. MI 기반 Skill Discovery

skill latent $z$ 와 방문 state $s$ 사이의 MI를 최대화:
$$
\max\; I(z; s)
$$
서로 다른 skill이 서로 다른 state 분포를 만들면 $s$ 를 보고 $z$ 를 잘 맞힐 수 있다 → diverse skill.

**구조**:
1. skill latent $z$ sample
2. $\pi(a\mid s, z)$ 로 rollout
3. **discriminator**가 최종 state/trajectory에서 $z$ 예측
4. 예측이 잘 되도록 **intrinsic reward**
5. policy는 서로 구분되는 행동을 학습

Maximum entropy RL이 reward + entropy를 최대화하듯, skill discovery는 환경 reward 대신 **diversity·controllability**를 reward처럼 사용.

**한계**: 다양하지만 쓸모없는 skill / state coverage만 넓히면 task-relevant behavior와 어긋남 / learned skill을 새 task에 조합하는 문제 / obs가 크면 discriminator 학습 어려움.

## 4. Hierarchical RL

### long-horizon의 어려움
sparse reward에서 성공까지 우연히 도달 어려움 / primitive action sequence가 너무 김 / credit assignment / exploration space 기하급수.

### skill / option
$$
\pi_{\text{low}}(a\mid s, z),\qquad \pi_{\text{high}}(z\mid s)
$$
상위 policy가 primitive action 대신 **skill**을 선택 → action space를 추상적 decision space로 → planning horizon 축소.

### skill 획득 경로
expert data에서 / unsupervised discovery / 사람이 설계 / subtask reward / world model·skill prior. (coverage, task relevance, 비용 trade-off.)

### Skill Chaining
$$
s_0 \xrightarrow{\text{skill 1}} s_{\text{mid}} \xrightarrow{\text{skill 2}} s_{\text{goal}}
$$
좋은 skill은 종료 state가 다음 skill의 시작 조건과 맞아야(transition feasibility).

### Goal-conditioned policy
$\pi(a\mid s, g)$ — 상위 policy가 목표 $g$ 선택, 하위 policy가 그 목표까지.

### 어려움
어떤 skill abstraction이 좋은지 정의 어려움 / 상·하위 policy 동시 학습 시 **nonstationarity** / skill duration·termination 조건이 성능에 큰 영향 / 잘못된 skill set은 policy를 제한.

## 복습 질문

- $I(z;s)$ 를 최대화하는 skill discovery의 구조(policy + discriminator + intrinsic reward)는?
- entropy·KL·MI 각각이 RL에서 어떻게 쓰이는가?
- HRL이 long-horizon exploration을 줄이는 방식($\pi_{\text{high}}(z\mid s)$)과, 상·하위 동시 학습의 nonstationarity란?
- skill chaining에서 transition feasibility가 중요한 이유는?
{% endraw %}

---

이전: [09. Reward Learning · RLHF](09-reward-learning-rlhf.md) · 다음: [11. Sim2Real · Humanoid RL](11-sim2real-and-humanoid-rl.md)
