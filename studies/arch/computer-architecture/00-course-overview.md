---
layout: page
title: "00. 강의 개요"
permalink: /studies/arch/computer-architecture/00-course-overview/
sitemap: false
---

- **원본**: [GitHub — Computer Architecture](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Computer_Architecture) · 강의 노트 `00` 정리
- 교재: Patterson & Hennessy, *Computer Organization and Design* (RISC-V edition). 강의 슬라이드와 대조해 사용하세요.

{% raw %}
## 개요

**EEE3530 Computer Architecture**. software와 hardware 사이의 **interface(ISA)**부터 datapath·pipeline·cache까지 다룬다. 회로만 배우는 과목이 아니라 "C 코드 → instruction → hardware 동작 → 성능"의 사슬을 잇는다.

## 전체 지도 (P&H ch 1–5)

| # | 주제 | 핵심 | 과제 |
|---|---|---|---|
| 1 | Computer Abstractions & Performance | abstraction layer, CPU time 식, power/energy, Amdahl | — |
| 2 | Instructions (RISC-V ISA) | register, load-store, instruction format, branch, function call | A1 RISC-V Assembly, A2 Functions(Hanoi) |
| 3 | Computer Arithmetic | 2's complement, adder(RCA/CLA), ALU, IEEE 754 | A3 Floating-Point add/sub |
| 4 | The Processor | single-cycle datapath, 5-stage pipeline, hazard, forwarding, branch prediction | A4 Branch Predictor + BTB |
| 5 | Memory Hierarchy | locality, cache mapping, write policy, AMAT, 3C miss | A5 Victim Cache |

## 실습 환경

과제는 **Kite** RISC-V architecture simulator(5-stage pipeline 모델)와 **RISC-V assembly**를 쓴다. Linux/Ubuntu, shell, `make` 빌드 흐름을 초반에 익혀둘 것. 환경 설정이 늦으면 assembly·function call·pipeline 과제가 밀린다.

## 학습 관점

- 뒤쪽 강의는 전부 **CPU time = IC × CPI × Clock cycle time** 의 어느 항을 줄이느냐로 연결된다 (pipeline·cache는 CPI의 stall 항).
- ISA는 "software가 hardware를 쓰는 약속"이며 assembly instruction이 그 구체적 표현.
- Computer Architecture는 software/hardware **interface**를 함께 다룬다.

## 복습 질문

- 이 과목이 다루는 abstraction layer의 범위(application ↔ transistor)는?
- 5개 챕터가 성능식(IC·CPI·clock)의 어느 부분과 연결되는가?
- Kite simulator와 RISC-V assembly가 과제에서 하는 역할은?
{% endraw %}

---

다음: [01. Computer Abstractions & Performance](01-computer-abstractions-and-performance.md)
