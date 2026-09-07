---
layout: page
title: "12. Frequency Response of CS - CS 주파수 응답"
permalink: /studies/circuits/electric-circuits-2/12-frequency-response-of-cs/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/12%20Frequency%20Response%20of%20CS%20-%20CS%20%EC%A3%BC%ED%8C%8C%EC%88%98%20%EC%9D%91%EB%8B%B5.md)

{% raw %}
## 핵심 요약

CS amplifier의 주파수 응답은 MOS capacitance, 특히 input과 output을 연결하는 `Cgd` 때문에 복잡해진다. Miller theorem을 사용하면 `Cgd`를 input/output node의 등가 capacitance로 나누어 근사할 수 있다. CS는 negative gain이 크므로 input에서 `Cgd`가 `1+|Av|`배 커진 것처럼 보이고, 이것이 dominant input pole을 만든다.

## CS 고주파 해석 문제

고려할 capacitance:

- `Cgs`
- `Cgd`
- `Cdb`

`Cgd`는 input과 output을 직접 연결하므로 node가 coupling되어 해석이 복잡하다.

## Miller's Theorem

두 node 사이 impedance `ZF`가 있고 voltage gain이 `Av = Vout/Vin`이면 이를 input/output ground capacitance로 등가 변환할 수 있다.

capacitor `CF`의 경우:

```text
Cin,Miller = CF (1 - Av)
Cout,Miller ≈ CF (1 - 1/Av)
```

CS amplifier는 `Av < 0`이므로:

```text
Cin,Miller ≈ Cgd (1 + |Av|)
```

이를 Miller effect라고 한다.

## CS Amplifier의 등가 Capacitance

load resistance를 `RL`이라 하고 gain을 대략:

```text
Av ≈ -gm RL
```

라고 하면 input capacitance:

```text
CX ≈ Cgs + Cgd(1 + gm RL)
```

output capacitance:

```text
CY ≈ Cdb + Cgd(1 + 1/(gm RL))
```

gain이 크면 input 쪽 Miller capacitance가 매우 커진다.

## 근사 전달함수

3-dB bandwidth 추정을 위한 근사:

```text
Vout/Vin ≈ -gm RL /
[(1 + s RS CX)(1 + s RL CY)]
```

input pole:

```text
wp,in ≈ 1 / [RS (Cgs + Cgd(1 + gm RL))]
```

output pole:

```text
wp,out ≈ 1 / [RL (Cdb + Cgd(1 + 1/(gm RL)))]
```

## Dominant Pole

CS에서는 보통 input Miller capacitance가 커서 input pole이 dominant가 되기 쉽다.

큰 gain 조건에서:

```text
wp,in ≈ 1 / (RS Cgd gm RL)
```

즉 gain이 커질수록 bandwidth가 줄어든다.

## Gain-Bandwidth Product

CS의 gain-bandwidth product는 대략:

```text
GBW ≈ gm / (Cgd) * 1/RS factor
```

슬라이드의 핵심 메시지는 gain을 키우면 Miller effect로 bandwidth가 줄어 gain-bandwidth trade-off가 생긴다는 것이다.

## 시험 포인트

- `Cgd`가 Miller effect를 만드는 이유를 설명한다.
- negative gain amplifier에서 input Miller capacitance가 `Cgd(1+|Av|)`가 된다.
- input/output pole을 resistance와 capacitance 곱으로 근사한다.
- CS의 dominant pole은 대개 input Miller pole이다.

## 같이 보면 좋은 노트

- [Pole Zero Bode Plot - 극점 영점 보드선도](10-pole-zero-bode-plot.md)
- [MOSFET High-Frequency Model - 고주파 모델](11-mosfet-high-frequency-model.md)
- [Frequency Response of CS Degeneration and CG - Degeneration CG 응답](13-frequency-response-of-cs-degeneration-and-cg.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **12. Frequency Response of CS - CS 주파수 응답**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

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
- **12. Frequency Response of CS - CS 주파수 응답**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [11. MOSFET High-Frequency Model - 고주파 모델](11-mosfet-high-frequency-model.md) · 다음: [13. Frequency Response of CS Degeneration and CG - Degeneration CG 응답](13-frequency-response-of-cs-degeneration-and-cg.md)
