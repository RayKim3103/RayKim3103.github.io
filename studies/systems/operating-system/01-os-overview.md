---
layout: page
title: "01. 운영체제 개요"
permalink: /studies/systems/operating-system/01-os-overview/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/01%20%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%20%EA%B0%9C%EC%9A%94.md)

{% raw %}
tags: #operating-system #virtualization #concurrency #persistence

관련 노트: [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md), [멀티스레딩](04-multithreading.md), [IO와 디스크 드라이브](05-io-and-disk-drives.md), [파일 시스템](06-file-systems.md)

## 핵심 요약

운영체제는 하드웨어를 관리하고 사용자 프로그램이 안전하고 편리하게 실행되도록 서비스를 제공하는 시스템 소프트웨어다. 사용자는 하드웨어를 직접 제어하지 않고, 운영체제가 제공하는 system call과 추상화를 통해 CPU, 메모리, 저장장치, 입출력 장치를 사용한다.

강의 전체는 OSTEP의 세 축으로 구성된다.

- Virtualization: 제한된 CPU와 메모리를 여러 프로그램이 각자 독점하는 것처럼 보이게 한다.
- Concurrency: 여러 실행 흐름이 동시에 진행될 때 correctness를 유지한다.
- Persistence: 전원이 꺼져도 데이터가 남도록 파일 시스템과 저장장치를 관리한다.

## 운영체제의 정의와 역할

운영체제는 computer hardware와 user program 사이의 중간 계층이다. 응용 프로그램은 파일 열기, 프로세스 생성, 메모리 할당, 네트워크/디스크 I/O 같은 작업을 직접 하드웨어 명령으로 수행하지 않고 OS API를 호출한다.

운영체제의 주요 역할은 다음과 같다.

- Hardware resource management: CPU core, memory, disk, I/O device를 관리한다.
- Scheduling: 어떤 프로그램을 언제 실행할지 결정한다.
- Memory management: 각 프로세스에 독립적인 address space를 제공한다.
- I/O management: 장치별 복잡한 protocol을 driver와 kernel interface로 숨긴다.
- File system: 영속 저장장치를 file과 directory 추상화로 제공한다.
- Protection: 프로그램이 서로의 메모리와 커널을 침범하지 못하게 막는다.

## 컴퓨터 시스템 구조

현대 컴퓨터는 Von Neumann 구조를 기반으로 한다. 프로그램과 데이터는 메모리에 저장되고, CPU는 명령어를 메모리에서 가져와 실행한다. CPU, memory, I/O device는 bus 또는 interconnect 계층으로 연결된다.

운영체제는 이 구조 위에서 각 장치의 사용을 조정한다. 예를 들어 CPU는 명령어를 실행하지만 어떤 process의 명령어를 실행할지는 scheduler가 정한다. 메모리는 byte array이지만 각 process가 보는 주소 공간은 virtual memory가 만든다.

## User Program에 제공하는 서비스

운영체제가 제공하는 서비스는 사용자 프로그램 입장에서는 system call로 보인다. 대표적인 서비스는 다음과 같다.

- Program execution: 프로그램을 실행하고 종료한다.
- I/O operation: 파일, 터미널, 장치에 읽기/쓰기를 수행한다.
- File system manipulation: 파일 생성, 삭제, 이름 변경, metadata 조회를 제공한다.
- Communication: process 간 pipe, signal, socket 같은 통신을 제공한다.
- Error detection: 하드웨어 오류, 잘못된 메모리 접근, I/O 실패를 감지한다.
- Resource allocation: CPU 시간, 메모리, 파일 descriptor 등을 분배한다.

System call은 user mode 프로그램이 kernel mode 기능을 요청하는 안전한 진입점이다. 임의의 프로그램이 privileged instruction을 직접 실행하면 시스템 전체가 망가질 수 있으므로, 운영체제는 제한된 interface만 열어둔다.

## Virtualization

Virtualization은 제한된 물리 자원을 여러 프로그램이 동시에 쓰는 것처럼 보이게 하는 기술이다.

CPU virtualization에서는 하나 또는 적은 수의 core 위에서 여러 process가 번갈아 실행된다. OS는 context switch를 통해 한 process의 register state를 저장하고 다른 process의 state를 복원한다. 사용자 입장에서는 여러 프로그램이 동시에 실행되는 것처럼 보인다.

Memory virtualization에서는 각 process가 자신만의 연속적인 address space를 가진 것처럼 보인다. 실제 물리 메모리에는 여러 process의 page가 흩어져 있어도, page table과 MMU가 virtual address를 physical address로 변환한다.

## Concurrency

Concurrency는 여러 실행 흐름이 함께 진행될 때 생기는 문제를 다룬다. Multi-processing은 독립 process들이 동시에 실행되는 구조이고, multi-threading은 하나의 process 내부에서 여러 thread가 같은 address space를 공유하며 실행되는 구조다.

Thread들은 heap과 global data를 공유하므로 race condition이 쉽게 생긴다. 예를 들어 두 thread가 동시에 shared counter를 증가시키면 load, add, store의 중간 순서가 섞여 결과가 틀어질 수 있다. 이를 막기 위해 lock, condition variable, semaphore 같은 synchronization primitive를 사용한다.

## Persistence

Persistence는 데이터가 프로그램 종료나 전원 차단 이후에도 남도록 만드는 성질이다. 운영체제는 저장장치를 file과 directory라는 추상화로 제공한다.

File system은 inode, directory entry, block allocation, free space management를 사용해 파일 이름과 실제 디스크 블록을 연결한다. 또한 write buffering과 crash consistency 문제를 다루어야 한다. `write()`가 반환되었다고 항상 데이터가 즉시 물리 디스크에 기록된 것은 아니며, `fsync()` 같은 호출이 persistence 보장을 강화한다.

## xv6와 실습의 위치

이 과목의 과제들은 xv6-riscv를 사용해 운영체제 내부를 직접 수정하게 한다. xv6는 교육용 Unix 계열 운영체제이며, 프로세스, system call, scheduler, memory allocator, page fault, thread 같은 개념을 실제 kernel code와 연결해 볼 수 있다.

강의 개념과 과제 연결:

- Process와 system call: [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md)
- Scheduling: [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md)
- Free list와 malloc: [A4 xv6 Free List 과제](a4-xv6-free-list.md)
- Paging과 page fault: [A5 xv6 Paging 과제](a5-xv6-paging.md)
- Thread와 synchronization: [A6 xv6 Thread 과제](a6-xv6-thread.md)
- Shell과 fork/exec/wait: [A1 xv6 Shell 과제](a1-xv6-shell.md)

## 복습 체크포인트

- 운영체제를 hardware manager와 abstraction provider 관점에서 설명할 수 있는가?
- Virtualization, concurrency, persistence가 각각 어떤 문제를 다루는가?
- User mode와 kernel mode를 분리하는 이유를 말할 수 있는가?
- System call이 일반 함수 호출과 어떻게 다른가?
- CPU virtualization과 memory virtualization의 공통점과 차이를 설명할 수 있는가?

{% endraw %}

---

다음: [02. CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md)
