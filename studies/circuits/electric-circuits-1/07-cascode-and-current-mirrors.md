---
layout: page
title: "07. 캐스코드와 전류 미러"
permalink: /studies/circuits/electric-circuits-1/07-cascode-and-current-mirrors/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/07%20%EC%BA%90%EC%8A%A4%EC%BD%94%EB%93%9C%EC%99%80%20%EC%A0%84%EB%A5%98%20%EB%AF%B8%EB%9F%AC.md)

## 한눈에 보기

이 장은 고이득 증폭기와 안정적인 바이어싱을 위한 두 핵심 회로인 cascode와 current mirror를 다룬다. Cascode는 출력저항을 키워 이득을 높이고, current mirror는 기준 전류를 복사해 바이어스 전류를 만든다.

```text
intrinsic gain 한계 -> output resistance boost -> cascode
-> current mirror -> W/L 비율로 전류 복사
-> lambda effect와 PVT variation 대응
```

## Intrinsic gain

common-source 증폭기에서 출력 부하가 무한대에 가까우면 이득은 소자의 intrinsic gain에 가까워진다.

$$
A_v\approx -g_m r_o
$$

하지만 실제 MOSFET의 $r_o$는 유한하다. 높은 전압 이득을 얻으려면 $r_o$를 키우는 구조가 필요하다.

## 출력저항과 이득

common-source 이득은 다음처럼 볼 수 있다.

$$
A_v=-g_m R_{out}
$$

따라서 이득을 키우는 방법은 두 가지이다.

- $g_m$을 키운다.
- $R_{out}$을 키운다.

Cascode는 특히 $R_{out}$을 크게 만드는 구조이다.

## Source degeneration을 통한 출력저항 증가

MOSFET source에 저항 $R_E$가 있으면 출력에서 보이는 저항이 증가한다.

대표식:

$$
R_{out}=R_E+(1+g_m R_E)r_o
$$

여기서 $g_m R_E$는 loop gain처럼 작용한다. $R_E$가 단순 저항이 아니라 다른 트랜지스터의 출력저항이면 출력저항이 크게 boost된다.

## Cascode 구조

Cascode는 common-source 위에 common-gate 소자를 쌓은 구조로 볼 수 있다.

아래 소자는 입력 전압을 전류로 바꾸고, 위 소자는 아래 소자의 drain 전압 변화를 작게 만들어 출력저항을 크게 한다.

간단한 2단 cascode 출력저항은 다음과 같이 근사된다.

$$
R_{out}\approx r_{o1}+(1+g_{m2}r_{o1})r_{o2}
$$

$g_{m2}r_{o1}\gg 1$이면

$$
R_{out}\approx g_{m2}r_{o1}r_{o2}
$$

이다.

따라서 이득은

$$
A_v\approx -g_{m1}R_{out}
$$

로 크게 증가한다.

## Cascode의 직관

Cascode의 장점은 아래 트랜지스터의 drain 전압을 거의 고정해 주는 데 있다. drain 전압 변화가 작으면 channel length modulation의 영향이 줄고, 아래 소자가 더 이상적인 전류원처럼 동작한다.

즉 cascode는 다음 효과를 만든다.

- 출력저항 증가
- 전압 이득 증가
- Miller 효과 감소 가능
- 동작 전압 headroom 요구 증가

## Double cascode

소자를 더 쌓으면 출력저항을 더 크게 만들 수 있다. double cascode는 여러 단계의 출력저항 boost를 중첩한다.

다만 단점도 있다.

- 필요한 전원 전압 headroom이 증가한다.
- 각 소자가 saturation을 유지해야 하므로 bias 설계가 어려워진다.
- 출력 swing 범위가 줄어든다.

## Current mirror의 목적

Current mirror는 기준 전류 $I_{REF}$를 이용해 다른 가지에 동일하거나 비례하는 전류를 만드는 바이어싱 회로이다.

저항 분배 바이어스는 공정, 전압, 온도 변화에 민감할 수 있다. Current mirror는 MOSFET의 matching을 이용해 더 robust한 bias current를 만든다.

## 기본 MOS current mirror

두 MOSFET이 같은 $V_{GS}$를 공유하고 둘 다 saturation에 있으면 전류비는 크기비로 정해진다.

MOSFET saturation 전류:

$$
I_D=\frac{1}{2}\mu C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2
$$

따라서 같은 공정, 같은 $V_{GS}$, 같은 $V_{th}$이면

$$
\frac{I_1}{I_2}
=
\frac{(W/L)_1}{(W/L)_2}
$$

이다.

이 식이 current mirror sizing의 핵심이다.

## 기준 전류가 gate 전압을 만든다

diode-connected MOSFET에 기준 전류 $I_{REF}$를 흘리면 그 전류에 맞는 $V_{GS}$가 결정된다.

$$
V_{GS}=V_{th}+\sqrt{\frac{2I_{REF}}{\mu C_{ox}(W/L)}}
$$

이 gate 전압을 다른 MOSFET에 공유하면 전류가 복사된다.

## 전류 복사 예시

만약 기준 소자의 크기가 $(W/L)_1$이고 복사 소자의 크기가 $(W/L)_2$라면

$$
I_2=I_1\frac{(W/L)_2}{(W/L)_1}
$$

예를 들어 소자 크기를 1배, 2배, 3배로 만들면 기준 전류에 대해 1배, 2배, 3배 전류를 만들 수 있다.

## Lambda effect의 문제

실제 current mirror에서는 $\lambda$ 효과 때문에 출력 전류가 $V_{DS}$에 의존한다.

$$
I_D=
\frac{1}{2}\mu C_{ox}\frac{W}{L}(V_{GS}-V_{th})^2(1+\lambda V_{DS})
$$

따라서 복사 소자와 기준 소자의 $V_{DS}$가 다르면 완전히 같은 전류가 흐르지 않는다.

## Lambda effect를 줄이는 방법

강의 노트에서 제시된 방향은 다음과 같다.

- cascode를 사용해 출력저항을 키운다.
- source degeneration 저항을 사용한다.
- long channel transistor를 사용해 $\lambda$를 줄인다.

long channel을 쓰면 channel length modulation이 작아져 $r_o$가 커진다.

## Bandgap reference 언급

정확한 $I_{REF}$를 만들려면 기준 전류 자체가 안정적이어야 한다. 강의에서는 bandgap reference를 언급한다. 이는 온도와 공정 변화에 덜 민감한 기준 전압/전류를 만들기 위한 회로 블록이다.

## Current mirror 해석 절차

1. diode-connected 기준 소자의 전류로 $V_{GS}$를 결정한다.
2. 같은 gate 전압이 걸리는 복사 소자를 찾는다.
3. 각 소자의 $(W/L)$ 비율을 비교한다.
4. saturation 조건을 확인한다.
5. $\lambda$를 고려해야 하면 $r_o$와 $V_{DS}$ 차이를 반영한다.

## Cascode current mirror

Cascode를 current mirror에 적용하면 출력저항이 증가한다. 따라서 출력 전압 변화에 대한 복사 전류 변화가 줄어든다.

장점:

- 전류 복사 정확도 향상
- 출력저항 증가
- $\lambda$ 효과 감소

단점:

- 더 많은 소자 필요
- 더 큰 전압 headroom 필요

## 핵심 정리

- intrinsic gain은 대략 $g_m r_o$이다.
- cascode는 출력저항을 boost해 이득을 키운다.
- cascode 출력저항은 대략 $g_m r_o^2$ 규모로 커질 수 있다.
- current mirror는 기준 전류를 MOSFET 크기비로 복사한다.
- 전류비는 이상적으로 $(W/L)$ 비율로 결정된다.
- $\lambda$ 효과는 전류 복사의 정확도를 떨어뜨린다.
- cascode current mirror는 출력저항을 키워 전류 안정성을 높인다.

## 연결되는 노트

- [MOSFET 기본 동작](05-mosfet-operation.md)
- [MOSFET 증폭기](06-mosfet-amplifiers.md)
- [CMOS 인버터](08-cmos-inverter.md)

## 복습 체크리스트

- [ ] intrinsic gain $g_m r_o$의 의미를 설명할 수 있다.
- [ ] cascode가 출력저항을 증가시키는 이유를 말할 수 있다.
- [ ] $R_{out}\approx g_m r_o^2$ 형태의 직관을 설명할 수 있다.
- [ ] current mirror의 전류비를 $(W/L)$ 비율로 계산할 수 있다.
- [ ] $\lambda$ 효과가 current mirror 정확도에 미치는 영향을 설명할 수 있다.
- [ ] cascode current mirror의 장단점을 말할 수 있다.


---

이전: [06. MOSFET 증폭기](06-mosfet-amplifiers.md) · 다음: [08. CMOS 인버터](08-cmos-inverter.md)
