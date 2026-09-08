---
layout: page
title: "03. BJT 기본 동작"
permalink: /studies/circuits/electric-circuits-1/03-bjt-operation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/03%20BJT%20%EA%B8%B0%EB%B3%B8%20%EB%8F%99%EC%9E%91.md)

{% raw %}
## 한눈에 보기

이 장은 BJT를 전압제어 전류원으로 이해한다. 증폭기의 핵심인 transconductance, forward active 동작, collector current, base/emitter current, current gain, small signal model, Early effect, 동작 영역을 다룬다.

```text
좋은 전압원/전류원 -> VCCS -> BJT 구조
-> collector current -> beta -> small signal model
-> Early effect -> operation region -> PNP
```

## 증폭기의 기본 아이디어

증폭기는 입력 신호를 더 큰 출력 신호로 만드는 회로이다.

전압 이득은 다음처럼 정의한다.

$$
A_v=\frac{v_{out}}{v_{in}}
$$

BJT 증폭기는 입력 전압 변화가 출력 전류 변화를 만들고, 그 전류가 부하 저항을 지나며 출력 전압으로 바뀌는 구조이다.

```text
v_in -> i_out -> i_out R -> v_out
```

따라서 좋은 증폭기를 만들려면 다음이 중요하다.

- 입력 전압 변화에 대한 출력 전류 변화가 커야 한다.
- 출력단의 부하 또는 출력 저항이 적절히 커야 한다.
- DC bias로 트랜지스터를 원하는 동작점에 두어야 한다.

## 좋은 전압원과 좋은 전류원

좋은 전압원은 부하가 바뀌어도 전압을 잘 유지해야 한다. 이를 위해 출력저항이 작아야 한다.

좋은 전류원은 부하가 바뀌어도 전류를 잘 유지해야 한다. 이를 위해 출력저항이 커야 한다.

트랜지스터는 특정 동작 영역에서 전압제어 전류원처럼 동작한다.

## VCCS

BJT는 작은 신호 관점에서 voltage controlled current source로 볼 수 있다.

$$
i_{out}=g_m v_{in}
$$

여기서 $g_m$은 transconductance이다.

출력 전압은 부하 저항 $R$에 의해

$$
v_{out}=i_{out}R
$$

로 변환된다.

따라서 이득을 크게 하려면

$$
g_m \uparrow,\quad R \uparrow
$$

가 유리하다.

## BJT 구조와 바이어스

npn BJT는 emitter, base, collector로 구성된다.

- emitter: carrier를 방출하는 쪽
- base: 매우 얇고 약하게 도핑된 제어 영역
- collector: carrier를 수집하는 쪽

forward active region에서 조건은 다음과 같다.

- base-emitter 접합: 순방향 바이어스
- base-collector 접합: 역방향 바이어스

npn 기준으로는 보통

$$
V_B>V_E
$$

$$
V_C>V_B
$$

이면 forward active region으로 본다.

## Collector current

BJT의 collector current는 base-emitter 전압에 의해 지수적으로 제어된다.

$$
I_C=I_S e^{V_{BE}/V_T}
$$

정확히는

$$
I_C=I_S(e^{V_{BE}/V_T}-1)
$$

이지만 forward bias에서 $e^{V_{BE}/V_T}$가 충분히 커지면 $-1$을 무시한다.

이 식은 BJT가 전압제어 전류원으로 동작한다는 핵심을 보여준다.

## 전류 이득 beta

BJT에서는 base current가 작고 collector current가 크다.

$$
I_C=\beta I_B
$$

따라서

$$
I_B=\frac{I_C}{\beta}
$$

emitter current는

$$
I_E=I_C+I_B=(\beta+1)I_B
$$

이다.

$\beta$는 공정과 도핑에 크게 의존하므로 설계에서 너무 정확한 상수처럼 믿으면 안 된다.

## 동작점 예시 감각

예를 들어

$$
I_S=5\times 10^{-19}A,\quad V_{BE}=800mV,\quad \beta=100
$$

상온에서 $V_T=26mV$이면

$$
I_C=I_S e^{V_{BE}/V_T}
$$

로 collector current를 구한다. 이후

$$
I_B=\frac{I_C}{100}
$$

$$
I_E=I_C+I_B
$$

를 계산한다.

collector에 저항 $R_C$가 있으면

$$
V_C=V_{CC}-I_C R_C
$$

로 collector 전압을 구하고, $V_C>V_B$인지 확인해 active region인지 검증한다.

## Transconductance

소신호 해석에서 가장 중요한 파라미터는 $g_m$이다.

$$
g_m=\frac{\partial I_C}{\partial V_{BE}}
$$

$$
I_C=I_S e^{V_{BE}/V_T}
$$

이므로

$$
g_m=\frac{I_C}{V_T}
$$

이다.

상온에서는 $V_T\approx 26mV$이므로

$$
g_m\approx \frac{I_C}{26mV}
$$

이다. DC 동작점의 $I_C$가 정해지면 $g_m$도 정해진다.

## Small signal model

BJT의 소신호 hybrid-pi 모델은 다음 요소를 사용한다.

- base-emitter 사이 입력저항 $r_\pi$
- collector-emitter 사이 제어전류원 $g_m v_\pi$
- Early effect를 고려한 출력저항 $r_o$

기본 관계는 다음과 같다.

$$
i_c=g_m v_\pi
$$

$$
v_\pi=i_b r_\pi
$$

$$
g_m r_\pi=\beta
$$

따라서

$$
r_\pi=\frac{\beta}{g_m}
$$

이다.

## Large signal과 small signal

large signal 모델은 $I_C=I_S e^{V_{BE}/V_T}$ 같은 비선형 식을 그대로 사용한다. DC 동작점과 큰 변화 분석에 필요하다.

small signal 모델은 동작점 주변에서 곡선을 선형화한다. 작은 AC 신호의 이득을 계산할 때 사용한다.

```text
DC bias로 Q point 결정 -> Q point에서 gm, r_pi, r_o 계산 -> AC 등가회로 해석
```

## Early effect

이상적인 전류원은 $V_{CE}$가 변해도 $I_C$가 일정해야 한다. 실제 BJT에서는 $V_{CE}$가 커지면 base width가 변하고 collector current도 조금 증가한다. 이를 Early effect라고 한다.

모델은 다음처럼 쓴다.

$$
I_C=I_S e^{V_{BE}/V_T}\left(1+\frac{V_{CE}}{V_A}\right)
$$

출력저항은

$$
r_o=\frac{V_A}{I_C}
$$

로 근사한다.

Early voltage $V_A$가 클수록 $r_o$가 커지고, 더 이상적인 전류원에 가까워진다.

## 소신호 파라미터 요약

DC 동작점에서 $I_C$를 알면 다음을 계산할 수 있다.

$$
g_m=\frac{I_C}{V_T}
$$

$$
r_\pi=\frac{\beta}{g_m}
$$

$$
r_o=\frac{V_A}{I_C}
$$

이 세 값은 이후 BJT 증폭기 해석의 출발점이다.

## 동작 영역

### Forward active region

트랜지스터가 VCCS처럼 동작하는 영역이다.

npn 기준:

$$
V_{BE}>0
$$

$$
V_{BC}<0
$$

즉 base-emitter는 forward bias, base-collector는 reverse bias이다.

### Edge of active region

$V_{BC}=0$ 근처가 active 영역의 경계이다. $V_{CE}$가 낮아지면 collector-base 접합의 역바이어스가 약해진다.

### Saturation region

$V_{CE}$가 너무 낮아지면 BJT가 더 이상 좋은 전류원처럼 동작하지 않는다. 이때 collector current가 $V_{CE}$의 영향을 크게 받는다.

실무적으로는 soft saturation과 hard saturation을 구분하기도 한다.

## PNP transistor

PNP는 NPN의 complementary device이다. 전류 방향과 전압 극성이 NPN과 반대이다.

PNP에서도 소신호 등가회로의 형태는 NPN과 비슷하게 사용할 수 있지만, 기준 전압과 전류 방향을 주의해야 한다.

## BJT 해석 절차

1. 회로의 DC bias를 해석한다.
2. $V_{BE}$ 또는 회로 방정식으로 $I_C$를 구한다.
3. $V_C$, $V_B$, $V_E$를 계산해 active region인지 확인한다.
4. $g_m$, $r_\pi$, $r_o$를 계산한다.
5. AC 소신호 등가회로로 바꾼다.
6. $v_{out}/v_{in}$, $R_{in}$, $R_{out}$을 구한다.

## 핵심 정리

- BJT는 forward active region에서 전압제어 전류원처럼 동작한다.
- $I_C$는 $V_{BE}$에 지수적으로 의존한다.
- $\beta$는 $I_C$와 $I_B$의 비이다.
- $g_m=I_C/V_T$는 증폭 능력을 나타내는 핵심 파라미터이다.
- $r_\pi=\beta/g_m$이다.
- Early effect는 유한한 출력저항 $r_o=V_A/I_C$로 모델링된다.
- 증폭기 해석은 DC 동작점 결정 후 소신호 등가회로로 넘어간다.

## 연결되는 노트

- [반도체와 PN 접합](01-semiconductors-and-pn-junction.md)
- [BJT 증폭기](04-bjt-amplifiers.md)
- [MOSFET 기본 동작](05-mosfet-operation.md)

## 복습 체크리스트

- [ ] forward active region의 바이어스 조건을 설명할 수 있다.
- [ ] $I_C=I_S e^{V_{BE}/V_T}$를 이용해 전류를 계산할 수 있다.
- [ ] $\beta$, $I_B$, $I_C$, $I_E$의 관계를 쓸 수 있다.
- [ ] $g_m$, $r_\pi$, $r_o$를 DC 동작점에서 계산할 수 있다.
- [ ] Early effect가 출력저항을 만드는 이유를 설명할 수 있다.
- [ ] NPN과 PNP의 전압/전류 방향 차이를 구분할 수 있다.

{% endraw %}

---

이전: [02. 다이오드 응용](02-diode-applications.md) · 다음: [04. BJT 증폭기](04-bjt-amplifiers.md)
