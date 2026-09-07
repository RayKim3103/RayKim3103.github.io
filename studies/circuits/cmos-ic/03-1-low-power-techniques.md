---
layout: page
title: "03. Low Power Techniques"
permalink: /studies/circuits/cmos-ic/03-1-low-power-techniques/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_CMOS_Integrated_Circuit/lecture_notes/03_1%20Low%20Power%20Techniques%20-%20%EC%A0%80%EC%A0%84%EB%A0%A5%20%EC%84%A4%EA%B3%84%20%EB%B3%B4%EA%B0%95.md)

{% raw %}
tags: #cmos-integrated-circuit #low-power #vdd-scaling #switching-activity #glitch #mtcmos

관련 노트: [지연 모델과 Logical Effort](03-speed-logical-effort.md), [CMOS 전력소모와 저전력기법](04-power-cmos.md)

## 핵심 요약

이 보강 자료는 CMOS power consumption을 architecture, logic, circuit level에서 줄이는 방법을 다룬다. 핵심 주제는 voltage scaling, parallelism/pipelining을 통한 낮은 VDD 동작, signal dynamic range와 switching activity 감소, glitch 억제, resource sharing의 power tradeoff, circuit-level low power technique이다.

## CMOS Power 구성

CMOS 전력은 크게 dynamic power와 static/leakage power로 나뉜다.

```text
P_total = P_dynamic + P_static
```

Dynamic power는 capacitance를 충방전할 때 쓰이며, switching activity와 VDD에 크게 의존한다. Static power는 회로가 가만히 있어도 leakage 때문에 소모된다.

## Architecture Level Power

Architecture level에서는 같은 throughput을 유지하면서 낮은 VDD로 동작할 수 있게 구조를 바꾼다.

### Parallelism

연산기를 병렬화하면 각 연산기가 더 낮은 frequency로 동작해도 전체 throughput을 유지할 수 있다. Frequency 여유를 VDD scaling에 사용하면 `VDD^2` 효과로 power를 크게 줄일 수 있다. 단, 병렬 hardware가 늘어나 capacitance와 area가 증가한다.

### Pipelining

Pipeline register를 넣어 critical path를 짧게 만들면 낮은 VDD에서도 목표 frequency를 만족할 수 있다. 하지만 register 자체의 clock power와 area가 추가된다.

## Arithmetic Computation Scaling

Adder-comparator datapath 예제는 architecture 변환으로 delay를 줄인 뒤 VDD를 낮추는 방법을 보여준다. Extra latch 때문에 capacitance가 약간 증가해도 VDD 감소 효과가 더 크면 전체 dynamic power가 줄어든다.

핵심 tradeoff:

```text
추가 hardware/register capacitance 증가
낮은 VDD로 인한 CVDD^2 감소
```

## Signal Dynamic Range와 Switching Activity

신호의 dynamic range가 작고, 인접 sample 사이에 음의 상관관계가 크면 transition 수가 줄어들 수 있다. Shift operation은 scaling operation으로 동작해 signal dynamic range를 줄이는 효과를 가질 수 있다.

Switching activity를 줄이면 dynamic power가 직접 줄어든다.

```text
P_dynamic = alpha C VDD^2 f
```

여기서 `alpha`는 activity factor이다.

## Glitching Activity

Static CMOS에서도 logic block 사이 propagation delay가 서로 다르면 glitch가 발생한다.

원인:

- 입력 도착 시간이 다름
- reconvergent fanout
- dynamic hazard
- critical race

Glitch는 최종 논리값이 바뀌지 않아도 내부 node를 불필요하게 충방전시켜 power를 증가시킨다.

## Logic Depth와 Register Power

Logic depth를 줄이면 glitch가 줄 수 있지만, pipeline register가 많아지면 clock power와 register capacitance가 늘어난다. 따라서 low power design은 combinational depth, glitch activity, register overhead를 함께 봐야 한다.

## Resource Sharing의 전력 Tradeoff

두 개의 물리 adder를 사용할 때와 하나의 time-multiplexed adder를 공유할 때 power가 항상 한쪽으로 유리한 것은 아니다.

- 물리 adder가 많으면 hardware capacitance가 증가한다.
- 공유 adder는 mux, control, switching pattern이 증가할 수 있다.
- 입력 변화가 고정되거나 반복되는 정도에 따라 switching activity가 달라진다.

## Circuit Level Low Power

자료가 언급한 circuit-level 선택지는 다음과 같다.

- static style vs dynamic style
- pass gate vs normal CMOS
- transistor sizing
- supply voltage 조절
- threshold voltage 조절
- power gating 계열 기법

## 시험ㆍ복습 체크포인트

- `P_dynamic = alpha C VDD^2 f`에서 각 항을 설명할 수 있어야 한다.
- Parallelism과 pipelining이 low VDD 동작을 가능하게 하는 이유를 이해해야 한다.
- Glitch가 power를 증가시키는 과정을 말할 수 있어야 한다.
- Register 추가가 항상 power를 줄이지 않는 이유를 설명할 수 있어야 한다.
- Resource sharing의 power tradeoff를 capacitance와 activity 관점에서 분석할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **03. Low Power Techniques**를 다루며, MOS device 특성에서 CMOS gate, delay, power, interconnect, sequential timing까지 회로와 물리 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 디지털 회로도 transistor와 wire RC 위에서 동작하므로 noise margin, delay, power를 함께 봐야 한다.
- logical effort는 복잡한 gate delay를 parasitic과 electrical effort로 나누어 설계 감각을 준다.
- scaling은 성능 이득과 함께 leakage, variability, reliability 문제를 키운다.

### 문제 풀이 또는 구현 루틴

- pull-up/pull-down network, 논리 기능, 최악 경로, load capacitance를 순서대로 확인한다.
- delay 계산은 equivalent resistance와 switched capacitance를 잡고 Elmore/RC 근사로 시작한다.
- sequential timing은 setup, hold, clock skew, contamination/propagation delay를 따로 계산한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- static CMOS에서 PUN과 PDN은 dual network여야 한다.
- dynamic power는 activity factor와 capacitance에 민감해 단순 주파수만으로 결정되지 않는다.
- setup violation과 hold violation은 고치는 방향이 다르다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 최악 delay path는 어디인가?
- 전력 중 switching, short-circuit, leakage 중 무엇이 지배적인가?
- 공정/전압/온도 변화가 margin을 얼마나 줄이는가?
- **03. Low Power Techniques**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. 지연 모델과 Logical Effort](03-speed-logical-effort.md) · 다음: [04. CMOS 전력소모와 저전력기법](04-power-cmos.md)
