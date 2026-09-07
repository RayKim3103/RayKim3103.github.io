---
layout: page
title: "10. SSD Software 2 - Superblock, LAST, FTL Functions"
permalink: /studies/arch/microprocessor/10-ssd-software-2-superblock-last-and-ftl-functions/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/10%20SSD%20Software%202%20-%20Superblock%20LAST%20and%20FTL%20Functions.md)

{% raw %}
tags: #micro-processor #ssd #ftl #superblock #last #bad-block-management #wear-leveling

관련 노트: [SSD Software 1 - FTL Overview, BAST, FAST](09-ssd-software-1-ftl-overview-bast-fast.md), [SSD Software 2 - Superblock LAST Annotated Image Copy](10a-ssd-software-2-superblock-last-annotated-image-copy.md)

## 핵심 요약

이 자료는 block-mapped FTL의 대표 방식 중 Superblock FTL과 LAST를 설명하고, bad block management와 wear-leveling을 자세히 다룬다. Superblock FTL은 인접 logical block을 묶어 merge 비용을 줄이고, LAST는 access locality를 이용해 hot/cold data를 나누어 full merge를 줄인다.

## Superblock FTL 개요

Superblock은 인접한 여러 logical block을 하나의 큰 group으로 묶은 것이다.

도입 이유:

- BAST는 large block NAND 특성 때문에 full merge가 많아질 수 있다.
- FAST는 single log block을 여러 data block이 공유하면서 full merge overhead가 생길 수 있다.
- Superblock은 block-level mapping table과 비슷한 크기의 mapping table을 유지하면서 더 유연한 page 배치를 제공한다.

## D-Block과 U-Block

Superblock FTL은 D-block과 U-block을 사용한다.

| 블록 | 의미 |
|---|---|
| D-block | data block 역할 |
| U-block | update/log block 역할 |

Update data는 U-block에 기록되고, mapping table이 logical page의 실제 위치를 추적한다.

## Three-Level Mapping

Superblock FTL은 3-level mapping table을 사용한다.

개념적으로:

1. Logical superblock index를 찾는다.
2. Superblock 내부 logical block/page offset을 계산한다.
3. PBMT 같은 physical block mapping table로 실제 physical block을 찾는다.

이 구조는 mapping table 크기를 block-level 방식에 가깝게 유지하면서 page 배치 유연성을 높인다.

## PBMT

PBMT(Physical Block Mapping Table)는 physical block mapping 정보를 담는다. NAND flash는 느리므로 mapping information을 매번 flash에서 읽으면 성능이 떨어진다. 따라서 controller는 mapping table cache와 SRAM/DRAM 사용을 함께 고려한다.

## Superblock Garbage Collection

Superblock FTL의 GC는 U-block과 D-block을 고려한다.

대표 흐름:

1. Free block이 부족하면 victim superblock을 찾는다.
2. Invalid page가 많거나 merge cost가 낮은 superblock을 선택한다.
3. U-block과 D-block의 valid page를 정리한다.
4. Partial merge 또는 full merge를 수행한다.

자료는 Superblock이 BAST/FAST 대비 약 30-32% 성능 향상을 보이며, switch merge 비중이 증가한다고 설명한다.

## Parameter Sensitivity

U-block 수가 많을수록 update를 흡수할 여지가 늘어 성능이 좋아질 수 있다. 하지만 DRAM/cache hit rate가 높거나 workload locality가 강하면 parameter 변화의 영향이 줄어들 수 있다.

## LAST 개요

LAST는 Locality-Aware Sector Translation이다. Access pattern의 locality를 관찰해 hot data와 cold data를 구분하고, 각각 다른 log block partition에서 관리한다.

## Hot Data와 Cold Data

| 분류 | 의미 |
|---|---|
| Hot data | 자주 update되는 data |
| Cold data | 드물게 update되는 data |

Hot data가 cold data와 섞이면 cold valid page까지 merge에 끌려 들어가 full merge 비용이 커진다. LAST는 hot/cold를 분리해 dead block을 더 많이 만들고 full merge를 줄인다.

## LAST Scheme

LAST는 random log block을 hot partition과 cold partition으로 나눈다.

핵심 구성:

- locality detector
- hot/cold classifier
- hot log blocks
- cold log blocks
- adaptive partition control

Locality threshold와 partition size는 workload에 따라 조정된다.

## LAST Garbage Collection

LAST의 GC는 두 단계로 볼 수 있다.

1. Candidate partition이나 block group을 정한다.
2. Victim block을 선택하고 merge를 수행한다.

Hot partition에서는 invalid page 비율이 높아 full merge 비용이 줄어들 가능성이 크다.

## Adaptiveness

LAST는 hot/cold partition size와 locality threshold를 workload에 맞게 조정한다. Hot data가 많으면 hot partition을 늘리고, cold data가 많으면 cold partition을 늘리는 식이다.

## Bad Block Management

NAND flash에는 bad block이 존재하므로 FTL은 bad block을 free block pool에서 제외하거나 replacement block으로 교체해야 한다.

기본 처리:

1. 특정 page에서 error가 발생한다.
2. 해당 block의 정상 page를 buffer 또는 다른 block으로 복사한다.
3. bad block을 더 이상 사용하지 않도록 표시한다.
4. mapping table을 갱신한다.

Bad block management는 hardware와 software가 함께 담당할 수 있지만, FTL은 free block allocation을 관리하므로 bad block 회피에 중요한 역할을 한다.

## Wear-Leveling

Wear-leveling은 block erase count를 고르게 만들어 flash 수명을 늘리는 기법이다.

### Dynamic Wear-Leveling

자주 write되는 data를 erase count가 낮은 block에 배치해 hot block 마모를 완화한다.

### Static Wear-Leveling

거의 변하지 않는 cold data가 낮은 erase count block을 오래 점유하면 다른 block만 계속 마모된다. Static wear-leveling은 cold data도 가끔 이동시켜 전체 erase count를 균등하게 만든다.

## 정리

FTL의 주요 기능:

- address translation
- garbage collection
- bad block management
- wear-leveling

자료의 결론은 BAST, FAST, Superblock, LAST 중 LAST가 locality를 활용해 가장 좋은 성능을 보인다는 것이다.

## 시험ㆍ복습 체크포인트

- Superblock이 BAST/FAST의 full merge 문제를 어떻게 줄이는지 설명할 수 있어야 한다.
- PBMT와 3-level mapping의 역할을 이해해야 한다.
- LAST에서 hot/cold data를 나누는 이유를 말할 수 있어야 한다.
- Bad block management 절차를 순서대로 설명할 수 있어야 한다.
- Dynamic wear-leveling과 static wear-leveling을 구분할 수 있어야 한다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. SSD Software 2 - Superblock, LAST, FTL Functions**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 저장장치 주제에서는 logical 주소와 physical 위치가 언제 어떻게 mapping되는지 추적한다.
- FTL/파일시스템 계층은 성능뿐 아니라 crash consistency와 metadata 복구 경로가 중요하다.
- processor 관점에서는 register, instruction encoding, addressing mode, exception 흐름을 함께 보아야 한다.
- SSD 관점에서는 NAND flash의 물리 제약이 FTL, mapping table, wear leveling, garbage collection 설계를 만든다.
- hardware와 software 경계에서 latency hiding, buffering, metadata consistency가 핵심 설계 문제가 된다.

### 문제 풀이 또는 구현 루틴

- assembly를 읽을 때는 각 instruction이 register와 memory 중 무엇을 바꾸는지 한 줄씩 주석으로 적는다.
- I/O 구조는 request path, queue, interrupt, DMA, completion 순서로 추적한다.
- FTL 함수는 logical page, physical page, block, superblock metadata가 언제 갱신되는지 확인한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- NAND flash는 overwrite가 불가능하므로 HDD처럼 제자리 갱신한다고 생각하면 안 된다.
- 성능 문제에서 CPU 계산보다 I/O latency와 queueing이 병목일 수 있다.
- metadata update 순서를 잘못 잡으면 전원 장애 상황에서 mapping consistency가 깨진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 동작은 processor core 내부 문제인가, memory/I/O subsystem 문제인가?
- 상태가 바뀌는 metadata는 어디에 있고 crash 후 어떻게 복구되는가?
- latency를 줄이는 방법과 throughput을 높이는 방법이 어떻게 다른가?
- **10. SSD Software 2 - Superblock, LAST, FTL Functions**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [09. SSD Software 1 - FTL Overview, BAST, FAST](09-ssd-software-1-ftl-overview-bast-fast.md) · 다음: [10. SSD Software 2 - Superblock LAST Annotated Image Copy](10a-ssd-software-2-superblock-last-annotated-image-copy.md)
