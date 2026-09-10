---
layout: page
title: "과제 1 — Imitation Learning (BC · DAgger)"
permalink: /studies/ai/reinforcement-learning/hw1-imitation-learning/
sitemap: false
---

- **원본 코드**: [GitHub — Reinforcement Learning / HW1](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning/HW1) (`cas4160/` 패키지, `run_hw1.py`, `bc_trainer.py`, `MLP_policy.py`)
- CAS4160 Introduction to Artificial Intelligence · Homework 1 (Berkeley **CS285** HW1 기반)
- 관련 강의 노트: [02. 모방학습](02-imitation-learning.md)

{% raw %}
## 개요

**Behavioral Cloning(BC)**과 **DAgger**를 직접 구현하고 MuJoCo 연속제어 4개 환경에서 비교. gym 환경 튜토리얼(`step()`/`reset()`/`action_space`)로 시작.

### 구현 대상 (TODO)
`pytorch_util.build_mlp` → `MLP_policy.MLPPolicySL`(get_action / forward / update) → `utils.sample_trajectory(ies)` → `replay_buffer` → `bc_agent.BCAgent` → `bc_trainer.BCTrainer`(run_training_loop / collect_training_trajectories / train_agent / **do_relabel_with_expert**(DAgger) / perform_logging).

### 알고리즘

**BC**: expert 데이터 $$D = \{(s_i, a_i)\}$$ 로 supervised 학습.
$$
\min_\theta \sum_i \lVert \pi_\theta(s_i) - a_i \rVert^2 \quad(\text{연속 action})
$$

**DAgger**: (1) 현재 $$\pi_\theta$$ 로 rollout → state 수집 (2) 그 state들에 **expert policy로 action relabel** (`do_relabel_with_expert`) (3) $$D$$ 에 추가 (4) 재학습. → learner가 실제로 방문하는 분포 위에서 학습해 **compounding error** 해결.

---

## 결과 (student report, `ep_len=1000`, `eval_batch_size=10000`, else default: MLP 2×64, lr 5e-3)

### BC

| 환경 | BC 성능 (Mean ± Std) | Expert 평균 | Eval 평균 episode 길이 |
|---|---:|---:|---:|
| Ant-v4 | **4575.17 ± 74.49** | 4713.65 | 1000.0 |
| HalfCheetah-v4 | 3817.51 ± 113.38 | 4205.78 | 1000.0 |
| Hopper-v4 | 860.78 ± 246.66 | 3772.67 | **247.4** (조기 종료) |
| Walker2d-v4 | 602.29 ± 538.70 | 5566.85 | **305.5** (조기 종료) |

→ 4족(Ant, HalfCheetah)은 BC로도 expert의 ~90–97% 도달. 2족(Hopper, Walker2d)은 **불안정** — 작은 이탈이 넘어짐·조기 종료로 이어져 return이 급락.

### Hyperparameter — `--num_agent_train_steps_per_iter` (Walker2d-v4)

| gradient steps | 1000 | 2000 | 3000 | 4000 | 5000 |
|---|---:|---:|---:|---:|---:|
| Eval AverageReturn | 602.3 | 1394.8 | 2199.0 | 3132.7 | **4226.8** |

→ steps↑ → underfitting 완화; **5000 초과 시 return이 다시 감소**(overfitting).

### DAgger (10 iterations)

| 환경 | BC | DAgger (10 iter) | Expert |
|---|---:|---:|---:|
| Ant-v4 | 4575.17 | **4749.1** (~101% of expert) | 4713.65 |
| Walker2d-v4 | 602.29 (~11%) | **5373.8** (~96.5%) | 5566.85 |

→ Ant는 BC가 이미 잘돼 DAgger 이득 작음. **Walker2d는 BC 11% → DAgger 96.5%로 극적 개선** — 불안정 환경일수록 DAgger의 expert relabel(방문 분포 보정)이 결정적.

## 핵심 정리

- BC = supervised 학습, $$O(T^2)$$ compounding error → 특히 불안정한 2족 보행에서 붕괴.
- DAgger = **learner rollout state + expert relabel**을 반복해 학습 분포 = 실행 분포로 정렬.
- gradient steps는 적으면 underfit, 과하면 overfit — 이 실험에서 Walker2d 최적 ≈ 5000.
- 실험 결과: Walker2d에서 BC 11% → DAgger 96.5%.

## 복습 질문

- BC의 objective와 compounding error($$O(T^2)$$)의 원인, DAgger가 이를 해결하는 메커니즘(누구의 분포 위에서 학습)?
- 왜 Ant·HalfCheetah는 BC로도 잘되는데 Hopper·Walker2d는 실패하나? (episode 길이 표와 연결)
- `do_relabel_with_expert`가 하는 일과, DAgger 루프의 4단계는?
- gradient steps 하이퍼파라미터가 성능에 U자형 영향을 주는 이유는?
{% endraw %}

---

다음 과제: [과제 2 — Policy Gradients](hw2-policy-gradients.md)
