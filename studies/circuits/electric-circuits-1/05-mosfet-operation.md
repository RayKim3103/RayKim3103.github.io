---
layout: page
title: "05. MOSFET 기본 동작"
permalink: /studies/circuits/electric-circuits-1/05-mosfet-operation/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/05%20MOSFET%20%EA%B8%B0%EB%B3%B8%20%EB%8F%99%EC%9E%91.md)

{% raw %}
## 한눈에 보기

이 장은 MOSFET의 구조와 전류식을 다룬다. BJT의 한계였던 입력 전류 문제를 줄이고, gate 전압으로 channel을 형성해 drain current를 제어하는 소자라는 점이 핵심이다.

```text
MOS 구조 -> electric field -> inversion -> channel
-> triode region -> saturation region -> gm
-> lambda effect -> ro -> PMOS
```

## MOSFET의 이름과 구조

MOSFET는 Metal-Oxide-Semiconductor Field Effect Transistor의 약자이다.

기본 구조:

- gate: 금속 또는 폴리실리콘 전극
- oxide: $SiO_2$ 절연층
- semiconductor: p-substrate 또는 n-substrate
- source: 캐리어가 들어오는 쪽
- drain: 캐리어가 빠져나가는 쪽

gate와 channel 사이에 oxide가 있으므로 DC gate current는 거의 0이다. 이 점이 BJT의 base current와 큰 차이이다.

## NMOS 구조

NMOS는 p-type substrate 위에 n+ source와 n+ drain을 만든 구조이다.

gate-source 전압 $V_{GS}$가 충분히 커지면 gate 아래에 전자가 모여 n-channel이 생긴다.

임계전압 $V_{th}$보다 작으면 강한 channel이 형성되지 않는다.

$$
V_{GS}<V_{th}: \text{off 또는 weak inversion}
$$

$$
V_{GS}>V_{th}: \text{strong inversion}
$$

## Field effect

MOSFET는 gate 전압이 만드는 전기장으로 channel의 캐리어 양을 조절한다.

$$
V_G \uparrow \Rightarrow Q_{ch}\uparrow \Rightarrow I_D\uparrow
$$

따라서 MOSFET도 BJT처럼 전압제어 전류원으로 사용할 수 있다.

## 동작 영역

NMOS에서 overdrive voltage를 다음처럼 둔다.

$$
V_{OV}=V_{GS}-V_{th}
$$

동작 영역은 $V_{DS}$와 $V_{OV}$의 관계로 결정된다.

### Cutoff

$$
V_{GS}<V_{th}
$$

channel이 거의 없고 전류가 흐르지 않는다.

### Triode region

$$
V_{DS}<V_{GS}-V_{th}
$$

channel이 source에서 drain까지 이어져 있고, MOSFET는 전압제어 가변저항처럼 동작한다.

### Pinch-off

$$
V_{DS}=V_{GS}-V_{th}
$$

drain 쪽 channel이 막히기 시작하는 경계이다.

### Saturation region

$$
V_{DS}>V_{GS}-V_{th}
$$

drain current가 $V_{DS}$에 거의 무관하게 유지되어 전류원처럼 동작한다. 증폭기에서는 보통 이 영역을 사용한다.

## Triode region 전류식

Triode region에서는 MOSFET가 저항처럼 동작한다. 전류는 다음과 같이 근사된다.

$$
I_D=
\mu_n C_{ox}\frac{W}{L}
\left[
(V_{GS}-V_{th})V_{DS}
-\frac{1}{2}V_{DS}^2
\right]
$$

$V_{DS}$가 매우 작으면

$$
I_D\approx
\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})V_{DS}
$$

이므로 저항처럼 볼 수 있다.

## Saturation region 전류식

Saturation region에서는

$$
I_D=\frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2
$$

이다.

MOSFET 전류는 $V_{GS}-V_{th}$의 제곱에 비례한다. BJT의 지수식과 다른 중요한 차이이다.

## Transconductance

MOSFET의 transconductance는 다음으로 정의된다.

$$
g_m=\frac{\partial I_D}{\partial V_{GS}}
$$

saturation region에서

$$
g_m=\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})
$$

또는

$$
g_m=\sqrt{2\mu_n C_{ox}\frac{W}{L}I_D}
$$

로 쓸 수 있다.

overdrive voltage를 쓰면

$$
g_m=\frac{2I_D}{V_{OV}}
$$

이다.

## MOSFET 소신호 모델

기본 소신호 모델은 다음 요소를 갖는다.

- gate 입력은 절연되어 있으므로 DC 입력저항이 매우 크다.
- drain-source 사이에 제어전류원 $g_m v_{gs}$가 있다.
- channel length modulation을 고려하면 $r_o$가 병렬로 붙는다.

기본 출력 전류:

$$
i_d=g_m v_{gs}
$$

## Lambda effect

실제 MOSFET에서는 $V_{DS}$가 커질수록 pinch-off 지점이 이동하고 유효 channel length가 짧아진다. 이 때문에 saturation region에서도 $I_D$가 조금 증가한다.

이를 channel length modulation 또는 lambda effect라고 한다.

모델은 다음과 같다.

$$
I_D=
\frac{1}{2}\mu_n C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2(1+\lambda V_{DS})
$$

출력저항은

$$
r_o=\frac{1}{\lambda I_D}
$$

로 근사한다.

$\lambda$가 작을수록 $r_o$가 커지고 좋은 전류원에 가까워진다.

## BJT와 MOSFET 비교

| 항목 | BJT | MOSFET |
|---|---|---|
| 제어 변수 | $V_{BE}$ | $V_{GS}$ |
| 전류식 | 지수식 | 제곱식 |
| 입력 전류 | base current 존재 | gate DC current 거의 없음 |
| 소신호 이득 파라미터 | $g_m=I_C/V_T$ | $g_m=2I_D/V_{OV}$ |
| 출력저항 원인 | Early effect | lambda effect |

## PMOS

PMOS는 NMOS의 complementary device이다. n-substrate 또는 n-well 위에 p+ source/drain을 만들고, 정공 channel이 형성된다.

PMOS는 NMOS와 극성이 반대이다.

- source가 높은 전위 쪽에 놓이는 경우가 많다.
- gate 전압이 source보다 충분히 낮아지면 켜진다.
- CMOS 회로에서 NMOS와 함께 complementary 구조를 만든다.

## 핵심 정리

- MOSFET는 gate 전기장으로 channel을 제어한다.
- gate oxide 때문에 DC gate current가 거의 0이다.
- NMOS는 $V_{GS}>V_{th}$일 때 channel이 형성된다.
- triode region에서는 가변저항처럼 동작한다.
- saturation region에서는 전류원처럼 동작한다.
- saturation 전류는 $(V_{GS}-V_{th})^2$에 비례한다.
- $g_m=2I_D/V_{OV}$이고 $r_o=1/(\lambda I_D)$이다.
- PMOS는 NMOS의 complementary device이다.

## 연결되는 노트

- [반도체와 PN 접합](01-semiconductors-and-pn-junction.md)
- [BJT 기본 동작](03-bjt-operation.md)
- [MOSFET 증폭기](06-mosfet-amplifiers.md)
- [캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
- [CMOS 인버터](08-cmos-inverter.md)

## 복습 체크리스트

- [ ] MOSFET의 gate, source, drain, substrate 역할을 설명할 수 있다.
- [ ] NMOS의 cutoff, triode, saturation 조건을 쓸 수 있다.
- [ ] triode region과 saturation region의 전류식을 구분할 수 있다.
- [ ] $g_m$을 $I_D$와 $V_{OV}$로 표현할 수 있다.
- [ ] lambda effect와 $r_o$의 관계를 설명할 수 있다.
- [ ] NMOS와 PMOS의 극성 차이를 말할 수 있다.

{% endraw %}

---

이전: [04. BJT 증폭기](04-bjt-amplifiers.md) · 다음: [06. MOSFET 증폭기](06-mosfet-amplifiers.md)
