---
layout: page
title: "11. MOSFET High-Frequency Model - 고주파 모델"
permalink: /studies/circuits/electric-circuits-2/11-mosfet-high-frequency-model/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/11%20MOSFET%20High-Frequency%20Model%20-%20%EA%B3%A0%EC%A3%BC%ED%8C%8C%20%EB%AA%A8%EB%8D%B8.md)

{% raw %}
## 핵심 요약

MOSFET은 gate oxide와 junction 구조 때문에 여러 capacitance를 가진다. 고주파 응답에서는 `Cgs`, `Cgd`, `Cdb`, `Csb` 같은 capacitance가 pole/zero를 만들고, amplifier bandwidth를 제한한다. transistor 자체의 속도는 unit-gain frequency `ft`로 비교한다.

## MOSFET Capacitance

이 과목에서는 실제 복잡한 capacitance model 중 핵심 capacitor만 고려한다.

주요 capacitance:

- `Cgs`: gate-source
- `Cgd`: gate-drain
- `Cdb`: drain-body
- `Csb`: source-body

PMOS도 NMOS와 동일한 방식으로 모델링한다.

## 왜 단순화하는가

실제 SPICE model은 매우 복잡하지만 손계산에서는 frequency response를 결정하는 dominant capacitor를 파악하는 것이 중요하다.

해석 흐름:

```text
MOS small-signal model
-> essential capacitances 추가
-> pole/zero 근사
-> bandwidth 추정
```

## Unit-Gain Frequency `ft`

`ft`는 common-source short-circuit current gain의 magnitude가 1이 되는 주파수이다.

정의:

```text
|Iout / Iin| = 1 at f = ft
```

대략:

```text
omega_T = gm / (Cgs + Cgd)
fT = gm / [2 pi (Cgs + Cgd)]
```

의미:

- transistor speed 비교 지표
- 어떤 transistor operation이 가능한 최대 주파수의 감각 제공

## 더 빠른 MOSFET을 만드는 법

`ft`를 키우려면:

- `gm`을 크게 한다.
- `Cgs`, `Cgd`를 작게 한다.

하지만 width를 키우면 `gm`도 커지고 capacitance도 커지므로 단순하지 않다.

## Capacitance 근사

saturation에서:

```text
Cgd ≈ W Lov Cox
Cgs ≈ W Lov Cox + (2/3) W L Cox
```

보통:

```text
Cgs > Cgd
```

overlap capacitance와 channel capacitance가 모두 고려된다.

## 예시 결과

자료의 예시 조건:

- `L = 0.25 um`
- `W = 10 um`
- `VDS = 2 V`
- `VGS = 1.5 V`

추정 `fT`는 약 `24 GHz` 수준이며, simulation/모델 값과 비슷한 범위를 보인다.

## 시험 포인트

- 고주파 MOS model에서 네 capacitor 위치를 그릴 수 있어야 한다.
- `ft = gm / [2pi(Cgs+Cgd)]`의 의미를 안다.
- `Cgd`는 Miller effect 때문에 amplifier bandwidth에 큰 영향을 준다.
- `gm`을 키우는 것과 capacitance 증가 사이 trade-off를 이해한다.

## 같이 보면 좋은 노트

- [MOS Small-Signal Characteristics - MOS 소신호 모델](03-mos-small-signal-characteristics.md)
- [Frequency Response of CS - CS 주파수 응답](12-frequency-response-of-cs.md)
- [Frequency Response of Cascode SF Differential - 고주파 응답 비교](14-frequency-response-of-cascode-sf-differential.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **11. MOSFET High-Frequency Model - 고주파 모델**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **11. MOSFET High-Frequency Model - 고주파 모델**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. Pole Zero Bode Plot - 극점 영점 보드선도](10-pole-zero-bode-plot.md) · 다음: [12. Frequency Response of CS - CS 주파수 응답](12-frequency-response-of-cs.md)
