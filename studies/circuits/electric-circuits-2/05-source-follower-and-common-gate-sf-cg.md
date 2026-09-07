---
layout: page
title: "05. Source Follower and Common-Gate - SF CG 증폭기"
permalink: /studies/circuits/electric-circuits-2/05-source-follower-and-common-gate-sf-cg/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/05%20Source%20Follower%20and%20Common-Gate%20-%20SF%20CG%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

{% raw %}
## 핵심 요약

Source follower(SF)는 voltage gain이 1보다 조금 작은 voltage buffer이고, common-gate(CG)는 낮은 input resistance와 높은 output resistance를 가진 current buffer/current amplifier로 해석하기 좋다. CS의 큰 gain 뒤에 SF를 붙이면 load가 작은 경우에도 CS gain을 보존할 수 있다.

## Source Follower

`ro`를 무시하면 source follower의 voltage gain은:

```text
Av = gm RL / (1 + gm RL)
```

input/output resistance:

```text
Rin = infinite
Rout ≈ 1/gm || RL
```

특징:

- gain은 1보다 작고 1에 가깝다.
- input resistance가 크다.
- output resistance가 작다.
- voltage buffer로 적합하다.

## `ro` 포함 Source Follower

finite `ro`를 고려하면 source node에서 보는 저항은 대략:

```text
Rout ≈ 1/gm || ro || RL
```

body effect까지 고려하면 effective transconductance가 `gm + gmb`가 되어 output resistance가 더 작아질 수 있다.

## CS + SF

CS 출력에 작은 `RL`을 직접 연결하면:

```text
Av = -gm (RD || RL)
```

가 되어 gain이 크게 줄어든다. SF를 buffer로 붙이면 CS는 큰 input resistance를 보는 셈이 되어 gain을 유지하고, SF가 작은 output resistance로 load를 구동한다.

```text
CS: large gain
SF: voltage buffer
```

## Common-Gate Amplifier

CG는 gate가 AC ground이고 source로 입력을 넣고 drain에서 출력을 얻는다.

`ro` 무시 시:

```text
Rin = 1/gm
Rout = RD
Av = gm RD
```

CS와 달리 voltage gain이 non-inverting이다.

## CG as Current Buffer

CG는 input resistance가 작고 output resistance가 크므로 current amplifier/current buffer에 적합하다.

전류 gain은 이상적으로:

```text
Ai ≈ -1
```

즉 source로 들어온 전류가 drain 쪽으로 거의 전달된다.

## `ro` 포함 CG 결과

강의 homework에서 제시된 결과:

```text
Rin = (ro + RD) / (1 + gm ro)
Rout = ro || RD
Av = (1 + gm ro) RD / (RD + ro)
```

`gm ro >> 1`이면 `Rin`은 `1/gm`에 가까워진다.

## CS, SF, CG 비교

| 회로 | gain | Rin | Rout | 대표 용도 |
|---|---:|---:|---:|---|
| CS | 큼, negative | 큼 | 중간/큼 | transconductance, voltage gain |
| SF | 약 1 | 큼 | 작음 | voltage buffer |
| CG | positive, `gmRD` | 작음 | 큼 | current buffer, wideband input |

## 시험 포인트

- SF gain `gmRL/(1+gmRL)`과 buffer 역할을 기억한다.
- CG의 `Rin ≈ 1/gm`이 왜 낮은지 설명할 수 있어야 한다.
- CS+SF가 load effect를 줄이는 이유를 이해한다.
- CG는 current buffer로 적합하며 current gain이 대략 `-1`이다.

## 같이 보면 좋은 노트

- [Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md)
- [Cascode Amplifier - 캐스코드 증폭기](06-cascode-amplifier.md)
- [Frequency Response of CS Degeneration and CG - Degeneration CG 응답](13-frequency-response-of-cs-degeneration-and-cg.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **05. Source Follower and Common-Gate - SF CG 증폭기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회로 주제에서는 DC 동작점, small-signal 모델, 주파수 응답, feedback 효과를 순서대로 분리한다.
- gain 식은 부호, loading, output resistance, capacitance가 들어가는 위치를 회로 노드에서 추적한다.
- midband gain만으로 평가하지 말고 input/output resistance, pole-zero, swing, noise, stability를 함께 봐야 한다.
- feedback은 gain을 희생해 bandwidth, 선형성, 저항 특성, 공정 민감도를 조절하는 도구다.
- filter와 oscillator는 s-domain pole 위치가 시간응답, 주파수응답, 안정성을 결정한다.

### 문제 풀이 또는 구현 루틴

- DC operating point, small-signal model, midband gain, pole-zero, feedback loop 순서로 해석한다.
- spec을 gain, bandwidth, noise, power, area, swing 제약으로 나누어 trade-off를 적는다.
- LTspice 결과는 operating point, AC response, transient response를 같은 회로에서 순차 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- Miller effect를 놓치면 high-frequency pole을 크게 잘못 예측한다.
- loop gain과 closed-loop gain을 섞으면 feedback 회로 해석이 흐려진다.
- oscillator는 loop gain뿐 아니라 phase 조건과 amplitude stabilization이 필요하다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 회로의 dominant pole은 어느 node capacitance와 resistance가 만드는가?
- feedback이 입력/출력 저항을 어느 방향으로 바꾸는가?
- spec 하나를 개선할 때 power, noise, swing, stability 중 무엇이 나빠지는가?
- **05. Source Follower and Common-Gate - SF CG 증폭기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [04. Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md) · 다음: [06. Cascode Amplifier - 캐스코드 증폭기](06-cascode-amplifier.md)
