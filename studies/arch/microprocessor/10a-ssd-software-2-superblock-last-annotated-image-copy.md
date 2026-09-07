---
layout: page
title: "10. SSD Software 2 - Superblock LAST Annotated Image Copy"
permalink: /studies/arch/microprocessor/10a-ssd-software-2-superblock-last-annotated-image-copy/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Micro_Processor/lecture_notes/10A%20SSD%20Software%202%20-%20Superblock%20LAST%20Annotated%20Image%20Copy.md)

{% raw %}
tags: #micro-processor #ssd #ftl #superblock #last #annotated-copy

관련 노트: [SSD Software 2 - Superblock, LAST, FTL Functions](10-ssd-software-2-superblock-last-and-ftl-functions.md)

## 핵심 요약

이 파일은 `SSD Software Details 2`의 image/annotated copy이다. 텍스트 추출은 제한적이지만, 렌더링 확인 결과 Superblock FTL과 LAST, bad block management, wear-leveling을 다루는 같은 강의자료 계열이다. 필기 흔적이 있는 페이지에서는 LAST의 locality-aware sector translation, hot/cold partition, full merge 감소가 강조되어 있다.

## 자료 성격

이 사본은 텍스트 기반 PDF인 [SSD Software 2 - Superblock, LAST, FTL Functions](10-ssd-software-2-superblock-last-and-ftl-functions.md)와 같은 53쪽 강의의 이미지 기반 버전으로 보인다. 따라서 개념 흐름은 10번 노트와 동일하게 잡고, 필기/주석이 있는 복습용 사본으로 활용하면 좋다.

## Superblock FTL 복습

Superblock FTL은 인접한 logical block을 하나의 superblock으로 묶어 update block 사용을 더 유연하게 만든다.

핵심:

- D-block과 U-block 사용
- three-level mapping
- PBMT 활용
- partial/switch merge 비중 증가
- BAST/FAST보다 full merge overhead 감소

## LAST 복습

LAST는 Locality-Aware Sector Translation이다. 접근 locality를 관찰해 random log block을 hot/cold partition으로 나눈다.

필기 확인상 강조된 내용:

- locality를 이용한 sector translation
- hot data와 cold data 분리
- hot partition은 dead block을 더 많이 만들어 full merge 감소
- cold data가 hot update와 섞이면 merge cost가 커짐

## Hot/Cold 분리의 의미

Hot data는 자주 갱신되어 invalid page가 빨리 생긴다. Cold data는 오래 valid 상태로 남는다. 둘이 같은 log block에 섞이면 garbage collection 시 cold valid page를 계속 복사해야 한다.

```text
hot/cold 분리 -> valid page copy 감소 -> full merge cost 감소
```

## Bad Block Management 복습

Bad block은 초기 제조 시점부터 있거나 사용 중 발생할 수 있다. FTL은 bad block을 사용하지 않도록 mapping table과 free block pool을 관리한다.

처리 흐름:

1. Error 발생 block 감지
2. 정상 page를 다른 block으로 이동
3. Bad block 표시
4. Mapping 갱신

## Wear-Leveling 복습

Wear-leveling은 모든 block의 erase count를 균등하게 만드는 작업이다.

- Dynamic wear-leveling: update되는 data 중심으로 마모 분산
- Static wear-leveling: 오래 움직이지 않는 cold data도 이동해 전체 block을 고르게 사용

## 이 사본으로 공부할 때 볼 것

- 주석이 있는 page에서 교수자가 강조한 단어와 그림을 먼저 본다.
- 텍스트 검색은 제한적이므로 개념 검색은 10번 노트를 사용한다.
- Superblock과 LAST의 공통 목표가 merge 비용 감소라는 점을 중심으로 연결한다.
- LAST의 차별점은 locality detection과 hot/cold partition이다.

## 시험ㆍ복습 체크포인트

- LAST의 약자를 풀어 쓰고 핵심 아이디어를 설명할 수 있어야 한다.
- Hot/cold partition이 full merge를 줄이는 이유를 말할 수 있어야 한다.
- Superblock FTL과 LAST의 차이를 비교할 수 있어야 한다.
- 이 사본은 필기/이미지 확인용이고, 상세 텍스트 정리는 10번 노트와 함께 보는 것이 좋다.

## 보강 학습 노트

### 큰 그림

- 이 문서는 **10. SSD Software 2 - Superblock LAST Annotated Image Copy**를 다루며, ARM 프로세서와 저장장치 구조를 통해 명령어 실행, 메모리, I/O, SSD 펌웨어가 맞물리는 방식을 익힌다.
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
- **10. SSD Software 2 - Superblock LAST Annotated Image Copy**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [10. SSD Software 2 - Superblock, LAST, FTL Functions](10-ssd-software-2-superblock-last-and-ftl-functions.md)
