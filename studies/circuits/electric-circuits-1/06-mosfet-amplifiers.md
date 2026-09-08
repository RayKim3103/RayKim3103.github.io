---
layout: page
title: "06. MOSFET 증폭기"
permalink: /studies/circuits/electric-circuits-1/06-mosfet-amplifiers/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/06%20MOSFET%20%EC%A6%9D%ED%8F%AD%EA%B8%B0.md)

{% raw %}
## 한눈에 보기

이 장은 MOSFET를 이용한 증폭기 해석을 정리한다. DC bias로 saturation 동작점을 잡고, 소신호 모델로 바꾼 뒤 common-source, source degeneration, 다단 MOS 증폭기 이득을 계산한다.

```text
DC bias -> saturation check -> gm, ro 계산
-> small signal equivalent -> common-source gain
-> source degeneration -> multi-stage MOS amplifier
```

## MOSFET 증폭기의 기본 구조

NMOS common-source 증폭기는 drain에 저항 $R_D$가 있고, gate에 입력을 넣으며, drain에서 출력을 뽑는다.

출력 전압은 drain current 변화가 $R_D$를 지나며 만들어진다.

$$
v_{out}=-i_d R_D
$$

소신호에서

$$
i_d=g_m v_{gs}
$$

이므로 이상적인 common-source 이득은

$$
A_v=-g_m R_D
$$

이다.

## DC bias

MOSFET 증폭기에서도 BJT와 마찬가지로 먼저 DC 동작점을 정해야 한다.

### Resistive divider bias

저항 분배기로 gate 전압을 정한다.

$$
V_G=\frac{R_2}{R_1+R_2}V_{DD}
$$

gate DC current가 거의 0이므로 BJT보다 분압 설계가 단순하다.

### Self bias

drain과 gate를 저항으로 연결하는 self bias에서는 음의 피드백이 생겨 동작점이 안정화된다.

DC에서

$$
V_{DS}=V_{GS}
$$

가 되는 구조가 자주 나오며, 이 경우 saturation 조건

$$
V_{DS}>V_{GS}-V_{th}
$$

을 자연스럽게 만족하기 쉽다.

## AC 해석 규칙

- coupling capacitor는 충분히 크면 AC short로 본다.
- DC 전원 $V_{DD}$는 AC ground로 둔다.
- gate DC current는 0으로 본다.
- MOSFET는 $g_m v_{gs}$ 전류원과 $r_o$로 치환한다.

## Common-source 이득

$r_o$를 무시하면

$$
A_v=-g_m R_D
$$

$r_o$를 고려하면

$$
A_v=-g_m(R_D\parallel r_o)
$$

이다.

입력에 source resistance $R_S$가 있고 gate 커패시턴스 효과를 단순 임피던스로 보면, 입력 감쇠가 이득에 반영된다.

## Gate capacitance와 주파수 영향

MOS 구조는 conductor-insulator-conductor 형태이므로 capacitor를 형성한다.

주파수 영역에서 gate-source capacitance는

$$
Z_{C_{gs}}=\frac{1}{sC_{gs}}
$$

로 동작한다.

저주파에서는 커패시터 임피던스가 커서 입력 전달에 영향을 줄 수 있고, 고주파에서는 작아진다. 강의 노트에서는 단순화해 주파수가 매우 작지 않으면 입력 커패시턴스가 거의 short처럼 작동하는 상황을 다룬다.

## Source degeneration이 있는 common-source

source에 저항 $R_S$ 또는 $R_2$가 들어가면 이득은 다음처럼 줄어든다.

$$
A_v\approx -\frac{R_D}{1/g_m+R_S}
$$

이는 BJT의 emitter degeneration과 같은 역할을 한다.

효과:

- 이득 감소
- 선형성 증가
- 동작점 안정화
- 입력 신호 대비 출력 전류 변화 완화

## 다단 MOS 증폭기

다단 MOS 증폭기에서는 각 단의 이득을 따로 구하고 곱한다.

예를 들어 2단이면

$$
A_v=A_{v1}A_{v2}
$$

각 단의 이득 계산 시 이전 단의 출력저항이 다음 단을 구동하는 Thevenin resistance로 작용할 수 있다.

## BJT와 MOSFET 증폭기 토폴로지 대응

BJT와 MOSFET의 대표 구성은 서로 대응된다.

| BJT | MOSFET | 기능 |
|---|---|---|
| Common-emitter | Common-source | 전압 증폭, 위상 반전 |
| Common-base | Common-gate | 낮은 입력저항, 높은 출력저항 |
| Common-collector | Common-drain | source follower, voltage buffer |

## Current source load가 있는 MOS 증폭기

저항 대신 current source 또는 active load를 drain에 놓으면 출력저항이 커져 이득이 증가한다.

기본적으로

$$
A_v=-g_m R_{out}
$$

이므로 $R_{out}$을 크게 만드는 구조가 중요하다.

## Diode-connected MOS 부하

gate와 drain이 연결된 MOSFET는 소신호에서 저항처럼 보인다.

대략적인 저항은

$$
R\approx \frac{1}{g_m}
$$

이다.

이를 부하 또는 bias 소자로 사용할 수 있다.

## 예제형 이득 공식 감각

자료에 등장하는 여러 예제는 공통적으로 다음 방식으로 정리된다.

1. 각 MOSFET가 saturation인지 확인한다.
2. 각 소자의 $g_m$, $r_o$를 계산한다.
3. 출력 노드에서 보이는 등가저항을 구한다.
4. $A_v=-g_m R_{out}$ 또는 source degeneration 공식을 적용한다.
5. 다단이면 단계별 이득을 곱한다.

예를 들어 current source load가 있고 $\lambda=0$이면 이상적으로 출력저항이 무한대가 되어 이득이 매우 커진다. 실제로는 $\lambda\neq 0$이므로 $r_o$가 유한하고 이득도 제한된다.

## 핵심 정리

- MOSFET 증폭기는 saturation region에서 사용한다.
- common-source 이득은 기본적으로 $-g_m R_D$이다.
- $r_o$를 고려하면 $R_D\parallel r_o$가 출력저항이 된다.
- source degeneration은 이득을 낮추지만 선형성을 높인다.
- gate capacitance는 주파수 응답에 영향을 준다.
- active load와 current source load는 출력저항을 키워 이득을 높인다.
- 다단 증폭기는 각 단의 loading을 고려해 이득을 곱한다.

## 연결되는 노트

- [MOSFET 기본 동작](05-mosfet-operation.md)
- [BJT 증폭기](04-bjt-amplifiers.md)
- [캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
- [CMOS 인버터](08-cmos-inverter.md)

## 복습 체크리스트

- [ ] MOSFET 증폭기의 DC bias와 AC 해석을 분리할 수 있다.
- [ ] common-source 이득의 부호가 음수인 이유를 설명할 수 있다.
- [ ] $A_v=-g_m(R_D\parallel r_o)$를 적용할 수 있다.
- [ ] source degeneration이 이득에 미치는 영향을 계산할 수 있다.
- [ ] diode-connected MOS의 소신호 저항을 설명할 수 있다.
- [ ] BJT CE/CB/CC와 MOS CS/CG/CD의 대응을 말할 수 있다.

{% endraw %}

---

이전: [05. MOSFET 기본 동작](05-mosfet-operation.md) · 다음: [07. 캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
