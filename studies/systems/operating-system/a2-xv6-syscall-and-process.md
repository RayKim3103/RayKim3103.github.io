---
layout: page
title: "A2 xv6 System Call and Process 과제"
permalink: /studies/systems/operating-system/a2-xv6-syscall-and-process/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A2%20xv6%20System%20Call%20and%20Process%20%EA%B3%BC%EC%A0%9C.md)

{% raw %}
tags: #operating-system #xv6 #system-call #process

관련 노트: [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [A1 xv6 Shell 과제](a1-xv6-shell.md), [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md)

## 과제 목표

이 과제는 xv6-riscv에 process 정보를 조회하는 새 system call을 구현하고, user program `ps`가 process table을 출력하도록 만드는 것이다. Linux의 `ps`가 파일 시스템의 proc 정보를 읽는 것과 달리, 이 과제에서는 xv6 kernel 내부의 process table을 system call로 직접 탐색한다.

## 핵심 개념

과제는 user program에서 kernel system call까지 이어지는 경로를 이해하게 한다.

- User program이 wrapper function을 호출한다.
- RISC-V register에 system call number와 argument가 들어간다.
- Trap을 통해 kernel mode로 진입한다.
- `syscall()` dispatch table이 handler를 찾는다.
- Kernel handler가 argument를 읽고 process table을 탐색한다.
- 결과를 출력하거나 user space로 복사한다.

## pstate() System Call

Skeleton에는 `pstate(int, ...)` 형태의 syscall interface가 준비되어 있다. xv6-riscv는 system call 인자를 최대 6개까지 register로 전달할 수 있다. `ps` program은 command-line option을 parsing해 `pstate()`에 조건을 전달해야 한다.

`pstate()`가 수집할 process 정보:

- PID
- PPID
- Process state
- Elapsed runtime 또는 ticks 기반 시간
- Process name 등 skeleton에서 요구하는 정보

## xv6 System Call 구현 위치

`getpid()` 같은 기존 syscall을 따라가면 구현 위치를 이해할 수 있다.

- `user/user.h`: user-level prototype
- `user/usys.pl`: user stub 생성
- `kernel/syscall.h`: syscall number
- `kernel/syscall.c`: syscall table과 extern declaration
- `kernel/sysproc.c`: 실제 kernel handler

Skeleton에서 일부 연결은 이미 되어 있을 수 있으므로, handler 내부의 기능과 argument 처리에 집중한다.

## Process Table과 State

xv6의 process는 `struct proc`로 표현되고, process table은 `proc[]` 배열이다. Process state는 보통 다음 enum으로 정의된다.

- `UNUSED`: 비어 있는 process slot
- `USED`: 생성 중인 상태
- `SLEEPING`: event를 기다리는 상태
- `RUNNABLE`: scheduler가 실행할 수 있는 상태
- `RUNNING`: CPU에서 실행 중인 상태
- `ZOMBIE`: 종료되었지만 parent가 아직 wait하지 않은 상태

출력 대상에서는 `UNUSED`와 관찰이 어려운 중간 상태를 어떻게 다룰지 명세를 따라야 한다.

## ticks와 시간 정보

xv6에는 timer interrupt마다 증가하는 global variable `ticks`가 있다. 과제 설명에서는 `ticks`가 100ms 단위로 증가한다고 설명한다. Process runtime이나 elapsed time을 보여주려면 process 생성 시점과 현재 ticks 차이를 사용한다.

Kernel global variable에 접근할 때는 lock이 필요한지 확인해야 한다. Process table을 순회할 때도 process lock을 적절히 잡아 inconsistent state를 읽지 않도록 해야 한다.

## ps Option 처리

`ps`는 pid 또는 state option을 받을 수 있다. 예를 들어 특정 PID만 출력하거나, `S`, `R`, `X`, `Z` 같은 state 조건에 맞는 process만 출력한다.

중요한 해석:

- 여러 option은 OR 조건으로 처리될 수 있다.
- 숫자는 PID 조건으로 해석한다.
- 문자 option은 process state 조건으로 해석한다.
- invalid option이나 너무 많은 argument는 help/error 처리한다.

## Argument 전달

User program의 `argc`, `argv`는 command-line 인자를 담는다. 이를 syscall에 넘기기 위해 `ps`는 option을 integer encoding으로 변환한다. Kernel handler에서는 `argint()` 등으로 register에 담긴 인자를 가져온다.

System call argument 수가 고정되어 있다면 실제 option 개수를 알리는 marker 또는 count가 필요하다. Skeleton은 최대 6개 인자를 넘기는 형태라, 마지막 인자 처리 조건을 정확히 해석해야 한다.

## 검증 포인트

- `ps`가 header와 process 정보를 정해진 format으로 출력한다.
- PID option이 해당 process만 출력한다.
- State option이 해당 상태 process를 출력한다.
- 여러 option을 조합했을 때 조건을 OR로 처리한다.
- 잘못된 option에 대해 help 또는 error가 나온다.
- 불필요한 debug print가 없다.

## 실수하기 쉬운 부분

- Kernel에서 user pointer를 직접 신뢰하는 문제
- Process table 순회 중 lock을 잡지 않아 race가 생기는 문제
- `UNUSED` slot을 출력하는 문제
- `RUNNING` 상태가 single CPU 기준에서만 관찰되는 점을 놓치는 문제
- `argc`와 syscall 인자 수를 혼동하는 문제

{% endraw %}

---

이전: [A1 xv6 Shell 과제](a1-xv6-shell.md) · 다음: [A3 xv6 Scheduling 과제](a3-xv6-scheduling.md)
