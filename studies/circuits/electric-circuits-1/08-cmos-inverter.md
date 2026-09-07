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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **08. CMOS 인버터**를 다루며, 반도체 물성에서 diode, BJT, MOSFET 동작과 기본 증폭기 해석으로 이어지는 전자회로 기초를 다진다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 회로 주제에서는 DC 동작점, small-signal 모델, 주파수 응답, feedback 효과를 순서대로 분리한다.
- gain 식은 부호, loading, output resistance, capacitance가 들어가는 위치를 회로 노드에서 추적한다.
- large-signal 동작점과 small-signal 증폭 해석을 분리해야 선형 근사의 의미가 명확해진다.
- diode, BJT, MOSFET 모두 전류-전압 관계와 bias 조건이 먼저이고, 증폭도는 그 주변 미분 특성에서 나온다.
- 회로 해석은 등가모델 선택, 동작 영역 확인, KCL/KVL 적용, 근사 검증의 반복이다.

### 문제 풀이 또는 구현 루틴

- DC bias를 먼저 풀어 동작 영역을 확인하고, 그다음 small-signal 등가회로로 바꾼다.
- 전압 이득, 입력저항, 출력저항을 각각 별도 test source 관점으로 계산한다.
- 근사식은 어떤 항을 무시했는지와 그 조건이 실제 수치에서 성립하는지 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 동작 영역 확인 없이 small-signal gain부터 계산하면 틀리기 쉽다.
- BJT와 MOSFET의 제어 변수와 transconductance 정의를 혼동하지 않는다.
- 부호는 입력 증가가 출력 node 전류와 전압을 어느 방향으로 바꾸는지로 추적한다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 소자는 지금 cutoff, active/saturation, triode 중 어디에 있는가?
- 작은 신호 모델에서 어떤 source가 AC ground가 되는가?
- gain을 높이면 swing, bandwidth, bias 안정성 중 무엇을 희생하는가?
- **08. CMOS 인버터**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [07. 캐스코드와 전류 미러](07-cascode-and-current-mirrors.md)
