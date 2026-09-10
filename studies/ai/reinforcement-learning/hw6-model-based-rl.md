---
layout: page
title: "과제 6 — Model-Based RL (Dynamics Model · MPC · Random Shooting · CEM)"
permalink: /studies/ai/reinforcement-learning/hw6-model-based-rl/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW6](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW6) (`scripts/run_hw6.py`, `agents/model_based_agent.py`)
- CAS4160 · Homework 6 (CS285 model-based RL 기반)
- 관련 강의 노트: [07. 모델 기반 강화학습](07-model-based-rl.md)

{% raw %}
## 개요

**dynamics model**을 학습하고, 그 model로 **plan**(action 최적화)하여 명시적 policy 없이 행동을 선택. MBRL = (1) dynamics 학습 + (2) 학습된 model로 reward 최대화 planning.

### 구현 대상 (TODO)
`run_hw6.py` 학습 루프 전체 → `model_based_agent.get_loss()` → random-shooting / CEM planner → MPC(매 step replan) → on-policy data collection 루프 → ensemble 평균.

### 알고리즘

**Dynamics model** — 정규화된 **state 차분**을 예측 (다음 state를 직접 예측하지 않음):
$$
L(\theta) = \mathbb{E}_{(s_t,a_t,s_{t+1})\sim D}\Big[\big\lVert \text{Normalize}(s_{t+1}-s_t) - f_\theta(s_t,a_t)\big\rVert_2^2\Big],\qquad
F_\theta(s,a) = s + \text{Unnormalize}(f_\theta(s,a))
$$

**Planning** — 무한·open-loop 최적화는 불가능(model 오차 누적) → 유한 horizon $$H$$, 유한 후보 $$K$$:
$$
A^* = \arg\max_{A^{(0)},\dots,A^{(K-1)}} \sum_{t'=t}^{t+H-1} r(\hat s_{t'}, a_{t'}),\qquad \hat s_{t'+1} = F_\theta(\hat s_{t'}, a_{t'})
$$

| 방법 | 절차 |
|---|---|
| **Random shooting** | $$K$$개 무작위 action 시퀀스 → model rollout → reward 최대 시퀀스 선택 |
| **CEM** | 반복: $$K$$개 샘플 → 상위 $$J$$개 **elite** → elite의 평균·분산으로 diagonal Gaussian fit → 다음 iteration 샘플 분포. $$M$$회 후 최종 평균을 action으로 |
| **MPC** | 매 timestep planning → **첫 action만 실행** → 다음 state로 replan (누적 오차 완화) |

- **On-policy 수집**: MBRL은 이론상 off-policy지만, random 데이터만으론 관심 영역의 state 공간을 못 덮음 → Algorithm 1로 model rollout·환경 실행을 번갈아 $$D$$ 확장.
- **Ensemble**: $$N$$개 독립 초기화 network $$\{f^i_\theta\}$$ → 후보마다 $$N$$개 rollout 평균으로 평가.

---

## 결과 (student report, HalfCheetah)

### Dynamics model 학습 (`halfcheetah_0_iter`)
model loss가 iteration 500까지 **0.2 미만**으로 하강 (0.68 → ~0.05).

### Random Shooting vs CEM
| 방법 | Mean Return | Std |
|---|---:|---:|
| Random shooting | 847.05 | 54.77 |
| **CEM** | **2500.27** | 50.19 |

→ CEM이 elite 시퀀스로 sampling 분포를 반복 개선 → random shooting 대비 return **약 3배**, 안정성은 동등하거나 약간 우위.

### CEM 하이퍼파라미터 (원래 H=15, K=1000)
| 설정 | Mean Return | Std |
|---|---:|---:|
| **원본 (H=15, K=1000)** | **2500.27** | 50.19 |
| H = 1 | **−15250.87** | 11181.71 |
| K = 50 | 1302.09 | 61.47 |

→ **H=1**: 미래를 못 내다봐 model 오차 누적 → return 붕괴·분산 폭증. **K=50**: 후보 부족 → 탐색력 저하로 최적 시퀀스 못 찾음(원본의 절반 이하). 충분한 **planning horizon $$H$$** 와 **후보 수 $$K$$** 가 MPC 성능의 핵심.

## 핵심 정리

- Dynamics model은 정규화된 **차분** $$s_{t+1}-s_t$$ 를 예측 (스케일·수치안정). $$F_\theta(s,a) = s + \text{Unnorm}(f_\theta)$$.
- 유한 horizon planning으로 model 오차 누적 방지; MPC(첫 action만 실행 + replan)로 추가 보정.
- CEM > random shooting (2500 vs 847): elite refit이 곧 반복적 분포 최적화.
- H, K 둘 다 성능에 결정적: H=1 → −15250(붕괴), K=50 → 1302.
- Ensemble + on-policy 수집이 model 편향과 coverage 부족을 완화.

## 복습 질문

- 왜 다음 state 대신 (정규화된) state 차분을 예측하나? $$F_\theta$$ 정의를 쓰라.
- random shooting과 CEM의 절차 차이, 그리고 CEM이 2500 vs 847로 이기는 이유는?
- MPC에서 첫 action만 실행하고 replan하는 이유(무엇을 완화)는?
- CEM에서 H=1이 −15250으로 붕괴하고 K=50이 1302로 반토막 나는 이유를 각각 설명하라.
- MBRL이 이론상 off-policy인데 on-policy 데이터 수집이 필요한 이유는?
{% endraw %}

---

이전 과제: [과제 5 — Offline RL](hw5-offline-rl.md) · 다음 과제: [과제 7 — RLHF / 선호 학습](hw7-rlhf.md)
