---
layout: page
title: "15. Sim2Real Transfer"
permalink: /studies/ai/reinforcement-learning/15-sim2real-transfer/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/15%20Sim2Real%20Transfer.md)

{% raw %}
## 핵심 요약

Sim2Real transfer는 simulation에서 학습한 policy를 real world에 배포하는 문제이다. 실제 로봇에서 RL을 직접 학습하면 sample이 많이 필요하고, 실패가 위험하며, reward와 초기 상태 다양성을 확보하기 어렵다. simulation은 안전하고 data를 많이 만들 수 있지만, sim과 real 사이의 dynamics 및 visual discrepancy 때문에 transfer가 실패할 수 있다.

## 왜 simulation을 쓰는가

현실 세계 RL은 다음 제약이 있다.

- 많은 sample 수집이 필요하다.
- 실패가 위험하거나 장비를 손상시킬 수 있다.
- interaction 비용이 크다.
- reward signal을 자동으로 주기 어렵다.
- 다양한 초기 상태를 만들기 어렵다.

simulation은 이 문제를 완화한다.

- 안전하다.
- 많은 sample을 빠르게 생성할 수 있다.
- 로봇 마모를 피할 수 있다.
- 초기 상태와 환경 조건을 다양하게 만들 수 있다.
- dense reward를 쉽게 줄 수 있다.

## Sim2Real gap

transfer 실패의 원인은 sim과 real의 차이다.

- friction, mass, damping, actuator gain 같은 physics parameter 불일치
- deformable object나 contact dynamics 근사 오류
- camera calibration과 sensor noise 차이
- texture, lighting, background 같은 visual domain 차이
- real world variation을 simulation이 모두 담지 못함

## System Identification

system identification은 실제 시스템의 input-output behavior를 맞추도록 simulation parameter를 추정하는 접근이다.

예:

- robot kinematics calibration
- actuator delay와 gain 추정
- camera calibration
- object mass와 friction 추정

정확한 parameter를 찾는 것은 오래된 어려운 문제이며, 실제 시스템은 시간에 따라 변할 수도 있다.

## Domain Randomization

domain randomization은 simulation parameter를 하나로 맞추려 하지 않고, 다양한 parameter와 visual condition을 무작위로 바꿔 policy를 robust하게 만든다.

```text
train on many randomized sims -> policy robust to real variation
```

physics randomization은 legged robot locomotion에서 효과적일 수 있다. visual randomization은 현실의 복잡한 시각 분포를 충분히 덮기 어려워 더 까다롭다.

## Domain Adaptation

domain adaptation은 simulation과 real observation distribution을 맞추거나, real data로 representation을 보정하는 접근이다. visual sim2real에서는 image translation, feature alignment, real-world fine-tuning이 사용될 수 있다.

## Online Correction과 Adaptation

real world에 배포한 뒤 policy나 dynamics representation을 빠르게 보정하는 방법도 중요하다. 예를 들어 legged robot은 지면 마찰이나 payload가 달라져도 빠르게 적응해야 한다.

## 실전 체크

- simulation reward가 real task objective와 맞는가?
- real sensor noise와 latency를 simulation에 넣었는가?
- actuator saturation과 safety constraint를 반영했는가?
- train randomization 범위가 real variation을 포함하는가?
- real deployment 전에 policy를 안전하게 검증할 수 있는가?

## 연결 노트

- [Model-Based RL](09-model-based-rl.md)
- [Hierarchical RL](14-hierarchical-rl.md)
- [Humanoid RL](16-humanoid-rl.md)
- [강화학습 리뷰와 열린 문제](18-rl-review-and-open-problems.md)

{% endraw %}

---

이전: [14. Hierarchical RL](14-hierarchical-rl.md) · 다음: [16. Humanoid RL](16-humanoid-rl.md)
