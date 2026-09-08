---
layout: page
title: "05. NAND Flash Memory — 셀 구조부터 3D NAND까지"
permalink: /studies/arch/microprocessor/05-nand-flash-memory/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · `08 SSD Hardware 4 — NAND Flash Memory` 보강

{% raw %}
## 개요

SSD의 실제 저장 매체인 NAND flash memory. NOR vs NAND, floating-gate / charge-trap cell과 threshold voltage, program(ISPP) / read / erase 동작, SLC~QLC, memory organization과 addressing, command set, interleaved operation, bad block, reliability issue, ECC, 3D NAND.

---

## 1. NOR Flash vs NAND Flash

| 항목 | NOR Flash | NAND Flash |
|---|---|---|
| cell 연결 | 각 cell이 bit line에 병렬 → **random direct access** | 여러 cell이 **series string** → page/block 단위 access |
| 읽기 | 빠른 random read, **execute-in-place(XIP)** 가능 | page(sequential) read에 적합 |
| 쓰기/삭제 | 상대적으로 느림 | write/erase 밀도·성능 유리 |
| 밀도/비용 | 낮은 밀도, 높은 비용 | **높은 밀도, 낮은 bit cost** |
| 용도 | code storage, firmware(XIP) | **mass storage, SSD** |

→ NAND가 write/erase 효율과 집적도 덕분에 SSD의 주류.

## 2. 표준 — ONFI

NAND interface와 command set은 **ONFI(Open NAND Flash Interface)** 표준화 흐름과 vendor-specific interface가 함께 발전. SSD controller는 여러 NAND chip의 timing·command protocol을 맞춰 제어.

---

## 3. Unit Cell — Floating Gate / Charge Trap

cell은 **floating gate**(또는 **charge trap layer**)에 전하를 가두어 transistor의 **threshold voltage(Vth)**를 바꾼다.

```text
저장 전하 많음  → Vth 높음
저장 전하 적음  → Vth 낮음
```

- **Read**: control gate에 특정 read voltage 인가 → cell이 켜지는지(전류가 흐르는지) sensing → 저장된 bit 판별.
- 비휘발성: 전원이 없어도 floating gate의 전하가 유지됨(수년 retention).

## 4. Cell Array 구조

word line(WL)과 bit line(BL)로 배열. NAND는 여러 cell이 **series string**으로 연결되고, string 양끝을 **select transistor**(SSL / GSL)가 제어.

**구조 단위 (작은 것 → 큰 것)**:

| 단위 | 의미 |
|---|---|
| **cell** | 1~4 bit 저장 |
| **page** | read/program의 최소 단위 (예: 4~16 KB + spare) |
| **block** | **erase의 최소 단위** (예: 수백~수천 page) |
| **plane** | 병렬 동작 가능한 block 묶음 |
| **die (chip)** | 여러 plane |
| **package** | 여러 die 적층 |

## 5. Program Operation — ISPP

Program = floating gate에 전하 주입 → Vth 상승. **page 단위**.

**ISPP (Incremental Step Pulse Program)**:
1. 작은 program pulse 인가
2. **verify read**로 Vth가 목표 범위 도달했는지 확인
3. 부족하면 pulse 전압을 한 step 높여 재인가
4. 목표 도달까지 반복

→ cell의 Vth 분포를 좁게 제어 → reliability·MLC 구현에 필수.

## 6. Read Operation

select cell의 Vth와 **read reference voltage**를 비교해 판별. on-cell current가 클수록 **sensing margin** 커져 안정적.

MLC/TLC/QLC로 갈수록 threshold level 간 간격이 좁아짐 → sensing margin 감소 → **더 강한 ECC 필요**, read 시 여러 reference voltage로 여러 번 sensing.

## 7. Erase Operation — NAND의 핵심 제약

```text
read / program : page 단위
erase          : block 단위   ← 핵심 제약
```

이미 program된 page를 **직접 overwrite 불가** → 새 값은 다른 free page에 쓰고(out-of-place), 옛 page는 invalid로 표시 → invalid가 쌓이면 block 전체를 erase해서 회수. 그래서 **FTL + garbage collection**이 필수 ([06](06-flash-translation-layer.md)).

## 8. SLC / MLC / TLC / QLC

| 종류 | 저장 bit/cell | Vth level 수 | 특징 |
|---|---:|---:|---|
| **SLC** | 1 | 2 | 빠름, endurance 높음(~10⁵ P/E), 비쌈 |
| **MLC** | 2 | 4 | 밀도↑, margin↓, endurance ~10³–10⁴ |
| **TLC** | 3 | 8 | 비용 유리, ECC·controller 부담 큼 |
| **QLC** | 4 | 16 | 최고 밀도, 최저 endurance, 강력한 LDPC 필요 |

## 9. Pin Configuration & Command Set

NAND는 command / address / data를 정해진 bus(I/O[7:0])와 control signal(CLE, ALE, CE#, WE#, RE#, R/B#)로 주고받는다.

대표 operation:

| operation | 용도 |
|---|---|
| **Read ID / Read Status** | 칩 식별 / 동작 완료·pass·fail 확인 |
| **Page Read** | page → page register → 출력 |
| **Page Program** | 입력 → page register → cell |
| **Change Read Column / Change Write Column** | page register 내 column 위치 이동 |
| **Block Erase** | block 단위 소거 |
| **Copyback (Read/Program)** | page를 host 경유 없이 NAND 내부에서 다른 위치로 복사 |
| **Page Cache Program / Read** | page register + cache register 이중 buffer로 pipeline |

## 10. Memory Organization & Addressing

address = **column address** + **row address**.

- **Column address**: page 내부 byte/word 위치
- **Row address**: page → block → plane 위치

Random page programming이 제한되는 경우가 있어(같은 block 안에서는 낮은 page부터 순서대로 program) **program order를 controller가 관리**해야 함.

## 11. Copyback

data를 host로 내보내지 않고 NAND 내부에서 page를 다른 위치로 복사 → garbage collection·wear-leveling 시 data 이동 비용·bus 점유 절감. **Data modification copyback** = 일부 data를 수정하며 복사. (단, ECC를 controller가 다시 검증하지 않으면 error 누적 위험 — 보통 on-die ECC나 controller 재검증과 함께 사용.)

## 12. Interleaved Operation

여러 **plane / die / channel**이 있을 때 interleaving으로 한 unit이 program/erase(느림) 중인 동안 다른 unit에서 read/write 수행.

효과: 내부 parallelism ↑, channel utilization ↑, **latency hiding**. → SSD의 sequential throughput이 HDD를 앞서는 주된 이유.

## 13. Bad Block

- **Factory(initial) bad block**: 제조 시점부터 존재. 출하 시 bad block marking 정보가 spare 영역에 기록되어 있으니 **처음에 이를 읽어 bad block table 구축** (첫 erase 전에!).
- **Runtime(grown) bad block**: 사용 중 P/E wear로 program fail / erase fail / uncorrectable ECC 발생 시 추가.

Controller가 bad block table 관리 + mapping에서 제외.

## 14. Reliability Issues

| 문제 | 원인/설명 |
|---|---|
| **Program disturbance** | program 시 인접 non-target cell의 Vth가 의도치 않게 상승 |
| **Read disturbance** | 반복 read의 pass voltage가 같은 block 다른 page cell Vth를 서서히 변화 |
| **Floating-poly coupling** | 인접 cell floating gate 간 전하 결합 noise |
| **Vth distribution widening** | P/E 반복으로 cell 간 산포 증가 |
| **Endurance degradation** | oxide 손상 누적 → P/E cycle 한계 |
| **Retention loss** | 시간이 지나며 floating gate 전하 누설 → Vth 하강 |

## 15. ECC

NAND는 bit error가 정상적으로 발생 → **ECC 필수**.

| 코드 | 특징 |
|---|---|
| **Hamming** | 단순, 정정 능력 약함 (SLC 초기) |
| **BCH** | 다중 bit 정정, MLC 시대 주류 |
| **LDPC** | soft-decision, 강력, TLC/QLC 필수, 구현 복잡 |

cell당 bit 수 ↑ → threshold margin ↓ → 더 강한 ECC 필요.

## 16. 3D NAND

2D scaling(cell 미세화)의 한계(cell 간 간섭, 전하 수 부족)를 극복하기 위해 **cell을 수직으로 적층**. 높은 density 제공, tera-bit 급 storage로 확장. 주로 charge-trap 방식 + 큰 공정 노드로 오히려 신뢰성 개선.

---

## 복습 질문

- NAND에서 read/program 단위와 erase 단위의 차이, 그리고 이 제약이 FTL을 필요하게 만드는 이유는?
- ISPP의 4단계와, 그것이 필요한 이유(특히 MLC)는?
- SLC~QLC로 갈수록 Vth level 수·endurance·ECC 요구는 어떻게 변하는가?
- Factory bad block을 "첫 erase 전에" 읽어야 하는 이유는?
- Read disturbance와 retention loss는 각각 Vth를 어느 방향으로 움직이는가?
{% endraw %}

---

이전: [04. SSD 기본과 아키텍처](04-ssd-basics-and-architecture.md) · 다음: [06. Flash Translation Layer](06-flash-translation-layer.md)
