---
layout: page
title: "08. CMOS 인버터"
permalink: /studies/circuits/electric-circuits-1/08-cmos-inverter/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%201/lecture_notes/08%20CMOS%20%EC%9D%B8%EB%B2%84%ED%84%B0.md)

{% raw %}
## 한눈에 보기

이 장은 디지털 회로의 기본 블록인 CMOS 인버터를 다룬다. 저항 부하 인버터의 한계에서 시작해 NMOS와 PMOS를 상보적으로 사용하면 전력과 속도 측면에서 왜 유리한지 설명한다.

```text
인버터 기능 -> 저항 부하 NMOS 인버터
-> power/speed tradeoff -> CMOS inverter
-> delay -> dynamic power -> CMOS logic gates
```

## 인버터의 역할

인버터는 입력 논리를 반전하는 게이트이다.

| 입력 | 출력 |
|---:|---:|
| 0 | 1 |
| 1 | 0 |

전압으로는 다음처럼 해석한다.

$$
0 \rightarrow V_{DD}
$$

$$
V_{DD} \rightarrow 0
$$

인버터는 NAND, NOR, sequential logic, processor 같은 디지털 회로의 기본 building block이다.

## 저항 부하 NMOS 인버터

초기 후보 구조는 위에 저항 $R_D$, 아래에 NMOS를 둔 인버터이다.

- 입력이 0이면 NMOS off, 출력은 $V_{DD}$
- 입력이 1이면 NMOS on, 출력은 0에 가까워짐

문제는 power와 speed tradeoff이다.

### Static power

NMOS가 켜져 있을 때 $V_{DD}$에서 저항과 NMOS를 통해 DC 전류가 흐른다.

$$
I_D\approx \frac{V_{DD}}{R_D}
$$

전력 소모를 줄이려면 $R_D$를 크게 해야 한다.

### Speed

출력 노드에는 load capacitance $C_L$이 있다. 저항과 커패시터의 1차 응답은

$$
V_{out}=V_{\text{final}}+(V_{\text{initial}}-V_{\text{final}})e^{-t/(R_DC_L)}
$$

이다.

95% 정도 충전되는 시간은 대략

$$
T_{95\%}\approx 3R_DC_L
$$

이므로 빠르게 만들려면 $R_D$를 작게 해야 한다.

즉 저항 부하 인버터는

- power를 줄이려면 $R_D$를 크게
- speed를 높이려면 $R_D$를 작게

해야 하므로 tradeoff가 생긴다.

## CMOS 인버터

CMOS 인버터는 NMOS와 PMOS를 상보적으로 연결한다.

- 위쪽 PMOS: pull-up network
- 아래쪽 NMOS: pull-down network

입력과 출력 관계:

### 입력이 0일 때

- NMOS off
- PMOS on
- 출력은 $V_{DD}$

### 입력이 1일 때

- NMOS on
- PMOS off
- 출력은 0

정상 상태에서는 $V_{DD}$에서 ground로 직접 흐르는 DC 경로가 거의 없다. 그래서 static power가 매우 작다.

## CMOS 인버터의 전달특성

입력 $V_{in}$이 낮을 때 출력은 높다. 입력이 증가하면 어느 지점에서 출력이 급격히 내려가고, 입력이 높을 때 출력은 낮다.

동작 구간은 NMOS와 PMOS의 영역 조합으로 나뉜다.

- 낮은 입력: NMOS off, PMOS on
- 중간 입력: 둘 다 일부 켜짐, 전환 구간
- 높은 입력: NMOS on, PMOS off

중간의 전환점은 trip point라고 볼 수 있다. 보통 noise margin과 switching threshold를 결정하는 중요한 지점이다.

## CMOS 인버터의 속도

출력 전환은 load capacitance $C_L$을 충전하거나 방전하는 과정이다.

- low to high: PMOS가 $C_L$을 충전
- high to low: NMOS가 $C_L$을 방전

MOSFET의 on 저항을 $R_{on}$으로 근사하면 delay는 다음에 비례한다.

$$
T_{PLH}\propto R_{on,p}C_L
$$

$$
T_{PHL}\propto R_{on,n}C_L
$$

자료에서는 더 자세한 식으로 $V_{th}$와 $V_{DD}$가 포함된 delay 식도 정리한다. 핵심은 on resistance가 작고 load capacitance가 작을수록 빠르다는 점이다.

## On resistance

MOSFET가 켜졌을 때의 on resistance는 대략 transconductance의 역수와 관련된다.

$$
R_{on}\approx \frac{1}{g_m}
$$

또는 공정과 크기 관점에서

$$
R_{on}\propto \frac{1}{\mu C_{ox}(W/L)(V_{GS}-V_{th})}
$$

따라서 폭 $W$를 키우면 $R_{on}$이 줄어 속도는 빨라지지만, gate capacitance와 면적이 증가할 수 있다.

## Power dissipation

CMOS의 주요 전력 소모는 switching 때 load capacitance를 충방전하면서 발생한다.

출력 커패시터에 저장되는 에너지는

$$
E=\frac{1}{2}C_LV_{DD}^2
$$

충전과 방전이 반복되면 평균 동적 전력은 clock 또는 switching frequency에 비례한다.

대표식:

$$
P\approx f C_L V_{DD}^2
$$

정확히는 activity factor $\alpha$를 포함해

$$
P_{dyn}=\alpha C_L V_{DD}^2 f
$$

로 쓴다.

강의 노트의 핵심 감각:

- 전력은 $C_L$에 비례한다.
- 전력은 switching frequency에 비례한다.
- 전력은 $V_{DD}^2$에 비례한다.
- CMOS scaling에서 $V_{DD}$를 낮추는 것이 중요하다.

## CMOS의 장점

CMOS는 저항 부하 NMOS 인버터의 tradeoff를 완화한다.

- static power가 작다.
- pull-up과 pull-down을 transistor로 구성해 면적 효율이 좋다.
- 출력이 rail-to-rail로 나온다.
- 디지털 게이트 구현에 적합하다.

## CMOS OR, NOR, NAND 게이트

CMOS 논리 게이트는 PMOS pull-up network와 NMOS pull-down network를 상보적으로 설계한다.

### NOR 게이트

NOR는 입력 중 하나라도 1이면 출력이 0이다.

- NMOS는 병렬로 구성해 하나라도 켜지면 pull-down
- PMOS는 직렬로 구성해 모든 입력이 0일 때만 pull-up

진리표:

| X | Y | NOR |
|---:|---:|---:|
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 0 |

### NAND 게이트

NAND는 두 입력이 모두 1일 때만 출력이 0이다.

- NMOS는 직렬로 구성해 모두 켜질 때만 pull-down
- PMOS는 병렬로 구성해 하나라도 입력이 0이면 pull-up

진리표:

| X | Y | NAND |
|---:|---:|---:|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### OR 게이트

CMOS에서 OR는 보통 NOR 뒤에 인버터를 붙여 구현한다. 기본 CMOS pull-up/pull-down 구조는 자연스럽게 NAND와 NOR를 만들기 쉽기 때문이다.

## 설계 감각

CMOS 디지털 설계에서 중요한 균형은 다음과 같다.

- 큰 $W$: 낮은 $R_{on}$, 빠른 전환, 큰 capacitance
- 작은 $W$: 작은 capacitance, 작은 면적, 느린 전환
- 낮은 $V_{DD}$: 전력 감소, 속도와 noise margin 감소 가능
- 작은 $C_L$: 빠른 속도와 낮은 전력

## 핵심 정리

- 인버터는 디지털 회로의 기본 building block이다.
- 저항 부하 NMOS 인버터는 power와 speed 사이 tradeoff가 크다.
- CMOS 인버터는 PMOS와 NMOS를 상보적으로 사용한다.
- 정상 상태에서 DC current가 거의 없어 static power가 작다.
- 지연시간은 대략 $R_{on}C_L$에 비례한다.
- 동적 전력은 $C_LV_{DD}^2f$에 비례한다.
- NAND와 NOR는 CMOS pull-up/pull-down 네트워크로 자연스럽게 구현된다.

## 연결되는 노트

- [MOSFET 기본 동작](05-mosfet-operation.md)
- [MOSFET 증폭기](06-mosfet-amplifiers.md)
- [캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)

## 복습 체크리스트

- [ ] 저항 부하 NMOS 인버터의 power-speed tradeoff를 설명할 수 있다.
- [ ] CMOS 인버터에서 입력 0과 입력 1일 때 NMOS/PMOS 상태를 말할 수 있다.
- [ ] $R_{on}C_L$이 delay를 결정하는 이유를 설명할 수 있다.
- [ ] $P_{dyn}=\alpha C_LV_{DD}^2f$의 각 항 의미를 말할 수 있다.
- [ ] NAND와 NOR의 CMOS pull-up/pull-down 구조를 그릴 수 있다.

{% endraw %}

---

이전: [07. 캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
