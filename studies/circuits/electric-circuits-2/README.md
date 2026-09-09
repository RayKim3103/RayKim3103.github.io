---
layout: page
title: Electric Circuits 2
description: >
  전자회로 2 강의 노트. MOS 증폭기(CS·SF·CG·cascode·차동)부터 주파수 응답, 필터, 음귀환, 발진기, 그리고 광수신기 TIA/CTLE 설계 프로젝트까지. 교재는 Razavi, Fundamentals of Microelectronics 2nd ed.
hide_description: false
sitemap: false
permalink: /studies/circuits/electric-circuits-2/
---

원본: [GitHub — Electric Circuits 2](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Electric_Circuits%202)

> 원본 28개 노트를 16개로 통합했습니다. "revised" 중복 PDF는 없었지만, **한 주제를 여러 파일로 나눈 강의 세트**(차동 증폭기 1·2, CS/Degeneration/Cascode 주파수 응답, 필터 5개, 음귀환+I/O 저항 1·2, Ring/LC 발진기, 프로젝트 가이드+결과)를 교재의 syllabus 블록 단위로 하나씩 합치고 보강했습니다. **설계 프로젝트의 수치·회로 파라미터는 원문 그대로**입니다.

## 강의 노트

1. [Introduction — 전자회로 II 개요](01-introduction-ii.md)
2. [MOS 소자 특성 — 대신호·소신호](02-mos-device-characteristics.md) — 원본 `02`+`03`
3. [Common-Source 증폭기 (CS)](03-common-source-amplifier-cs.md)
4. [Source Follower & Common-Gate (SF·CG)](04-source-follower-and-common-gate-sf-cg.md)
5. [Cascode 증폭기](05-cascode-amplifier.md)
6. [Bias 회로와 Current Mirror](06-bias-circuits-and-current-mirrors.md)
7. [Differential Amplifiers — DM/CM · CMRR · Active Load](07-differential-amplifiers.md) — 원본 `08`+`09`
8. [Pole·Zero & Bode Plot](08-pole-zero-bode-plot.md)
9. [MOSFET 고주파 모델 (Capacitance · fT)](09-mosfet-high-frequency-model.md)
10. [증폭기 주파수 응답 — CS·Degeneration·CG·Cascode·SF·Differential](10-amplifier-frequency-response.md) — 원본 `12`+`13`+`14`
11. [OTA & Op-Amp](11-ota-and-op-amp.md)
12. [필터 — 1차·2차 수동·인덕터 시뮬레이터·Biquad·고차 Butterworth](12-filters.md) — 원본 `16`~`20`
13. [음귀환 (Feedback) — 기초·I/O 저항 개선](13-feedback.md) — 원본 `21`+`24`+`25`
14. [발진기 — Ring·LC](14-oscillators.md) — 원본 `26`+`27`
15. [설계 프로젝트 — Optical Receiver (PD·TIA·CTLE)](15-design-project-tia-ctle.md) — 원본 `23`+`00`
16. [LTspice 튜토리얼](16-ltspice-tutorial.md)
