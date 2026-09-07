---
layout: page
title: "15. OTA and Op-Amp - OTA와 연산증폭기"
permalink: /studies/circuits/electric-circuits-2/15-ota-and-op-amp/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/15%20OTA%20and%20Op-Amp%20-%20OTA%EC%99%80%20%EC%97%B0%EC%82%B0%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

{% raw %}
## 핵심 요약

OTA는 differential input을 output current로 바꾸는 operational transconductance amplifier이다. 전류미러와 single-ended differential amplifier, CS load를 조합해 만들 수 있다. voltage amplifier가 필요하면 source follower buffer를 붙여 op-amp 구조로 확장한다. op-amp는 보통 negative feedback과 함께 사용해 gain을 resistor ratio로 안정화한다.

## OTA의 구성

기본 요소:

- current mirrors
- single-ended differential amplifier
- CS stage with PMOS load

증폭기 유형:

```text
input: voltage
output: current
-> transconductance amplifier
```

## Input Common-Mode Range

OTA 입력단 MOSFET들이 saturation을 유지해야 한다.

하한 조건:

```text
VCM,min = VGS3 - |VTH,p|
```

상한 조건:

```text
VCM,max = VDD - VSG5 - VSG1 + |VTH,p|
```

큰 ICMR을 얻으려면 bias current를 작게 하여 필요한 overdrive voltage를 줄이는 것이 유리하다. 하지만 current를 너무 줄이면 `gm`과 bandwidth가 줄어든다.

## Transconductance Gain

OTA는 differential pair와 gain stage가 직렬로 연결된 구조로 볼 수 있다.

자료의 핵심 형태:

```text
Gm,total ≈ -gm1 gm6 (ro2 || ro4)
Rout ≈ ro6 || ro7
```

voltage gain:

```text
Av ≈ -gm1 gm6 (ro2 || ro4)(ro6 || ro7)
```

## Frequency Response

각 stage의 dominant pole은 주로 Miller capacitance에 의해 결정된다.

1단:

```text
C1 ≈ gm2(ro2 || ro4) Cgd2
```

2단:

```text
C2 ≈ gm6(ro6 || ro7) Cgd6
```

두 번째 stage output resistance가 source resistance보다 훨씬 크면 두 번째 pole이 전체 dominant pole이 될 수 있다.

## OTA에서 Op-Amp로

OTA는 output resistance가 크다. voltage amplifier로 쓰려면 output buffer가 필요하다.

```text
OTA + source follower -> op-amp
```

ideal op-amp:

- `Rin = infinite`
- `Rout = 0`
- open-loop gain very large

## Negative Feedback Op-Amp

op-amp는 거의 항상 negative feedback과 함께 사용한다.

non-inverting amplifier에서 ideal op-amp 가정:

```text
V+ = V-
Vo/Vs = 1 + R2/R1
```

inverting amplifier:

```text
Vo/Vs = - RF/Rin
```

장점:

- gain이 transistor parameter가 아니라 resistor ratio로 결정된다.
- gain을 쉽게 바꿀 수 있다.
- 안정성이 좋아진다.

## 시험 포인트

- OTA는 transconductance amplifier이다.
- ICMR는 input pair와 current source/load의 saturation 조건으로 정한다.
- OTA voltage gain은 `Gm * Rout` 형태이다.
- op-amp는 OTA에 voltage buffer를 붙인 구조로 이해한다.
- negative feedback에서 `V+ ≈ V-`가 성립하는 이유를 loop gain으로 설명한다.

## 같이 보면 좋은 노트

- [Differential Amplifiers 1 - 차동 증폭기 기본](08-differential-amplifiers-1.md)
- [Differential Amplifiers 2 - CMRR와 Active Load](09-differential-amplifiers-2-cmrr-active-load.md)
- [Feedback - 음귀환 기초](21-feedback.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **15. OTA and Op-Amp - OTA와 연산증폭기**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **15. OTA and Op-Amp - OTA와 연산증폭기**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [14. Frequency Response of Cascode SF Differential - 고주파 응답 비교](14-frequency-response-of-cascode-sf-differential.md) · 다음: [16. First-Order Filters - 1차 필터](16-first-order-filters-1.md)
