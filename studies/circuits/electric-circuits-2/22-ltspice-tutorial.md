---
layout: page
title: "22. LTSpice Tutorial - 시뮬레이션 튜토리얼"
permalink: /studies/circuits/electric-circuits-2/22-ltspice-tutorial/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/22%20LTSpice%20Tutorial%20-%20%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%EC%85%98%20%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC.md)

{% raw %}
## 핵심 요약

이 자료는 LTspice 설치부터 NCSU 45nm CMOS model 설정, inverter schematic 작성, DC operating point, DC sweep, parametric sweep, AC analysis, transient simulation까지 실습 절차를 설명한다. 마지막에는 CS amplifier homework를 통해 `VTH`, `gm`, `ro`, gain, 3-dB bandwidth를 simulation으로 측정한다.

## LTspice란

LTspice는 Analog Devices가 제공하는 무료 circuit simulation program이다. 전력회로, analog 회로, power system 설계에 널리 사용되며, schematic 기반으로 다양한 SPICE simulation을 수행할 수 있다.

## 주요 단축키

| 기능 | 단축키 |
|---|---|
| Configure analysis | A |
| Run/Pause | Alt+R |
| Stop | Alt+S |
| Zoom to fit | Space |
| Component | P |
| Wire | W |
| Ground | G |
| Voltage source | V |
| Resistor | R |
| Capacitor | C |
| Inductor | L |
| Net name | N |
| SPICE directive | . |

## LTspice 단위 표기

| 표기 | 값 |
|---|---:|
| k | `1e3` |
| MEG | `1e6` |
| G | `1e9` |
| m | `1e-3` |
| u | `1e-6` |
| n | `1e-9` |
| p | `1e-12` |
| f | `1e-15` |

주의: LTspice에서 `M`은 mega가 아니라 milli로 해석될 수 있으므로 `MEG`를 쓴다.

## NCSU 45nm CMOS Model 설정

이 과목에서는 NCSU 45nm CMOS model을 사용한다.

절차:

1. `models_nom` 폴더 준비
2. LTspice setting의 user libraries directory에 추가
3. schematic에 `.inc` directive로 model file 포함
4. MOSFET instance model name을 `NMOS_VTL`, `PMOS_VTL` 등으로 설정
5. length/width 지정

## Inverter Design 예

구성:

- `nmos4`
- `pmos4`
- VDD voltage source
- input voltage source
- ground
- IN/OUT net label
- PMOS body는 VDD에 연결
- NMOS body는 ground에 연결

PMOS symbol에서 drain/source 표시가 기대와 다를 수 있으므로 simulation 결과의 terminal naming에 주의한다.

## DC Operating Point Simulation

목적:

- 각 node voltage 확인
- device current 확인
- MOSFET operating region 확인

특징:

- capacitor는 open으로 처리
- inductor는 short으로 처리

사용 예:

- bias voltage 확인
- saturation 조건 확인
- current mirror 동작 확인

## DC Sweep Simulation

목적:

- DC source 값을 바꾸며 voltage/current curve 관찰

예:

- inverter VTC
- `Id` vs `Vgs`
- threshold voltage 추정

## Parametric Simulation

특정 parameter를 sweep한다.

예:

```spice
.step param width_nmos 0.5u 1u 0.1u
```

component 값에는 `{width_nmos}`처럼 parameter 이름을 넣는다.

활용:

- transistor width 변화에 따른 gain 비교
- resistor/capacitor tuning
- design trade-off 탐색

## AC Analysis

목적:

- frequency response 확인
- magnitude/phase Bode plot
- DC gain
- 3-dB bandwidth

주의:

- AC analysis는 time-domain simulation이 아니다.
- 입력 source의 AC amplitude를 보통 1로 설정한다.

결과 분석:

- magnitude dB plot
- phase plot 제거 가능
- cursor로 gain과 3-dB bandwidth 측정

## Transient Simulation

목적:

- time-domain response 확인
- sine/pulse/PWL 입력에 대한 출력 파형 확인
- eye diagram 생성

simulation time이 너무 길면 파형이 조밀해 보이므로 x-axis range를 조정한다.

## Homework 핵심

CS amplifier 조건:

- `VDD = 1.2 V`
- `VSS = 0 V`
- `RD = 1.4 kOhm`
- `M1 length = 180 nm`
- `M1 width = 4.5 um`
- `CL = 100 fF`
- `Vin DC offset = 0.6 V`
- amplitude `0.05 V`
- frequency `300 MHz`

해야 할 일:

1. `Id-Vgs` curve로 `VTH` 결정
2. DC sweep에서 `gm` plot
3. `VGS = 0.6 V`에서 `ro` 결정
4. transient simulation과 AC analysis 실행
5. DC gain과 3-dB bandwidth 측정

## 시험/실습 포인트

- `.op`, `.dc`, `.ac`, `.tran`, `.step`의 용도를 구분한다.
- AC amplitude를 1로 두면 transfer function을 바로 읽기 쉽다.
- `D(Id(M1))` 같은 derivative expression으로 `gm`을 얻을 수 있다.
- `ro`는 `Id-Vds` curve의 기울기 역수로 구한다.
- CMOS body connection을 반드시 확인한다.

## 같이 보면 좋은 노트

- [MOS Small-Signal Characteristics - MOS 소신호 모델](03-mos-small-signal-characteristics.md)
- [Common-Source Amplifier - CS 증폭기](04-common-source-amplifier-cs.md)
- [Project Design Guide - TIA CTLE 설계 가이드](23-project-design-guide-tia-ctle.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **22. LTSpice Tutorial - 시뮬레이션 튜토리얼**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 실습/과제 문서는 재현 절차, 입력 조건, 기대 출력, 디버깅 로그, 성능 또는 정확도 검증 기준을 함께 남겨야 한다.
- 보고서형 문서라면 단순 결과보다 설계 선택, 실패 원인, 수정 근거가 드러날수록 복습 가치가 커진다.
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
- **22. LTSpice Tutorial - 시뮬레이션 튜토리얼**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [21. Feedback - 음귀환 기초](21-feedback.md) · 다음: [23. Project Design Guide - TIA CTLE 설계 가이드](23-project-design-guide-tia-ctle.md)
