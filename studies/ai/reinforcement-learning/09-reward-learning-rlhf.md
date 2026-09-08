---
layout: page
title: "09. Reward Learning · RLHF"
permalink: /studies/ai/reinforcement-learning/09-reward-learning-rlhf/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `12` 정리·보강
- 표준 자료(IRL · RLHF · DPO 문헌) 수준으로 다듬음. 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

사람이 직접 설계하기 어려운 reward를 **data / demonstration / preference / human feedback**에서 학습. 현실 task는 무엇을 reward로 줄지 불명확하거나 reward shaping이 부작용(reward hacking)을 낳는다.

## 1. Reward는 어디서 오는가

시뮬레이션: reward가 코드(HalfCheetah = 전진 거리 − action penalty, LunarLander = 위치·각도·다리 접촉·연료·성공/실패 shaping).
현실: 목표가 모호 / 기준 충돌 / sparse success만 주면 느림 / **reward hacking** / 사람이 원하는 것 ≠ proxy reward.

## 2. Demonstration 기반 — Inverse RL

expert가 최적화했을 reward를 추정:
$$
\text{expert trajectory} \to \text{높은 reward},\qquad \text{non-expert} \to \text{낮은 reward}
$$
단점: reward가 유일하게 식별되지 않을 수 있고, reward 학습 후 다시 RL을 돌려야 함.

## 3. Preference Learning

두 trajectory(또는 response) 중 어느 쪽이 더 좋은지 사람에게 묻고, 비교 data로 reward model 학습. **Bradley–Terry**:
$$
P(\tau_A \succ \tau_B) = \frac{\exp(R_\phi(\tau_A))}{\exp(R_\phi(\tau_A)) + \exp(R_\phi(\tau_B))}
$$
loss = 사람이 고른 쪽의 확률을 높이는 cross-entropy.

## 4. RLHF

$$
\text{human feedback} \to \text{reward model } R_\phi \to \text{policy를 RL로 최적화}
$$
1. supervised fine-tuning으로 초기 policy
2. 여러 output 생성 → 사람이 preference label
3. preference data로 reward model 학습
4. policy를 reward model 기준 PPO 등으로 최적화
5. base policy에서 너무 멀어지지 않게 **KL penalty**

## 5. DPO (Direct Preference Optimization)

reward model을 따로 학습 + PPO 돌리는 대신, preference data에서 **policy를 직접** 업데이트. optimal policy와 reward의 관계를 이용해 preference loss를 policy likelihood ratio로 변환. RLHF보다 구현 단순·안정적인 경우가 많다.

## 6. 위험

preference data 편향 / reward model이 학습 data 밖에서 잘못된 점수 / policy가 reward model 허점을 찾아 **reward hacking** / human feedback은 비싸고 noisy / 여러 사람의 선호 불일치.

**실전 체크**: reward model을 별도 validation preference로 평가했는가? policy optimization 중 reward score만 오르고 실제 품질은 떨어지지 않는가? KL constraint / early stopping으로 policy drift를 막는가?

## 복습 질문

- reward 설계가 현실 task에서 어려운 이유와 reward hacking이란?
- Bradley–Terry preference 모델과 reward model 학습 loss는?
- RLHF의 절차(SFT → reward model → PPO + KL)와, DPO가 이를 어떻게 단순화하는가?
- reward learning의 대표적 실패 모드는?
{% endraw %}

---

이전: [08. AlphaGo와 MCTS](08-alphago-and-mcts.md) · 다음: [10. Skill Discovery · Hierarchical RL](10-skill-discovery-and-hierarchical-rl.md)
