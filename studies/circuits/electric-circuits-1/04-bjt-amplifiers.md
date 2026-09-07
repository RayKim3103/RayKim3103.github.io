---
layout: page
title: "04. BJT 증폭기"
permalink: /studies/circuits/electric-circuits-1/04-bjt-amplifiers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/04%20BJT%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

## 한눈에 보기

이 장은 BJT 증폭기를 실제로 설계하고 해석하는 방법을 다룬다. 핵심은 임피던스, DC bias, 소신호 파라미터, AC 등가회로, common-emitter/common-base/common-collector 구성, 다단 증폭기 이득 계산이다.

```text
임피던스 개념 -> DC bias -> gm, r_pi, r_o
-> AC equivalent circuit -> CE/CB/CC
-> emitter degeneration -> multi-stage gain
```

## 증폭기에서 임피던스가 중요한 이유

증폭기는 앞단과 뒷단에 연결되어 사용된다. 따라서 이득만 보면 안 되고 입력저항과 출력저항을 함께 봐야 한다.

전압 증폭기에서 이상적인 조건은 다음과 같다.

- 입력저항이 커야 입력 전압을 덜 깎아먹는다.
- 출력저항이 작아야 부하에 전압을 잘 전달한다.

전류 증폭기에서 이상적인 조건은 다음과 같다.

- 입력저항이 작아야 입력 전류를 잘 받아들인다.
- 출력저항이 커야 부하 변화에도 전류를 잘 유지한다.

## 입력저항 예시

입력 소스에 내부저항 $R_S$가 있고 증폭기 입력저항이 $R_{in}$이면 실제 입력 노드 전압은

$$
v_1=\frac{R_{in}}{R_S+R_{in}}v_{in}
$$

이다. $R_{in}$이 클수록 입력 신호 손실이 작다.

## 출력저항 예시

증폭기 출력저항이 $R_{amp}$이고 부하가 $R_S$이면 부하에 걸리는 전압은

$$
v_{out}=\frac{R_S}{R_{amp}+R_S}v_{amp}
$$

이다. $R_{amp}$가 작을수록 출력 전달이 좋아진다.

## BJT의 단자별 임피던스 감각

소신호 모델에서 대략적인 크기 관계는 다음과 같다.

```text
collector impedance > base impedance > emitter impedance
```

대표적으로

- base impedance: $r_\pi$
- collector impedance: $r_o$
- emitter impedance: 약 $1/g_m$

이다.

## Base impedance

base에서 들여다보면 입력은 $r_\pi$를 본다.

$$
R_{in,base}=r_\pi
$$

emitter에 저항 $R_E$가 있으면 base에서 볼 때 크게 확대되어 보인다.

$$
R_{in}\approx r_\pi+(\beta+1)R_E
$$

이를 emitter degeneration의 입력저항 증가 효과라고 볼 수 있다.

## Collector impedance

base와 emitter가 AC ground이고 collector에서 들여다보면 출력저항은 대략

$$
R_{out}\approx r_o
$$

이다.

회로에 collector 저항 $R_C$가 있으면 실제 출력저항은 보통

$$
R_{out}\approx R_C\parallel r_o
$$

로 본다.

## Emitter impedance

emitter에서 들여다보는 저항은 매우 작다.

$$
R_{in,emitter}\approx \frac{1}{g_m}
$$

좀 더 정확히는 $r_\pi$, $r_o$와 병렬 형태가 포함될 수 있지만, 보통 $1/g_m$이 지배적이다.

## Reduction theorem

BJT 소신호 회로에서는 한쪽 단자의 임피던스가 다른 쪽에서 $\beta+1$배 확대되거나 축소되어 보인다.

대표 결과:

- emitter의 저항 $R_E$는 base에서 약 $(\beta+1)R_E$로 보인다.
- base의 저항 $R_B$는 emitter에서 약 $R_B/(\beta+1)$로 보인다.

이 성질을 이용하면 KCL을 매번 쓰지 않고도 임피던스를 빠르게 계산할 수 있다.

## DC bias의 목적

증폭기는 작은 AC 신호를 선형적으로 증폭해야 한다. 이를 위해 트랜지스터를 적절한 DC 동작점에 놓아야 한다.

DC bias가 없으면 입력 신호가 곡선의 비선형 구간을 크게 오가며 왜곡된다.

### 해석 규칙

- DC 해석에서 커패시터는 open circuit이다.
- AC 소신호 해석에서 충분히 큰 coupling capacitor는 short circuit로 본다.
- DC 전원은 AC 해석에서 ground로 둔다.

## Resistive divider bias

저항 분배 바이어스는 $R_1$, $R_2$로 base 전압을 정한다.

$$
V_B=\frac{R_2}{R_1+R_2}V_{CC}
$$

이 방식이 잘 작동하려면 분압 전류가 base current보다 충분히 커야 한다.

$$
I_X \gg I_B
$$

그래야 base current가 분압점을 크게 흔들지 않는다.

## Self bias

collector와 base 사이에 저항을 두는 self bias는 출력 전압 변화가 base bias에 되먹임을 주어 동작점을 안정화한다.

회로 방정식은 대략 다음 형태이다.

$$
V_{CC}=I_C R_C+I_B R_B+V_{BE}
$$

$$
I_B=\frac{I_C}{\beta}
$$

따라서

$$
I_C=\frac{V_{CC}-V_{BE}}{R_C+R_B/\beta}
$$

근사적으로 구할 수 있다. 정확도를 높이려면 $V_{BE}=V_T\ln(I_C/I_S)$와 함께 반복 계산한다.

## Common-emitter 증폭기

common-emitter는 입력을 base에 넣고 출력을 collector에서 뽑는다.

기본 소신호 이득은

$$
A_v\approx -g_m(R_C\parallel r_o)
$$

이다.

$r_o$를 무시하고 source resistance $R_S$와 $r_\pi$를 고려하면

$$
A_v=
-g_m R_C\frac{r_\pi}{R_S+r_\pi}
$$

또는

$$
A_v=
-\frac{\beta R_C}{R_S+r_\pi}
$$

로 쓸 수 있다.

부호가 음수인 이유는 입력 base 전압이 증가하면 collector current가 증가하고, $R_C$ 전압강하가 커져 collector 전압이 내려가기 때문이다.

## Emitter degeneration이 있는 CE

emitter에 저항 $R_E$가 있으면 이득은 줄지만 선형성과 안정성이 좋아진다.

대표 이득 근사:

$$
A_v\approx
-\frac{R_C}{R_S/\beta+r_\pi/\beta+R_E}
$$

또는 $r_\pi/\beta\approx 1/g_m$을 사용해

$$
A_v\approx
-\frac{R_C}{R_E+1/g_m}
$$

로 해석할 수 있다.

Emitter degeneration의 효과:

- 이득 감소
- 선형성 향상
- 입력저항 증가
- 동작점 안정화

## Common-base 증폭기

common-base는 입력을 emitter에 넣고 출력을 collector에서 뽑는다.

특징:

- 입력저항이 낮다.
- 출력저항이 높다.
- 전압 이득은 양수이다.
- 전류 증폭기로 유용하다.

대표 근사:

$$
R_{in}\approx \frac{1}{g_m}
$$

$$
R_{out}\approx R_C
$$

$$
A_v\approx g_m R_C
$$

## Common-collector 증폭기

common-collector는 emitter follower라고도 한다. 입력을 base에 넣고 출력을 emitter에서 뽑는다.

특징:

- 전압 이득은 1보다 약간 작고 양수이다.
- 입력저항이 높다.
- 출력저항이 낮다.
- voltage buffer로 적합하다.

부하 $R_L$이 emitter에 있으면

$$
A_v\approx \frac{R_L}{R_L+1/g_m}
$$

이다.

## 증폭기 토폴로지 비교

| 구성 | 입력 | 출력 | 전압 이득 부호 | 입력저항 | 출력저항 | 용도 |
|---|---|---|---:|---:|---:|---|
| CE | base | collector | 음수 | 중간/높음 | 높음 | 전압 증폭 |
| CB | emitter | collector | 양수 | 낮음 | 높음 | 전류 전달, 고주파 |
| CC | base | emitter | 양수 | 높음 | 낮음 | 버퍼 |

## 다단 증폭기 이득 계산

다단 증폭기는 각 단을 따로 계산한 뒤 곱한다.

기본 절차:

1. 첫 번째 단의 이득을 다음 단을 고려하지 않고 구한다.
2. 첫 번째 단의 출력저항 또는 Thevenin 저항을 다음 단의 구동저항으로 본다.
3. 두 번째 단의 이득을 계산한다.
4. 전체 이득은 각 단의 곱이다.

$$
A_v=A_{v1}A_{v2}\cdots A_{vn}
$$

다단 해석에서 중요한 점은 이전 단의 출력저항과 다음 단의 입력저항이 서로 loading effect를 만든다는 것이다.

## 토폴로지를 섞어 쓰는 이유

한 가지 증폭기 구성만으로 모든 요구사항을 만족하기 어렵다.

- 큰 전압 이득이 필요하면 CE 또는 CS를 쓴다.
- 낮은 입력저항과 높은 출력저항이 필요하면 CB를 쓴다.
- 높은 입력저항과 낮은 출력저항이 필요하면 CC를 쓴다.

예를 들어 CE 뒤에 CC를 붙이면 전압 이득을 만든 뒤 부하를 잘 구동할 수 있다.

## 핵심 정리

- 증폭기 설계는 이득뿐 아니라 입력저항과 출력저항을 함께 본다.
- BJT 소신호 파라미터는 DC 동작점에서 결정된다.
- CE는 큰 전압 이득을 제공하지만 위상이 반전된다.
- emitter degeneration은 이득을 낮추는 대신 안정성과 선형성을 높인다.
- CB는 낮은 입력저항, 높은 출력저항을 갖는다.
- CC는 voltage buffer로 유용하다.
- 다단 증폭기 전체 이득은 loading을 반영해 단계별로 계산한다.

## 연결되는 노트

- [BJT 기본 동작](03-bjt-operation.md)
- [MOSFET 기본 동작](05-mosfet-operation.md)
- [MOSFET 증폭기](06-mosfet-amplifiers.md)
- [캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)

## 복습 체크리스트

- [ ] 입력저항이 전압 전달에 미치는 영향을 설명할 수 있다.
- [ ] DC 해석과 AC 해석에서 커패시터 처리를 구분할 수 있다.
- [ ] $g_m$, $r_\pi$, $r_o$를 이용해 소신호 등가회로를 그릴 수 있다.
- [ ] CE 이득의 음수 부호를 설명할 수 있다.
- [ ] emitter degeneration이 이득과 입력저항에 미치는 영향을 계산할 수 있다.
- [ ] CE, CB, CC의 입력/출력 단자와 용도를 구분할 수 있다.
- [ ] 다단 증폭기의 loading effect를 반영해 전체 이득을 구할 수 있다.


---

이전: [03. BJT 기본 동작](03-bjt-operation.md) · 다음: [05. MOSFET 기본 동작](05-mosfet-operation.md)
