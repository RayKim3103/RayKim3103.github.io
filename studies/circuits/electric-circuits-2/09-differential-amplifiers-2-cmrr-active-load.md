---
layout: page
title: "09. Differential Amplifiers 2 - CMRR와 Active Load"
permalink: /studies/circuits/electric-circuits-2/09-differential-amplifiers-2-cmrr-active-load/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/09%20Differential%20Amplifiers%202%20-%20CMRR%EC%99%80%20Active%20Load.md)

{% raw %}
## 핵심 요약

차동 증폭기는 symmetry에 의존한다. symmetry를 깨는 모든 요소, 예를 들어 resistor mismatch, current source finite resistance, active load mirror action은 성능에 영향을 준다. 이 강의는 single-ended output에서 common-mode gain이 생기는 이유, CMRR, component mismatch, active-loaded differential amplifier의 gain을 다룬다.

## Single-Ended Output

differential output이 아니라 한쪽 drain만 output으로 쓰면 common-mode 성분이 완전히 사라지지 않는다.

DM gain:

```text
Adm(single-ended) ≈ - (1/2) gm RD
```

CM gain은 tail resistance `RSS`가 finite일 때 생긴다.

```text
Acm ≈ - RD / (2RSS + 1/gm)
```

`gm RSS >> 1`이면 대략:

```text
Acm ≈ - RD / (2RSS)
```

## CMRR

Common-Mode Rejection Ratio:

```text
CMRR = |Adm / Acm|
```

single-ended output에서는 대략:

```text
CMRR ≈ gm RSS
```

tail current source의 output resistance가 클수록 common-mode rejection이 좋아진다.

## Component Mismatch 영향

차동 pair는 좌우 대칭이 깨지면 CM 입력이 DM 출력으로 변환된다.

저항 mismatch 예:

```text
RD1 = RD
RD2 = RD + Delta RD
```

common-mode 입력에서도 두 output이 정확히 같지 않아 differential output이 생긴다.

대략:

```text
Acm,mismatch ∝ Delta RD / RSS
```

반면 differential-mode gain은 작은 resistor mismatch에 1차적으로 크게 영향을 받지 않을 수 있다.

## Active-Loaded Differential Amplifier

저항 load 대신 PMOS current mirror active load를 사용한다.

장점:

- resistor 없이 구현 가능
- matching이 좋음
- 면적 작음
- single-ended output을 쉽게 얻음
- 큰 output resistance로 high gain 가능

## Half-Circuit 근사의 한계

active load에서는 current mirror action 때문에 단순 half-circuit으로는 정확한 gain이 나오지 않는다.

단순 추정:

```text
Av ≈ - gmN (roN || roP)
```

하지만 current mirror가 short-circuit transconductance를 두 배로 키우는 효과가 있어 더 정확한 해석에서는 factor of 2 차이가 나타난다.

강의의 핵심 해석:

```text
Gm ≈ -gmN
Rout ≈ roN || roP
Av ≈ -Gm Rout
```

## 시험 포인트

- differential pair의 성능은 symmetry에 크게 의존한다.
- single-ended output은 common-mode gain을 가질 수 있다.
- CMRR는 `Adm/Acm`이고 tail resistance가 클수록 좋아진다.
- mismatch는 common-mode를 differential output으로 변환한다.
- active load의 current mirror action 때문에 half-circuit 해석이 틀릴 수 있다.

## 같이 보면 좋은 노트

- [Differential Amplifiers 1 - 차동 증폭기 기본](08-differential-amplifiers-1.md)
- [OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)
- [Bias Circuits and Current Mirrors - 바이어스와 전류미러](07-bias-circuits-and-current-mirrors.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **09. Differential Amplifiers 2 - CMRR와 Active Load**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **09. Differential Amplifiers 2 - CMRR와 Active Load**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [08. Differential Amplifiers 1 - 차동 증폭기 기본](08-differential-amplifiers-1.md) · 다음: [10. Pole Zero Bode Plot - 극점 영점 보드선도](10-pole-zero-bode-plot.md)
