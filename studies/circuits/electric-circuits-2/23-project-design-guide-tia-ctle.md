---
layout: page
title: "23. Project Design Guide - TIA CTLE 설계 가이드"
permalink: /studies/circuits/electric-circuits-2/23-project-design-guide-tia-ctle/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/23%20Project%20Design%20Guide%20-%20TIA%20CTLE%20%EC%84%A4%EA%B3%84%20%EA%B0%80%EC%9D%B4%EB%93%9C.md)

{% raw %}
## 핵심 요약

이 자료는 설계 프로젝트의 전체 구조와 목표 스펙을 설명한다. 대상 회로는 optical receiver이며, photodiode가 optical signal을 current signal로 변환하고, TIA가 current를 voltage로 바꾸며, CTLE가 PD/TIA의 제한된 bandwidth를 보상한다. 성능은 frequency response, eye diagram, power consumption, FoM으로 평가한다.

## 전체 시스템

```text
Optical signal
-> Photodiode
-> Current signal
-> TIA
-> Voltage signal
-> CTLE
-> Output signal
```

역할:

- PD: optical-to-current conversion
- TIA: current-to-voltage conversion and amplification
- CTLE: bandwidth extension and ISI reduction

## Eye Diagram

eye diagram은 unit interval 단위로 time waveform을 겹쳐 표시한다.

주요 지표:

- eye height: vertical noise margin
- eye width: timing margin

자료 기준 예:

```text
eye height ≈ 0.7 Vo
0.15Vo, 0.85Vo 기준 cursor 사용
```

10Gbps data의 unit interval:

```text
UI = 1 / 10Gbps = 100 ps
```

## OTA 설계 목표

OTA는 TIA의 amplifier core로 사용된다.

이론적 gain 형태:

```text
Av ≈ gm1 gm6 (ro2 || ro4)(ro6 || ro7)
```

target:

- OTA open-loop gain > 15 dB
- OTA open-loop bandwidth > 6.5 GHz

중요:

- `IREF`와 input common-mode voltage를 조절해 모든 transistor가 saturation에 있도록 한다.
- gain과 bandwidth는 `gm`, `ro`, parasitic capacitance의 trade-off이다.

## TIA 설계

TIA는 shunt negative feedback 구조의 transimpedance amplifier이다.

입력/출력:

```text
input: current
output: voltage
```

target:

```text
Transimpedance gain > 60 dBOhm
```

피드백 저항 `RF`가 gain과 bandwidth에 모두 영향을 준다.

## CTLE 설계

CTLE는 PD+TIA의 high-frequency loss를 보상하기 위해 zero를 배치한다.

### 입력 High-Pass Filter

입력 bias를 만들고 DC를 차단한다.

```text
Vx/Vin = s(R1 || R2)Ci / [1 + s(R1 || R2)Ci]
```

DC bias:

```text
Vx(DC) = R2/(R1+R2) * VDD
```

target:

```text
cut-off frequency < 1 MHz
```

### CTLE Core

source degeneration capacitor를 이용한 high-frequency boosting:

```text
Vout/Vx = -gm RD (1 + s RS CS) /
          (1 + gm RS + s RS CS)
```

zero:

```text
wz = 1 / (RS CS)
```

pole:

```text
wp = (1 + gm RS) / (RS CS)
```

zero를 PD+TIA bandwidth roll-off 근처에 배치해 loss를 보상한다.

## Overall Peaking

overall peaking은 system gain의 maximum과 low-frequency gain 사이 차이이다.

target:

```text
overall peaking < 1.5 dB
```

peaking이 너무 크면 high-frequency noise가 과도하게 증폭되고 eye가 왜곡될 수 있다.

## LTspice Eye Diagram 설정

transient setting 예:

- stop time: 300 ns
- time to start saving: 150 ns
- maximum timestep: 1 ps

초기 0-150ns는 high-pass filter settling 때문에 버리고, 150-300ns 구간을 eye diagram에 사용한다.

SPICE directive:

```spice
.option baudrate={1/100p}
```

## Photodiode Simulation

PD symbol 파일을 user library directory에 넣고 사용한다.

simulation 모드:

- transient: `PWL FILE`에 `project_input.txt` 지정
- AC analysis: current input의 AC amplitude를 1로 설정

## Power Consumption 측정

transient simulation에서 VDD source로 들어가는 전류 평균을 사용한다.

예:

```spice
.meas TRAN Power_consumption 1*AVG I(V1)
```

SPICE output log에서 값을 확인한다.

## Target Specification

| 항목 | 목표 |
|---|---:|
| Data rate | 10 Gbps |
| OTA open-loop gain | > 15 dB |
| OTA open-loop bandwidth | > 6.5 GHz |
| Transimpedance gain | > 60 dBOhm |
| HPF cut-off | < 1 MHz |
| Overall bandwidth | > 4.5 GHz |
| Overall peaking | < 1.5 dB |
| Power consumption | < 5 mW |

FoM:

```text
FoM = eye height * eye width / power consumption
```

## 같이 보면 좋은 노트

- [Design Project Summary - Optical Receiver PD TIA CTLE](00-design-project-summary-optical-receiver-pd-tia-ctle.md)
- [OTA and Op-Amp - OTA와 연산증폭기](15-ota-and-op-amp.md)
- [Feedback - 음귀환 기초](21-feedback.md)
- [LTSpice Tutorial - 시뮬레이션 튜토리얼](22-ltspice-tutorial.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **23. Project Design Guide - TIA CTLE 설계 가이드**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **23. Project Design Guide - TIA CTLE 설계 가이드**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [22. LTSpice Tutorial - 시뮬레이션 튜토리얼](22-ltspice-tutorial.md) · 다음: [24. IO Resistance Improvement with Feedback 1 - 전압 증폭기 피드백](24-io-resistance-improvement-with-feedback-1.md)
