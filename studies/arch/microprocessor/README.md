---
layout: page
title: Microprocessor
description: >
  Microprocessor 강의 노트. ARM 프로세서 설계·어셈블리부터 컴퓨터 조직(I/O·버스·멀티프로세서), SSD 하드웨어(NAND flash), SSD 소프트웨어(FTL)까지. 인접 챕터와 revised 중복본을 주제 단위로 통합·보강했습니다.
hide_description: false
sitemap: false
permalink: /studies/arch/microprocessor/
---

원본: [GitHub — Micro Processor](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Micro_Processor)

> 원본 12개 노트를 6개로 통합했습니다. ARM Ch1–3은 하나의 ARM 노트로, `04A`(Storage/I/O annotated copy)와 `10A`(Superblock/LAST annotated image copy)는 강의 노트에 "같은 강의의 별도 사본, 본문 동일"이라 명시된 **중복본**이라 원본에 합쳤고, SSD Overview 1+2도 하나로 합쳤습니다. **수치·프로토콜·계산식은 원문을 유지**하고 교재 수준으로 보강했습니다.

## 강의 노트

1. [ARM — 프로세서 설계 · 아키텍처 · 어셈블리](01-arm-architecture-and-assembly.md) — ARM Ch1+Ch2+Ch3 통합
2. [Storage & I/O — 디스크 · 버스 · 인터럽트 · DMA](02-storage-and-io.md) — Comp. Org. Ch6 (+ annotated copy)
3. [Multiprocessors — 병렬성 · 캐시 일관성 · 동기화](03-multiprocessors.md) — Comp. Org. Ch7
4. [SSD — 기본 개념과 내부 아키텍처](04-ssd-basics-and-architecture.md) — SSD Overview 1+2 통합
5. [NAND Flash Memory — 셀 구조부터 3D NAND까지](05-nand-flash-memory.md) — SSD Hardware 4
6. [Flash Translation Layer — BAST · FAST · Superblock · LAST](06-flash-translation-layer.md) — SSD Software 1+2 (+ annotated copy)
