---
layout: page
title: "07. RL 벤치마크"
permalink: /studies/ai/reinforcement-learning/07-rl-benchmarks/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Reinforcement_Learning/lecture_notes/07%20RL%20%EB%B2%A4%EC%B9%98%EB%A7%88%ED%81%AC.md)

{% raw %}
## 핵심 요약

RL 알고리즘은 최종 성능만으로 비교하기 어렵다. 같은 final return을 얻더라도 필요한 environment step 수가 다르면 sample efficiency가 다르다. 이 장은 RL benchmark의 역할, 연속 제어와 이산 제어 benchmark, 로봇 및 long-horizon task 평가 기준을 정리한다.

## 평가 지표

강의는 두 가지 기준을 강조한다.

| 지표 | 의미 |
| --- | --- |
| asymptotic performance | 충분히 오래 학습했을 때 도달하는 최종 성능 |
| sample efficiency | 적은 training step으로 얼마나 빨리 높은 성능에 도달하는가 |

그래프에서 x축은 training steps, y축은 evaluation return으로 표시한다. 어떤 알고리즘은 최종 성능은 높지만 sample이 많이 필요하고, 다른 알고리즘은 초반에는 빠르지만 최종 성능이 낮을 수 있다.

## Continuous Control Benchmark

연속 action space benchmark는 로봇 제어와 motor control 알고리즘을 평가하는 데 많이 쓰인다.

- DeepMind Control Suite
- OpenAI Gym / Gymnasium MuJoCo
- BSuite
- robosuite
- RLBench
- Meta-World
- LIBERO
- FrankaKitchen
- FurnitureBench
- HumanoidBench

MuJoCo 기반 환경은 물리 시뮬레이션 위에서 locomotion과 manipulation을 테스트한다.

## Discrete Control Benchmark

이산 action benchmark는 value-based RL과 exploration 성능을 평가하는 데 자주 쓰인다.

- Atari
- MiniGrid
- Procgen
- Crafter
- Minecraft 계열 환경

Atari는 pixel observation과 discrete action을 사용하는 대표적 benchmark이다. Procgen은 generalization 평가에 유용하고, MiniGrid는 sparse reward와 exploration 분석에 자주 쓰인다.

## Benchmark가 보는 능력

각 benchmark는 서로 다른 어려움을 가진다.

- simple continuous control: 기본 locomotion 성능
- complex robotic system: 고차원 state와 action 제어
- long-horizon task: 긴 계획과 sparse reward
- unseen game generalization: train/test 환경 차이
- open-ended embodied learning: 다양한 목표와 상호작용

## 벤치마크 사용 시 주의점

RL 실험은 seed variance가 크다. 따라서 하나의 curve만 보고 결론을 내리면 위험하다.

확인할 항목:

- 여러 random seed 평균과 신뢰구간을 보고 있는가?
- evaluation protocol이 학습 중 exploration noise를 끈 상태인가?
- environment step 수와 gradient update 수를 구분했는가?
- observation setting이 state인지 pixel인지 명확한가?
- reward normalization, action repeat, frame stack 같은 세부 설정이 같은가?

## 실험 결과 읽는 법

알고리즘 A가 B보다 좋다고 말하려면 적어도 다음 중 무엇이 좋은지 분명히 해야 한다.

- 더 높은 final return
- 더 빠른 학습
- 더 안정적인 seed 성능
- 더 적은 hyperparameter tuning
- 더 넓은 task에서 일관된 성능
- sim2real 또는 unseen task에서 더 좋은 transfer

## 연결 노트

- [Off-policy RL](06-off-policy-rl.md)
- [Offline RL](08-offline-rl.md)
- [Sim2Real Transfer](15-sim2real-transfer.md)
- [Humanoid RL](16-humanoid-rl.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **07. RL 벤치마크**를 다루며, agent가 environment와 상호작용하며 reward를 최대화하는 정책을 배우는 문제를 MDP, value, policy, exploration으로 해석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **07. RL 벤치마크**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [06. Off-policy RL](06-off-policy-rl.md) · 다음: [08. Offline RL](08-offline-rl.md)
