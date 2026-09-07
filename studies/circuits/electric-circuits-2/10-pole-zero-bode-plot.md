---
layout: page
title: "10. Pole Zero Bode Plot - 극점 영점 보드선도"
permalink: /studies/circuits/electric-circuits-2/10-pole-zero-bode-plot/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/10%20Pole%20Zero%20Bode%20Plot%20-%20%EA%B7%B9%EC%A0%90%20%EC%98%81%EC%A0%90%20%EB%B3%B4%EB%93%9C%EC%84%A0%EB%8F%84.md)

{% raw %}
## 핵심 요약

회로의 frequency response는 s-domain transfer function의 pole과 zero로 결정된다. Bode plot은 `20log10|H(jw)|`와 phase를 log frequency 축에 그린 것이다. pole은 magnitude slope를 `-20 dB/dec`씩 낮추고 phase를 `-90 deg` 변화시키며, zero는 반대로 `+20 dB/dec`, `+90 deg` 효과를 만든다.

## s-Domain 해석

시간 영역 미분방정식은 Laplace transform으로 s-domain algebra 문제로 바뀐다.

소자 impedance:

```text
ZC = 1/(sC)
ZL = sL
```

sinusoidal steady state에서는:

```text
s = jw
```

## Transfer Function

회로의 frequency-domain 특성은:

```text
H(s) = Vout(s) / Vin(s)
```

형태:

```text
H(s) = A * product(s - z_i) / product(s - p_i)
```

여기서:

- `z_i`: zeros
- `p_i`: poles

## dB Scale

power ratio:

```text
dB = 10 log10(Pout/Pin)
```

voltage ratio는 power가 voltage square에 비례하므로:

```text
dB = 20 log10(Vout/Vin)
```

## Zero at Origin

```text
H(s) = s
H(jw) = jw
```

효과:

- magnitude: `+20 dB/dec`
- phase: `+90 deg`

## Pole at Origin

```text
H(s) = 1/s
H(jw) = 1/(jw)
```

효과:

- magnitude: `-20 dB/dec`
- phase: `-90 deg`

## Real Zero

```text
H(s) = 1 + s/wz
```

효과:

- `w << wz`: 거의 변화 없음
- `w = wz`: +3 dB
- `w >> wz`: `+20 dB/dec`
- phase: `wz/10`부터 `10wz` 사이에서 0도에서 90도로 변함

## Real Pole

```text
H(s) = 1 / (1 + s/wp)
```

효과:

- `w << wp`: 거의 변화 없음
- `w = wp`: -3 dB
- `w >> wp`: `-20 dB/dec`
- phase: 0도에서 -90도로 변함

## CS Amplifier 예

MOS 자체 고주파 응답을 무시하고 output load capacitance `CL`만 고려하면:

```text
H(s) = -gm RD / (1 + s RD CL)
```

pole:

```text
wp = 1 / (RD CL)
```

DC gain:

```text
Av0 = -gm RD
```

## Bode Plot 작성 절차

1. transfer function을 pole-zero 형태로 정리한다.
2. DC gain 또는 기준 gain을 dB로 표시한다.
3. 각 zero에서 slope를 `+20 dB/dec` 추가한다.
4. 각 pole에서 slope를 `-20 dB/dec` 추가한다.
5. phase는 각 pole/zero의 decade 전후에서 부드럽게 변화시킨다.

## 시험 포인트

- capacitor/inductor의 s-domain impedance를 기억한다.
- voltage ratio에는 `20log`, power ratio에는 `10log`를 쓴다.
- pole/zero가 magnitude slope와 phase에 주는 영향을 설명할 수 있어야 한다.
- single-pole CS amplifier의 pole `1/(RDCL)`를 바로 도출한다.

## 같이 보면 좋은 노트

- [MOSFET High-Frequency Model - 고주파 모델](11-mosfet-high-frequency-model.md)
- [Frequency Response of CS - CS 주파수 응답](12-frequency-response-of-cs.md)
- [First-Order Filters - 1차 필터](16-first-order-filters-1.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. Pole Zero Bode Plot - 극점 영점 보드선도**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **10. Pole Zero Bode Plot - 극점 영점 보드선도**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. Differential Amplifiers 2 - CMRR와 Active Load](09-differential-amplifiers-2-cmrr-active-load.md) · 다음: [11. MOSFET High-Frequency Model - 고주파 모델](11-mosfet-high-frequency-model.md)
