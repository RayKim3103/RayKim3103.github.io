---
layout: page
title: "06. Flash Translation Layer — BAST · FAST · Superblock · LAST"
permalink: /studies/arch/microprocessor/06-flash-translation-layer/
sitemap: false
---

- **원본**: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor) · `09 SSD Software 1 — FTL Overview, BAST, FAST` + `10 SSD Software 2 — Superblock, LAST and FTL Functions` + `10A Annotated Image Copy` **통합·보강**
- `10A`는 강의 노트에 "53쪽 같은 강의의 image/annotated copy"라고 명시된 **중복본**이라 하나로 합쳤습니다.

{% raw %}
## 개요

**FTL(Flash Translation Layer)**은 host가 보는 logical block interface를 NAND flash의 physical page/block operation으로 변환하는 SSD firmware 계층이다. 이 장은 FTL이 필요한 이유, address translation(page-mapped vs block-mapped), garbage collection과 merge, 그리고 block-mapped FTL의 진화 — **BAST → FAST → Superblock → LAST** — 를 비교하고, bad block management와 wear-leveling을 다룬다.

---

## 1. FTL의 역할과 필요성

**FTL은 host의 LBA(Logical Block Address)를 NAND의 physical address로 변환**한다. 필요한 이유:

- NAND는 **erase-before-write** — 제자리 덮어쓰기 불가
- **program은 page 단위, erase는 block 단위** (비대칭)
- **bad block**이 존재 (factory + runtime)
- cell **endurance 제한** → wear-leveling 필요
- legacy file system은 HDD식 **block device interface**(제자리 overwrite 가능)를 기대

## 2. FTL과 File System

Legacy FS는 logical sector를 그냥 overwrite한다고 가정. FTL은 실제로는 **새 physical page에 out-of-place update**를 하고 **mapping table**을 갱신한다.

```text
File system LBA  ──(FTL mapping)──▶  physical flash page/block
```

---

## 3. Address Translation

### Page-Mapped FTL

page 단위 mapping.

| 장점 | 단점 |
|---|---|
| update 유연성 높음 | **mapping table이 매우 큼** |
| random write에 강함 | |
| GC 비용 절감 가능 | |

예: 64 GB SSD, 4 KB page, entry 4 B → **table ≈ 64 MB** → 전부 SRAM에 못 올림 → DMA(DRAM) cache + 일부만 상주(DFTL 류).

### Block-Mapped FTL

block 단위 mapping. LBA를 (block-level index, page offset)로 분해.

| 장점 | 단점 |
|---|---|
| **mapping table 작음** | page-level update 유연성 낮음 |
| SRAM/DRAM 요구 ↓ | random write 시 **merge 비용 큼** |

강의는 block-mapped FTL을 중심으로 진화 과정을 다룬다.

---

## 4. Garbage Collection

out-of-place update 반복 → invalid page 축적 → free block 부족.
**GC**: victim block 선택 → valid page를 다른 곳으로 복사 → block erase → free block 확보.

GC 비용(valid page copy + erase 횟수)이 FTL 성능을 크게 좌우 → **write amplification**의 주범.

## 5. Merge Operation

block-mapped FTL에서 **data block**과 **log block**을 합치는 연산.

| Merge | 비용 | 설명 |
|---|---|---|
| **Switch merge** | 가장 쌈 | log block이 순서대로 꽉 채워졌으면 그대로 새 data block으로 전환, 옛 data block만 erase |
| **Partial merge** | 중간 | log block의 빈 부분에 data block의 남은 valid page만 복사 후 전환 |
| **Full merge** | 가장 비쌈 | data block + log block 양쪽에서 valid page를 모아 새 block 생성, 양쪽 erase |

Full merge가 많아지면 성능 급락.

---

## 6. BAST — Block-level Associative Sector Translation

- block-mapped FTL
- 각 data block에 **log block을 associatively 연결** (1:1 경향)
- incoming LBA의 **LSB → page offset**, **MSB → block-level mapping table index**
- switch merge / full merge 지원

**문제**: random update가 여러 data block에 흩어지면 → 각 data block이 log block을 요구 → **log block 부족** → 자주 log block을 비우려 **full merge 빈발** → **thrashing**. log block 활용률(utilization)이 낮다.

## 7. FAST — Fully Associative Sector Translation

BAST의 log block 활용률 문제 완화가 목표. log block을 **모든 data block이 공유**(fully associative)하고, 두 종류로 나눔:

- **Sequential log block**: sequential write pattern이 조건을 만족하면 **switch merge** 가능, 아니면 partial merge.
- **Random log block**: random update를 fully associative하게 수용. 단, merge 시 **여러 data block과 얽힌 page**를 처리해야 함.

**문제**: random log block 하나를 merge할 때 그것과 연관된 **여러 data block 각각에 full merge**가 필요. 강의 예시 — **1 page update 때문에 3번의 full merge**가 연쇄로 발생 → FAST의 큰 overhead.

### BAST vs FAST

| 항목 | BAST | FAST |
|---|---|---|
| log block 연결 | data block과 associative (1:1 경향) | fully associative (공유) |
| 장점 | 구조 단순 | log block 활용률 개선 |
| 약점 | thrashing, full merge 빈번 | random log merge 비용 큼(연쇄 full merge) |
| merge | switch / full 중심 | sequential/random log에 따라 다양 |

## 8. Superblock FTL

**Superblock** = 인접한 여러 logical block을 하나의 큰 group으로 묶음.

도입 이유:
- BAST는 large-block NAND에서 full merge 과다
- FAST는 single log block 공유로 full merge overhead
- Superblock은 **block-level 수준의 작은 mapping table을 유지하면서 page 배치 유연성 확보**

**D-block / U-block**:

| block | 역할 |
|---|---|
| **D-block** | data block |
| **U-block** | update/log block |

**Three-level mapping**:
1. logical **superblock index** 찾기
2. superblock 내부 logical block/page offset 계산
3. **PBMT(Physical Block Mapping Table)**로 실제 physical block 찾기

**PBMT**: physical block mapping 정보. NAND는 느리므로 매번 flash에서 읽으면 성능 저하 → mapping table cache + SRAM/DRAM 병용.

**Superblock GC**: free block 부족 → victim superblock(invalid page 많거나 merge cost 낮은 것) 선택 → U-block/D-block valid page 정리 → partial 또는 full merge.

**결과**: BAST/FAST 대비 **약 30–32% 성능 향상**, **switch merge 비중 증가**.
**Parameter sensitivity**: U-block 수가 많을수록 update 흡수 여지 ↑ → 성능 ↑. 단 DRAM/cache hit rate가 높거나 workload locality가 강하면 parameter 영향이 줄어듦.

## 9. LAST — Locality-Aware Sector Translation

access pattern의 **locality**를 관찰해 **hot data / cold data**를 구분, 각각 다른 log block partition에서 관리.

**왜?** hot data가 cold data와 섞이면 GC 시 **cold valid page까지 merge에 끌려 들어가** full merge 비용 폭증.
```text
hot/cold 분리 → valid page copy 감소 → full merge cost 감소
```

**LAST scheme** — random log block을 **hot partition / cold partition**으로 분리. 구성:
- **locality detector**
- **hot/cold classifier**
- hot log blocks / cold log blocks
- **adaptive partition control** (locality threshold·partition size를 workload에 맞게 조정)

**LAST GC** (2단계):
1. candidate partition / block group 결정
2. victim block 선택 → merge 수행

hot partition은 invalid page 비율이 높아 full merge 비용이 낮을 가능성이 큼.

**Adaptiveness**: hot data가 많으면 hot partition 확대, cold data가 많으면 cold partition 확대.

**강의 결론**: BAST < FAST < Superblock < **LAST** — LAST가 locality를 활용해 가장 좋은 성능.

---

## 10. Bad Block Management (FTL 관점)

FTL은 어떤 block이 bad인지 파악하고 mapping 대상에서 제외 / replacement block으로 교체. **Factory + runtime** 둘 다 관리.

처리 흐름:
1. 특정 page에서 error(program fail / erase fail / uncorrectable ECC) 발생
2. 해당 block의 정상 page를 buffer 또는 다른 block으로 복사
3. bad block으로 표시
4. mapping table 갱신

HW/SW 공동 담당이지만, **FTL이 free block allocation을 관리**하므로 bad block 회피에 핵심 역할.

## 11. Wear-Leveling

block erase count를 고르게 만들어 flash 수명 연장.

| 종류 | 동작 |
|---|---|
| **Dynamic** | 자주 write되는 data를 **erase count 낮은 block**에 배치 → hot block 편중 마모 완화 |
| **Static** | 거의 안 변하는 **cold data**가 낮은 erase count block을 오래 점유하면 다른 block만 계속 마모 → cold data도 **가끔 이동**시켜 전체 erase count 균등화 |

## 12. FTL 주요 기능 정리

- **address translation** (LBA → physical)
- **garbage collection** (invalid page 회수)
- **bad block management** (factory + runtime)
- **wear-leveling** (dynamic + static)

---

## 복습 질문

- FTL이 필요한 5가지 이유는? page-mapped와 block-mapped FTL의 table 크기·random write 특성 차이는?
- Switch / partial / full merge를 비용 순으로 놓고 각각 언제 가능한가?
- BAST의 thrashing 원인과, FAST에서 "1 page update → 3 full merge"가 나오는 이유는?
- Superblock FTL의 D-block/U-block, three-level mapping, PBMT의 역할은? BAST/FAST 대비 성능 향상 수치는?
- LAST가 hot/cold를 분리하는 이유(full merge 비용 관점)와 구성 요소 4가지는?
- Dynamic wear-leveling만으로 부족해서 static wear-leveling이 필요한 상황은?
{% endraw %}

---

이전: [05. NAND Flash Memory](05-nand-flash-memory.md)
