---
layout: page
title: "01. Introduction — 전자회로 II 개요"
permalink: /studies/circuits/electric-circuits-2/01-introduction-ii/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Electric_Circuits%202/lecture_notes) · 교재: Razavi, *Fundamentals of Microelectronics*, 2nd Edition

{% raw %}
## 개요

전자회로 II는 MOS 전자회로를 복습하고 **증폭기 → 주파수 응답 → 필터 → 피드백 → 발진기 → 설계 프로젝트**로 확장한다. 전자공학의 목적은 전자/전하 운반자의 흐름을 제어해 **amplification, filtering, logic, memory, computing** 같은 유용한 기능을 수행하는 것.

---

## 1. Electronics란

고유한 **V–I 특성**을 가진 소자로 전자의 흐름을 제어하는 과학·기술.

| 기본 소자 | 회로 기능 |
|---|---|
| resistor, capacitor, inductor | amplification |
| diode | filtering |
| transistor | logic / memory / computing |

## 2. 전자공학 역사

| 시기 | 사건 | 의미 |
|---|---|---|
| 1904 | Fleming tube | 최초의 전자 소자 (diode, rectification) |
| 1906 | Audion triode | vacuum tube amplifier 시작 |
| 1946 | ENIAC | vacuum tube computer |
| 1947 | transistor 발명 | Bell Labs (Bardeen / Shockley / Brattain) |
| 1958–1959 | first IC | Kilby(Ge), Noyce(Si) |
| 1968 | Fairchild μA741 | op-amp IC |
| 1970–1971 | Intel DRAM / CPU | memory, microprocessor |

## 3. CMOS Scaling과 현대 반도체

Moore's Law에 따라 산업이 폭발적으로 성장.
- 3 nm CMOS 공정
- Apple A17 Bionic: 16 billion transistors
- Nvidia A100: 52.2 billion transistors
- 대규모 AI datacenter·GPU

## 4. 왜 전자회로를 공부하는가

- 반도체는 한국 경제·전기전자공학의 핵심
- 전자회로 = 모든 하드웨어의 building block
- engineering design 감각 훈련
- analog / digital / semiconductor 전 분야가 MOS 회로 이해를 요구

## 5. 과목 목표

1. MOS basic electronic circuits 복습
2. MOS **amplifier / filter / feedback / oscillator** 학습
3. MOS electronic circuit **design practice** (설계 프로젝트)

---

## 6. 강의 주제 구성

| 파트 | 강의 | 대응 노트 |
|---|---|---|
| Introduction | Lect. 1 | 01 |
| Electronic Circuits I review | Lect. 2–9 | 02–08 |
| Frequency responses of MOS circuits | Lect. 10–15 | 09–12 |
| Filters | Lect. 16–22 | 13, 17 |
| Feedback | Lect. 23–26 | 14 |
| Oscillators and PLL | Lect. 27–30 | 15 |
| Design Project | — | 16 |

## 7. 평가

| 항목 | 점수 |
|---|---:|
| Attendance & participation | 25 |
| Tests | 50 |
| Design Project | 25 |

- Test #1: 4월 23일 · Test #2: 6월 18일
- Tuesday 결석 −1 / 지각 −0.5 / Thursday 과제 미제출 −2 / 과제 부실 −1 / copying 의심 관련자 전원 −10

---

## 핵심 정리

- 전자회로 II = MOS 증폭기·주파수 응답·필터·피드백·발진기 + 설계 프로젝트.
- 교재는 Razavi, *Fundamentals of Microelectronics* 2nd ed.
- 전자공학의 본질 = V–I 특성을 가진 소자로 전자 흐름을 제어해 유용한 기능(증폭·필터·논리·기억·연산)을 수행.

## 복습 질문

- 전자공학 역사에서 diode(1904) → triode(1906) → transistor(1947) → IC(1958) → op-amp/DRAM/CPU 흐름을 설명할 수 있나?
- 이 과목의 5개 주제 블록(review / frequency response / filters / feedback / oscillators)은?
{% endraw %}

---

다음: [02. MOS 소자 특성 (대신호·소신호)](02-mos-large-signal-characteristics.md)
