---
layout: page
title: "04. Common-Source Amplifier - CS 증폭기"
permalink: /studies/circuits/electric-circuits-2/04-common-source-amplifier-cs/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/04%20Common-Source%20Amplifier%20-%20CS%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

{% raw %}
## 핵심 요약

Common-source(CS) 증폭기는 MOSFET의 `gm vgs` 전류원 특성을 이용해 입력 전압을 출력 전압 또는 출력 전류로 변환한다. 기본 gain은 `-gm RD`이며, channel-length modulation을 고려하면 `-gm(RD || ro)`가 된다. source degeneration을 넣으면 gain은 줄지만 안정성과 선형성이 좋아진다.

## 증폭기의 네 가지 유형

| 입력 | 출력 | 유형 | 이상적 Rin | 이상적 Rout |
|---|---|---|---:|---:|
| V | V | Voltage amplifier | infinite | 0 |
| I | I | Current amplifier | 0 | infinite |
| V | I | Transconductance amplifier | infinite | infinite |
| I | V | Transimpedance amplifier | 0 | 0 |

CS는 특히 transconductance amplifier에 잘 맞는다.

## 기본 CS 증폭기

small-signal model에서:

```text
vout = - gm vin RD
Av = vout / vin = - gm RD
```

입출력 저항:

```text
Rin = infinite
Rout = RD
```

gate 전류가 없으므로 input resistance는 매우 크다.

## Channel-Length Modulation 포함

finite `ro`를 고려하면:

```text
Av = - gm (RD || ro)
Rout = RD || ro
Rin = infinite
```

`ro`가 작을수록 gain과 output resistance가 감소한다.

## CS as Transconductance Amplifier

출력 포트를 등가적으로 보면:

```text
iout = gm vin
```

따라서 transconductance gain은:

```text
Gm = gm
```

전압 gain은 이 current가 output resistance를 흐르면서 만들어진 결과이다.

## PMOS Load CS

저항 대신 PMOS load를 사용하면 IC에서 면적, 정확도, 구현성이 좋아진다.

gain:

```text
Av = - gm1 (ro1 || ro2)
```

입출력:

```text
Rin = infinite
Rout = ro1 || ro2
```

## CS with Source Degeneration

source에 `RS`를 넣으면 local negative feedback이 생긴다.

`ro` 무시 시:

```text
Av = - gm RD / (1 + gm RS)
Rin = infinite
Rout = RD
```

`gm RS >> 1`이면:

```text
Av ≈ - RD / RS
```

장점:

- gain이 transistor parameter `gm`에 덜 민감해진다.
- 선형성이 좋아진다.
- bias 안정성이 좋아진다.

단점:

- voltage gain이 감소한다.

## 시험 포인트

- 기본 CS gain `-gmRD`와 `ro` 포함 gain `-gm(RD||ro)`를 구분한다.
- CS의 `Rin`은 gate 때문에 이상적으로 infinite이다.
- source degeneration의 gain 식과 `-RD/RS` 근사를 기억한다.
- degeneration은 local negative feedback으로 이해한다.

## 같이 보면 좋은 노트

- [MOS Small-Signal Characteristics - MOS 소신호 모델](03-mos-small-signal-characteristics.md)
- [Source Follower and Common-Gate - SF CG 증폭기](05-source-follower-and-common-gate-sf-cg.md)
- [Frequency Response of CS - CS 주파수 응답](12-frequency-response-of-cs.md)
- [Feedback - 음귀환 기초](21-feedback.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **04. Common-Source Amplifier - CS 증폭기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **04. Common-Source Amplifier - CS 증폭기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [03. MOS Small-Signal Characteristics - MOS 소신호 모델](03-mos-small-signal-characteristics.md) · 다음: [05. Source Follower and Common-Gate - SF CG 증폭기](05-source-follower-and-common-gate-sf-cg.md)
