---
layout: page
title: Digital Logic Circuits
description: >
  Digital Logic Circuits 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/digital/digital-logic-circuits/
---

원본: [GitHub — Digital Logic Circuits](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Digital_Logic_Circuits)

> 원본 강의 번호(02·03·04·06·07·08, 01·05는 노트 없음)를 그대로 유지하고, 각 장에 정식 수식·전체 진리표·worked example(K-map 직접 풀이, MUX residue map, ROM/PLA 용량 계산, hazard 타이밍 표, flip-flop characteristic/excitation table, FSM state minimization·state assignment 수치 비교, Verilog 코드)을 추가해 표준 교재(Katz *Contemporary Logic Design* · Mano *Digital Design*) 수준으로 보강했습니다.

## 강의 노트

1. [02. 조합논리](02-combinational-logic.md) — Boolean algebra·듀얼·consensus, canonical form, K-map, full adder 진리표·worked 최소화 예제
2. [03. 조합논리 활용](03-combinational-logic-applications.md) — comparator/adder 설계, prime/essential implicant, NAND-NAND 변환, static hazard 타이밍 worked example, Verilog structural·behavioral 코드
3. [04. 조합논리 구현 기술](04-combinational-logic-implementation.md) — ROM/MUX/decoder/PLA/PAL, MUX residue map·ROM 용량·PLA personality matrix worked example
4. [06. 순차논리 설계](06-sequential-logic-design.md) — latch/flip-flop, characteristic·excitation table, setup/hold 수치 예제, metastability
5. [07. 유한상태기계](07-finite-state-machines.md) — FSM 모델, T-FF counter 유도, D-FF 기반 vending machine worked example, Moore/Mealy state 수 비교
6. [08. 유한상태기계 활용](08-fsm-applications.md) — state minimization(7→5 state row matching worked example), state assignment(binary vs one-hot 수치 비교), partitioning
