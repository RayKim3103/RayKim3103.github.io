---
layout: page
title: "11. Sim2Real · Humanoid RL"
permalink: /studies/ai/reinforcement-learning/11-sim2real-and-humanoid-rl/
sitemap: false
---

- **원본**: [GitHub — Reinforcement Learning](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Reinforcement_Learning) · 강의 노트 `15(Sim2Real)` + `16(Humanoid RL)` 통합·보강
- 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**Sim2Real transfer**: simulation에서 학습한 policy를 real world에 배포. 실제 로봇 RL은 sample 많이 필요·실패 위험·reward/초기상태 다양성 확보 어려움. simulation은 안전·대량 data 가능하지만 **sim–real gap**으로 transfer 실패 가능.
**Humanoid RL**: 사람형 로봇의 locomotion·manipulation·whole-body control. PPO가 locomotion baseline, motion prior 결합으로 복잡한 행동.

## 1. 왜 simulation

현실 RL 제약: sample 수집 비쌈 / 실패 위험·장비 손상 / interaction 비용 / reward 자동화 어려움 / 초기 상태 다양성 어려움.
simulation: 안전 / 빠른 대량 sample / 마모 회피 / 초기 상태·환경 조건 다양화 / dense reward.

## 2. Sim2Real Gap

- physics parameter 불일치: friction, mass, damping, actuator gain
- deformable object·contact dynamics 근사 오류
- camera calibration·sensor noise 차이
- visual domain 차이: texture, lighting, background
- real world variation을 simulation이 다 담지 못함

## 3. 대응책

| 방법 | 아이디어 |
|---|---|
| **System Identification** | 실제 시스템 input–output을 맞추도록 sim parameter 추정 (kinematics calibration, actuator delay/gain, object mass/friction) — 오래된 어려운 문제, 시스템이 시간에 따라 변할 수도 |
| **Domain Randomization** | sim parameter를 하나로 맞추지 않고 다양한 parameter·visual 조건을 무작위화 → policy를 real variation에 robust하게. physics randomization은 legged locomotion에 효과적, visual randomization은 현실 분포를 덮기 어려워 까다로움 |
| **Domain Adaptation** | sim/real observation 분포를 맞추거나 real data로 representation 보정 (image translation, feature alignment, real fine-tuning) |
| **Online Correction** | 배포 후 policy/dynamics representation을 빠르게 보정 (지면 마찰·payload 변화에 적응) |

## 4. Humanoid RL

### 범위
locomotion(걷기·달리기·parkour) / manipulation(손·팔) / whole-body control(이동+조작 동시) / human-object interaction.

### PPO 기반 locomotion
reward = command following + smooth motion + alive bonus − energy penalty − safety constraint − 불필요한 움직임. 안정적 보행엔 좋지만 매우 다양한 동작으로 확장 어려움.

### Teacher–Student distillation
$$
\text{state-based teacher (privileged state)} \;\xrightarrow{\text{distill}}\; \text{vision-based student (배포용 sensor obs)}
$$
teacher는 쉽게 학습, student는 실제 배포 가능한 관측으로 행동.

### Human Motion Prior (AMASS 등)
$$
\text{reward} = \text{task reward} + \text{motion similarity} - \text{energy/safety penalty}
$$
자연스러운 동작 + 효율적 탐색. **단점**: reference motion이 task와 충돌 가능 / imitation에 너무 묶이면 task 최적화 제한 / dataset coverage 밖 동작 어려움.

### Whole-body manipulation
locomotion보다 어려움 — balance, hand dexterity, contact dynamics, perception을 동시에. 손·팔·몸통·다리 coordination.

### Humanoid Sim2Real
관절 마찰·actuator dynamics / 발 접촉·slip / sensor delay / mass distribution 오차 / 안전 constraint → domain randomization, teacher-student distillation, online adaptation이 중요.

## 복습 질문

- sim2real gap의 원인 다섯 가지와, 각각에 대응하는 기법은?
- domain randomization과 system identification의 접근 차이는?
- teacher–student distillation에서 teacher/student가 각각 어떤 관측을 쓰는가?
- human motion prior를 쓰는 이점과 단점(task 충돌 등)은?
{% endraw %}

---

이전: [10. Skill Discovery · Hierarchical RL](10-skill-discovery-and-hierarchical-rl.md) · 다음: [12. 생성모델·표현학습 기반 RL](12-generative-models-and-representation-rl.md)
