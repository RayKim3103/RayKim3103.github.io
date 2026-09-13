---
layout: page
title: Operating System
description: >
  Operating System 강의 노트.
hide_description: false
sitemap: false
permalink: /studies/systems/operating-system/
---

원본: [GitHub — Operating System](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Operating_System)

> OSTEP 기반 강의 노트와 xv6-riscv 과제 노트에 실제 값까지 끝까지 추적하는 worked example(scheduling 정책별 turnaround/response 계산, segmentation·paging 주소 변환, FIFO vs LRU page fault 비교, race condition 값 추적, semaphore 상태 추적, HDD I/O 시간·SSTF 이동 거리·RAID 용량 계산, xv6 inode 최대 파일 크기, pipe fd 배정 추적, ticket 정책 추적, worst-fit 비교, zero-frame page fault 절감량, thread stack 주소 배치 등)을 추가해 보강했습니다.

## 강의 노트

1. [01. 운영체제 개요](01-os-overview.md) — `write()` system call 5단계 추적
2. [02. CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md) — FIFO·STCF 4-job turnaround/response 비교
3. [03. 메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md) — segmentation·paging 주소 변환, FIFO vs LRU page fault 비교
4. [04. 멀티스레딩](04-multithreading.md) — counter++ race condition 값 추적, bounded buffer semaphore 추적
5. [05. IO와 디스크 드라이브](05-io-and-disk-drives.md) — HDD I/O 시간·SSTF 이동 거리·RAID capacity 계산
6. [06. 파일 시스템](06-file-systems.md) — xv6 inode 최대 파일 크기(≈268KB) 계산
7. [A1 xv6 Shell 과제](a1-xv6-shell.md) — `ls | wc -l` pipe fd 배정 추적
8. [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md) — ticks 기반 경과 시간 계산
9. [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md) — ticket 감소 정책 3-round 추적
10. [A4 xv6 Free List 과제](a4-xv6-free-list.md) — next-fit vs worst-fit 선택 비교
11. [A5 xv6 Paging 과제](a5-xv6-paging.md) — zero-frame 방식 물리 메모리 절감량 계산
12. [A6 xv6 Thread 과제](a6-xv6-thread.md) — thread stack 주소 배치 계산
