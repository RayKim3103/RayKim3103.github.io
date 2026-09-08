---
layout: page
title: "A3 xv6 Scheduling 과제"
permalink: /studies/systems/operating-system/a3-xv6-scheduling/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A3%20xv6%20Scheduling%20%EA%B3%BC%EC%A0%9C.md)

{% raw %}
tags: #operating-system #xv6 #scheduler #lottery-scheduling #context-switch

관련 노트: [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md)

## 과제 목표

이 과제는 xv6-riscv scheduler를 수정해 ticket 기반 scheduling을 구현하는 것이다. 기본 xv6 scheduler의 단순 round-robin 흐름을 이해하고, process별 ticket 수를 바탕으로 CPU 배분을 조정한다.

## xv6 CPU 실행 흐름

xv6에서 CPU scheduler는 runnable process를 고르고 `swtch()`를 호출해 scheduler context에서 process context로 전환한다. Process는 kernel stack을 사용해 kernel mode에서 준비를 마친 뒤 trampoline을 거쳐 user mode로 돌아간다. Timer interrupt, syscall, exception 등이 발생하면 다시 kernel로 들어오고 scheduler가 다음 process를 선택한다.

이 흐름은 [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md)의 context switching을 실제 xv6 code와 연결한다.

## Ticket 기반 정책

과제 설명의 핵심은 process가 CPU time slice를 어떻게 사용했는지에 따라 ticket을 조정하는 것이다.

- Process가 syscall 없이 time slice 전체를 사용하면 ticket을 잃는다.
- Process가 I/O나 syscall 등으로 CPU를 일찍 양보하면 ticket 유지 또는 다른 정책을 적용한다.
- Scheduler는 ticket 정보를 사용해 runnable process 중 다음 실행 대상을 고른다.

Ticket은 CPU 사용량을 조정하는 priority-like 값으로 생각할 수 있다. CPU-bound process가 계속 slice를 꽉 채우면 점차 불리해지고, interactive/I/O-bound process가 상대적으로 유리해진다.

## 구현 위치

주요 확인 파일은 xv6 kernel의 process와 scheduler 관련 코드다.

- `kernel/proc.c`: `scheduler()`, `yield()`, `sleep()`, `wakeup()` 등
- `kernel/proc.h`: `struct proc`에 scheduling field 추가
- `kernel/trap.c`: timer interrupt와 yield 흐름 확인
- user test program: scheduler 동작 검증

Process가 time slice 전체를 사용했는지 기록하려면 timer interrupt와 syscall/yield 경로를 구분해야 한다.

## Process 상태와 Lock

Scheduler는 `proc[]`을 순회하며 `RUNNABLE` process를 찾는다. Process state 변경은 race가 생기기 쉬우므로 `p->lock`을 잡고 상태를 읽고 변경해야 한다.

주의할 점:

- Runnable process만 scheduling 후보가 된다.
- Process lock 획득/해제 순서를 xv6 기존 패턴과 맞춘다.
- `swtch()` 전후 lock ownership이 어떻게 유지되는지 확인한다.
- Sleep/wakeup 경로와 scheduler 경로가 state를 동시에 건드릴 수 있다.

## Validation Case 해석

과제 PDF는 여러 실행 시간이 다른 concurrent process와 `forktest`류 검증을 언급한다. 검증은 특정 예시만 통과하는 것이 아니라 비슷한 common-sense 상황에서도 동작해야 한다.

검증 관점:

- Process가 많아도 scheduler가 panic 없이 동작한다.
- 서로 다른 실행 시간 process들이 ticket 정책에 맞게 CPU를 받는다.
- Time slice를 모두 사용한 process의 ticket 감소가 정확하다.
- 종료 process, sleeping process, runnable process가 올바르게 구분된다.

## 실수하기 쉬운 부분

- Timer interrupt가 발생했다고 모든 process의 ticket을 바꾸는 문제
- Syscall로 kernel에 들어온 경우와 time slice 만료를 구분하지 않는 문제
- Process lock 없이 `state`나 scheduling field를 읽고 쓰는 문제
- Ticket이 음수가 되거나 0이 되어 영원히 실행되지 않는 문제
- Debug print를 남겨 grading output format을 깨는 문제

## 복습 체크포인트

- xv6 scheduler가 `swtch()`를 호출하는 위치와 의미를 설명할 수 있는가?
- Timer interrupt와 voluntary yield가 scheduling policy에 다르게 반영되는 이유는 무엇인가?
- `struct proc`에 새 scheduling field를 추가할 때 초기화 위치를 찾을 수 있는가?
- Lock을 잡은 상태로 context switch하는 xv6의 관례를 이해했는가?

{% endraw %}

---

이전: [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md) · 다음: [A4 xv6 Free List 과제](a4-xv6-free-list.md)
