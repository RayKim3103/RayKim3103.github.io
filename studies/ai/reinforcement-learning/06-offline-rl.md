---
layout: page
title: "06. Offline RL"
permalink: /studies/ai/reinforcement-learning/06-offline-rl/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `08` 정리·보강
- 표준 자료(CQL · BCQ · IQL 문헌) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

environment와 **새로 상호작용하지 않고** static dataset $D = \{(s,a,r,s')\}$ 만으로 policy 학습. 목표는 online RL 수준의 return이지만, 새 action을 시도해 결과를 확인할 수 없다. 핵심 어려움: **dataset 밖(OOD) action의 Q가 부정확 → policy가 그걸 악용**.

## 1. 설정

behavior policy(하나일 수도, 여러 policy mixture일 수도, 보통 미지)가 수집한 transition. 학습자는 dataset만 보고 target policy 구성.

## 2. BC와의 차이 — Stitching

BC는 dataset 안 action을 그대로 모방. Offline RL은 reward·transition을 써서 dataset 조각을 **이어 붙인다**:
$$
A \to B \text{ trajectory},\quad B \to C \text{ trajectory} \;\Rightarrow\; \text{offline RL은 } A \to C \text{ 전략 구성 가능}
$$
BC는 관측된 action만 모방하므로 이런 조합 능력이 제한.

## 3. 왜 어려운가

Q-learning target의 $\max_{a'} Q(s',a')$ — 학습 안 된 OOD action이 우연히 높은 Q를 받으면:
$$
\text{OOD action} \;\to\; \text{overestimated } Q \;\to\; \text{bad policy}
$$
online RL은 그 action을 해보고 reward로 교정 가능하지만 offline은 불가.

**일반 off-policy와의 차이**: off-policy RL도 replay data를 쓰지만 필요하면 새 data를 계속 수집. offline은 dataset 고정 → distribution shift를 **직접 보정**해야.

## 4. Pessimism (보수적 접근)

- **behavior regularization**: learned policy가 behavior policy에서 너무 멀어지지 않게
- **conservative value learning**: OOD action의 Q를 낮춤
- **uncertainty penalty**: 불확실한 action에 penalty
- **advantage-weighted BC**: reward를 쓰되 BC 형태로 안정화

### CQL (Conservative Q-Learning)
$$
\text{minimize high } Q \text{ on unseen actions} \;+\; \text{fit Bellman target on dataset actions}
$$
dataset 밖 action의 Q를 낮추고 dataset action의 Q를 상대적으로 높여, policy가 OOD 과대평가를 악용하지 못하게.

## 5. 유용한 경우

실제 상호작용이 비싸거나 느림 / 로봇·자율주행처럼 실패가 위험 / 의료처럼 탐색이 윤리적으로 불가 / 과거 실험 data 재사용 / 여러 기관 dataset 활용.

## 6. 실전 체크

- dataset coverage가 목표 task에 충분한가?
- dataset이 expert / medium / random 혼합 중 무엇인가?
- policy가 behavior distribution 밖으로 나가지 않게 제약했는가?
- OOD action의 Q overestimation을 막는가? offline evaluation을 신뢰할 수 있는가?

## 복습 질문

- offline RL이 일반 off-policy RL보다 어려운 이유(OOD action)는?
- BC 대비 offline RL의 stitching 능력이란?
- pessimism 계열 접근 네 가지와, CQL이 하는 일은?
{% endraw %}

---

이전: [05. RL 벤치마크](05-rl-benchmarks.md) · 다음: [07. Model-Based RL](07-model-based-rl.md)
