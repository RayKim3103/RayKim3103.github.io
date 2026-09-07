---
layout: page
title: "02. CPU 가상화 - 프로세스와 스케줄링"
permalink: /studies/systems/operating-system/02-cpu-virtualization-processes-and-scheduling/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/02%20CPU%20%EA%B0%80%EC%83%81%ED%99%94%20-%20%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81.md)

{% raw %}
tags: #operating-system #process #scheduling #system-call #context-switch

관련 노트: [운영체제 개요](01-os-overview.md), [A1 xv6 Shell 과제](a1-xv6-shell.md), [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md), [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md)

## 핵심 요약

CPU 가상화는 제한된 CPU를 여러 process가 동시에 사용하는 것처럼 보이게 한다. 운영체제는 process abstraction, process API, context switching, scheduling policy를 통해 이를 구현한다. 강의는 process의 정의와 상태, POSIX API, fork/exec/wait/open/dup/pipe, limited direct execution, scheduling metric과 policy, MLFQ까지 이어진다.

핵심 질문은 두 가지다.

- 어떻게 user program이 CPU를 직접 쓰는 듯 실행되면서도 OS가 제어권을 잃지 않는가?
- 여러 runnable process 중 다음에 실행할 process를 어떤 기준으로 고르는가?

## Process의 의미

Process는 실행 중인 program이다. Program은 디스크에 저장된 정적 파일이고, process는 그 program이 메모리에 올라와 register, stack, heap, open file, scheduling state를 가진 실행 단위다.

CPU virtualization에서 single-core CPU도 time-sharing으로 여러 process를 번갈아 실행한다. OS는 짧은 시간 단위로 CPU를 넘겨주고 회수하면서 illusion of many CPUs를 만든다.

## Process API

운영체제는 process를 다루는 API를 제공한다.

- Create: 새 process를 만든다.
- Destroy: process를 종료한다.
- Wait: parent가 child 종료를 기다린다.
- Miscellaneous control: process를 중지하거나 재개한다.
- Status: process state나 PID 같은 정보를 조회한다.

Terminal에서는 `ps`로 process 목록과 state를 확인할 수 있고, `kill` 등으로 process에 signal을 보낼 수 있다.

## Process 생성과 주소 공간

Process creation은 program code와 data를 memory로 적재하고, stack과 heap 등 실행에 필요한 address space를 구성한 뒤 CPU 제어권을 새 process로 넘기는 과정이다. 운영체제는 process마다 PCB(Process Control Block)를 유지한다.

PCB에는 보통 다음 정보가 들어간다.

- PID와 parent PID
- Process state
- Register context
- Address space와 page table 정보
- Open file table
- Scheduling 관련 정보

## Process State

기본 상태는 다음과 같이 이해할 수 있다.

- New: process를 생성 중이다.
- Ready 또는 runnable: 실행 준비가 되었지만 CPU를 기다린다.
- Running: CPU에서 실행 중이다.
- Blocked 또는 sleeping: I/O나 event를 기다린다.
- Zombie: 실행은 끝났지만 parent가 종료 상태를 수거하지 않았다.

I/O를 요청하면 process는 running에서 blocked로 이동하고, I/O 완료 interrupt가 오면 runnable로 돌아온다. Scheduler는 runnable process 중 하나를 running으로 선택한다.

## Context Switching

Context switch는 CPU 제어권을 한 process에서 다른 process로 넘기는 동작이다. OS는 현재 process의 register state를 저장하고, 다음 process의 register state를 복원한다. 이 과정은 trap, interrupt, scheduler code, low-level switch routine을 포함한다.

Context switch는 CPU virtualization의 핵심이지만 overhead도 있다. 너무 자주 switch하면 응답성은 좋아질 수 있지만 실제 계산에 쓰는 시간이 줄어든다.

## POSIX Process API

### fork()

`fork()`는 현재 process를 복제해 child process를 만든다. child는 parent와 같은 program counter 근처에서 실행을 계속하지만, 반환값으로 parent/child를 구분한다.

- Parent: `fork()`가 child PID를 반환한다.
- Child: `fork()`가 0을 반환한다.
- 실패: 음수를 반환한다.

### wait()

`wait()`는 parent가 child process의 종료를 기다리게 한다. Child가 먼저 종료되면 zombie가 되었다가 parent의 `wait()`로 정리된다.

### exec()

`exec()`는 현재 process image를 다른 program으로 덮어쓴다. PID는 유지될 수 있지만 code, data, stack 등 address space가 새 program으로 바뀐다. Shell은 보통 `fork()`로 child를 만들고, child에서 `exec()`로 명령을 실행하며, parent는 foreground command에 대해 `wait()`한다.

### open(), close(), dup(), pipe()

File descriptor는 process가 열린 파일이나 device를 참조하는 정수 handle이다. `open()`은 file descriptor를 만들고, `close()`는 닫는다. `dup()` 또는 `dup2()`는 descriptor를 복제해 redirection을 구현할 수 있다.

`pipe()`는 두 file descriptor를 만들어 한 process의 output을 다른 process의 input으로 연결한다. Shell pipeline은 `fork()`, `pipe()`, `dup()`, `exec()`, `wait()`가 함께 쓰이는 대표 사례다.

## Limited Direct Execution

OS는 program을 CPU에서 직접 실행하게 해야 성능이 좋다. 그러나 user program이 CPU를 완전히 장악하면 OS가 보호와 scheduling을 할 수 없다. Limited direct execution은 대부분의 시간에는 user code가 직접 CPU에서 실행되게 하되, privileged operation과 timer interrupt에서는 kernel이 제어권을 회수하는 방식이다.

핵심 구성:

- User mode: 제한된 명령만 실행한다.
- Kernel mode: privileged instruction과 장치 제어가 가능하다.
- Trap: system call 또는 exception으로 kernel에 진입한다.
- Timer interrupt: OS가 주기적으로 CPU 제어권을 되찾는다.

## Scheduling Metric

Scheduling policy를 평가하려면 metric이 필요하다.

- Turnaround time: job 도착부터 완료까지 걸린 시간
- Response time: job 도착부터 처음 실행될 때까지 걸린 시간
- Fairness: CPU 시간을 얼마나 공정하게 나누는가
- Throughput: 단위 시간당 완료한 job 수

서로 다른 metric은 충돌할 수 있다. 예를 들어 turnaround time을 최적화하면 짧은 job을 우선하게 되고, response time을 최적화하면 time slicing이 필요해진다.

## Scheduling Policy

### FIFO

First-In, First-Out은 먼저 온 job을 먼저 끝까지 실행한다. 단순하지만 긴 job이 앞에 있으면 짧은 job들이 오래 기다리는 convoy effect가 생긴다.

### SJF

Shortest Job First는 실행 시간이 가장 짧은 job을 먼저 실행해 평균 turnaround time을 줄인다. 그러나 job runtime을 미리 알아야 한다는 비현실적인 가정이 있다.

### STCF

Shortest Time-to-Completion First는 새 job이 도착할 때 남은 시간이 가장 짧은 job을 실행한다. Preemptive SJF로 볼 수 있으며, 짧은 job의 응답과 완료를 빠르게 만든다.

### Round Robin

Round Robin은 각 process에 time slice를 주고 순환 실행한다. Response time이 좋아지지만 time slice가 너무 짧으면 context switch overhead가 커지고, 너무 길면 FIFO에 가까워진다.

## I/O를 포함한 Scheduling

Process는 CPU만 쓰지 않고 I/O를 기다리기도 한다. I/O-bound job은 자주 block되고, CPU-bound job은 긴 시간 CPU를 쓴다. Scheduler는 I/O가 끝난 job을 적절히 다시 실행해 CPU와 I/O device가 동시에 바쁘게 유지되도록 해야 한다.

## Multi-Level Feedback Queue

MLFQ는 job runtime을 미리 모르는 상황에서 interactive job에 좋은 response time을 주기 위한 정책이다. 여러 priority queue를 두고, process의 행동에 따라 priority를 조정한다.

일반 원리:

- 높은 priority queue가 먼저 실행된다.
- 같은 priority에서는 Round Robin을 쓴다.
- CPU를 time slice 끝까지 쓰면 priority를 낮춘다.
- I/O로 자주 양보하는 job은 높은 priority에 머물 수 있다.
- Starvation 방지를 위해 주기적으로 priority boost를 수행한다.

## 복습 체크포인트

- Process와 program의 차이를 설명할 수 있는가?
- `fork()`, `wait()`, `exec()`가 shell 실행 흐름에서 어떻게 연결되는가?
- Context switch에서 저장하고 복원해야 하는 정보는 무엇인가?
- FIFO, SJF, STCF, RR의 장단점을 metric 관점에서 비교할 수 있는가?
- MLFQ가 job runtime을 모르는 상황에서 어떤 heuristic을 쓰는지 설명할 수 있는가?

{% endraw %}

---

이전: [01. 운영체제 개요](01-os-overview.md) · 다음: [03. 메모리 가상화 - 세그먼테이션과 페이징](03-memory-virtualization-segmentation-and-paging.md)
