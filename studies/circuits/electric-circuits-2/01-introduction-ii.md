---
layout: page
title: "01. Introduction - 전자회로 II 개요"
permalink: /studies/circuits/electric-circuits-2/01-introduction-ii/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes/01%20Introduction%20-%20%EC%A0%84%EC%9E%90%ED%9A%8C%EB%A1%9C%20II%20%EA%B0%9C%EC%9A%94.md)

{% raw %}
## 핵심 요약

전자회로 II는 MOS 전자회로를 복습하고, 증폭기, 주파수 응답, 필터, 피드백, 발진기, 설계 프로젝트까지 확장하는 과목이다. 전자공학의 목적은 전자 또는 전하 운반자의 흐름을 제어하여 amplification, filtering, logic, memory, computing 같은 유용한 기능을 수행하는 것이다.

## Electronics란 무엇인가

Electronics는 고유한 V-I 특성을 가진 소자를 이용해 전자의 흐름을 제어하는 과학과 기술이다.

기본 소자:

- resistor
- capacitor
- inductor
- diode
- transistor

회로 기능:

- amplification
- filtering
- logic operation
- memory
- computing

## 전자공학 역사 흐름

| 시기 | 사건 | 의미 |
|---|---|---|
| 1904 | Fleming tube | 최초의 전자 소자, diode, rectification |
| 1906 | Audion triode | vacuum tube amplifier 시작 |
| 1946 | ENIAC | vacuum tube computer |
| 1947 | transistor 발명 | Bell Labs, Bardeen/Shockley/Brattain |
| 1958-1959 | first IC | Kilby의 Ge IC, Noyce의 Si IC |
| 1968 | Fairchild uA741 | op-amp IC |
| 1970-1971 | Intel memory/CPU | DRAM과 microprocessor |

## CMOS Scaling과 현대 반도체

강의는 Moore's Law와 CMOS scaling을 통해 반도체 산업이 얼마나 커졌는지 설명한다.

예시:

- 3nm CMOS
- Apple A17 Bionic: 16 billion transistors
- Nvidia A100: 52.2 billion transistors
- 대규모 AI data center와 GPU 사용

## 왜 전자회로를 공부하는가

핵심 이유:

- 반도체 산업은 한국 경제와 전기전자공학에서 매우 중요하다.
- 전자회로는 모든 하드웨어의 기본 building block이다.
- 회로 설계는 engineering design 감각을 훈련하기 좋다.
- analog/digital/semiconductor 분야 모두 MOS 회로 이해가 필요하다.

## 과목 목표

1. MOS basic electronic circuits 복습
2. MOS amplifier, filter, feedback, oscillator 학습
3. MOS electronic circuit design practice

교재는 Razavi의 `Fundamentals of Microelectronics, 2nd Edition`을 사용한다.

## 강의 주제 구성

| 파트 | 강의 |
|---|---|
| Introduction | Lect. 1 |
| Electronic Circuits I review | Lect. 2-9 |
| Frequency responses of MOS circuits | Lect. 10-15 |
| Filters | Lect. 16-22 |
| Feedback | Lect. 23-26 |
| Oscillators and PLL | Lect. 27-30 |

## 평가 방식

| 항목 | 점수 |
|---|---:|
| Attendance and participation | 25 |
| Tests | 50 |
| Design Project | 25 |

시험:

- Test #1: 4월 23일
- Test #2: 6월 18일

출석/과제 규칙:

- Tuesday lecture 결석: -1
- 지각: -0.5
- Thursday lecture 과제 미제출: -2
- 과제 부실: -1
- copying 의심: 관련자 모두 -10

## 같이 보면 좋은 노트

- [MOS Large-Signal Characteristics - MOS 대신호 특성](02-mos-large-signal-characteristics.md)
- [MOS Small-Signal Characteristics - MOS 소신호 모델](03-mos-small-signal-characteristics.md)
- [Project Design Guide - TIA CTLE 설계 가이드](23-project-design-guide-tia-ctle.md)

## 보강 학습 노트

### 큰 그림

- 이 문서는 **01. Introduction - 전자회로 II 개요**를 다루며, MOS 증폭기, 주파수 응답, 차동 회로, feedback, filter, oscillator를 설계 관점으로 연결한다.
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
- **01. Introduction - 전자회로 II 개요**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [00. Design Project Summary - Optical Receiver PD TIA CTLE](00-design-project-summary-optical-receiver-pd-tia-ctle.md) · 다음: [02. MOS Large-Signal Characteristics - MOS 대신호 특성](02-mos-large-signal-characteristics.md)
